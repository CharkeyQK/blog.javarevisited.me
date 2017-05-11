---
layout:     post
title:      "Auto-deployment with Git@OSC Webhook"
subtitle:   " \"Automatic instead Manual\""
date:       2017-05-11 21:45:00
author:     "Charkey"
header-img: "img/tag-bg.jpg"
catalog: true
tags:
    - Git
    - Webhook
    - 码云
---

> “Automatic instead Manual”

## 本文写什么？

有个静态主页托管在码云 Git@OSC，部署在 Linode 主机。鉴于静态主页还在修改完善，如果每次都 修改页面 -> 登陆主机 -> 拉取代码 -> 部署，不免麻烦了。

> Git@OSC 钩子功能（callback），是帮助用户 push 了代码后，自动回调一个您设定的 http 地址。 这是一个通用的解决方案，用户可以自己根据不同的需求，来编写自己的脚本程序（比如发邮件，自动部署等）

码云平台除了提供私有的仓库托管，也支持 webhook 钩子和部署公钥。因此可以利用这些特性来完成一些重复性工作，比如触发 CI 工具的运行、自动部署项目线上服务器等等。

## 码云项目自动化部署配置

### 部署公钥管理

> 部署公钥允许以只读的方式访问项目，主要用于项目在生产服务器的部署上，免去HTTP方式每次操作都要输入密码和普通SSH方式担心不小心修改项目代码的麻烦。

请参考 [如何生成ssh公钥](http://git.mydoc.io/?t=154712) 来生成 sshkey，并将 public key 添加到 Git@OSC 的部署公钥。添加部署公钥完成后，在 Linux 终端输入 *ssh -T git@git.oschina.net* 如果返回 *Welcome to Git@OSC, yourname!* 则表示部署公钥添加成功。此时通过 SSH 的方式拉取代码就不需要在输入用户名密码了。

![Repo management entrance](/img/in-post/auto-deployment-with-git@osc-webhook/repo_management_entrance.jpg)

![Repo add deploy keys entrance](/img/in-post/auto-deployment-with-git@osc-webhook/repo_add_deploy_keys_entrance.jpg)

![Repo add deploy keys](/img/in-post/auto-deployment-with-git@osc-webhook/repo_add_deploy_keys.jpg)

### Webhook 设置

首先，需要有能够接收 Git@OSC Webhook POST 请求回调的服务，简单起见，我直接使用 Servlet 跑在 Linode 主机上，接收到回调之后使用 Jsch 执行相应的 Linux 指令。

同时，需要在码云平台把回调地址配置上去，勾选需要的触发方式，提交即可。

![Repo webhook setting](/img/in-post/auto-deployment-with-git@osc-webhook/repo_webhook_setting.jpg)

### 服务器环境预配置

本文演示的是静态主页的自动化部署，服务器运行的是 Nginx。当项目文件更新时，拉取更新下来就可以了。

需要在服务器预先做好的事情就是在 nginx 主机 www 目录下把项目文件预先 clone 下来，后续只要在这个目录中通过 *Git pull* 更新即可。

## Sample Code

```java
public class WebHookExecutor {

    public void pull() {
        try {
            String username = "username";
            String password = "password";
            String host = "host_for_server";
            int port = 22;

            JSch jSch = new JSch();
            Session session = jSch.getSession(username, host, port);
            session.setPassword(password);

            Properties config = new Properties();
            // 当第一次连接服务器时，自动接受新的公钥
            config.put("StrictHostKeyChecking", "no");
            // compression
            config.put("compression.s2c", "zlib@openssh.com,zlib,none");
            config.put("compression.c2s", "zlib@openssh.com,zlib,none");
            config.put("compression_level", "9");

            session.setConfig(config);

            session.connect();

            CommandExecutor executor = new DefaultCommandExecutor(session);

            //exec("ls", "UTF-8", session);
            exec("cd /data/nginx/www/websitedir && git pull", "UTF-8", session);

            session.disconnect();
        } catch (JSchException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public static void exec(String command, String charset, Session session) throws JSchException, IOException {
        ChannelExec channelExec = (ChannelExec) session.openChannel("exec");
        channelExec.setCommand(command);
        channelExec.setInputStream(null);
        channelExec.setErrStream(System.err);
        channelExec.connect();
        InputStream in = channelExec.getInputStream();
        BufferedReader reader = new BufferedReader(new InputStreamReader(in, Charset.forName(charset)));
        String buf = null;
        while ((buf = reader.readLine()) != null) {
            System.out.println(buf);
        }
        reader.close();
        channelExec.disconnect();
    }
}
```

另外，也可以尝试使用 Node.js 来接收 Webhook 的请求，可以参考 [Webhook 实践 —— 自动部署](https://segmentfault.com/a/1190000003908244)