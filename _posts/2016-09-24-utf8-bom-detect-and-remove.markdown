---
layout:     post
title:      "UTF-8 BOM detect and remove"
subtitle:   " \"UTF-8 with BOM & UTF-8\""
date:       2016-09-24 15:45:00
author:     "Charkey"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - UTF-8
    - BOM
    - Java
---

> “UTF-8 with BOM & UTF-8”

## 本文写什么？

最近刚好有个小任务，需要批量重命名和处理文件内容，遇到了UTF-8 编码中 BOM 的问题。

最简单、重复的做法是一个一个文件手动重命名、使用文本编辑器如 Sublime 打开然后 Save with Encoding: UTF-8。

然而，程序员可以写点代码偷点懒。

## 使用 NIO 处理 BOM

UTF-8 编码中 BOM 的前三个字节是 **EF BB BF**

|Bytes|Encoding Form|
|-----|-------------|
|00 00 FE FF|UTF-32, big-endian|
|FF FE 00 00|UTF-32, little-endian|
|FE FF|UTF-16, big-endian|
|FF FE|UTF-16, little-endian|
|EF BB BF|UTF-8|

Unicode相关知识的详细介绍请参考 [UTF-8, UTF-16, UTF-32 & BOM](http://unicode.org/faq/utf_bom.html)

下面是使用 WinHex 查看的情况：

![UTF-8 with BOM](\img\in-post\utf8-bom-detect-and-remove\WinHex-UTF-8-with-BOM.jpg)
![UTF-8](\img\in-post\utf8-bom-detect-and-remove\WinHex-UTF-8.jpg)

在 NIO 库中，所有数据都是用缓冲区处理的。在读取数据时，它是直接读到缓冲区中的。在写入数据时，它是写入到缓冲区中的。

最常用的缓冲区类型是 **ByteBuffer**。 一个 ByteBuffer 可以在其底层字节数组上进行 get/set 操作(即字节的获取和设置)。

这边处理 BOM 想利用的是 Buffer 的 **position** 属性，position 变量跟踪了向缓冲区中写入了多少数据或者从缓冲区读取了多少数据。在读取了数据到 Buffer 后，判断数据的头三个字节是否分别是“-17”、“-69”、“-65”，然后通过 **Buffer.position(newPosition)** *newPosition=3* 跳过这三个字节即可。

## Show code

[Check on Github](https://github.com/CharkeyQK/AlgorithmDataStructure/blob/master/src/cn/simastudio/charkey/programming/RenameAndRemoveBOMBatch.java)

```java
public class RenameAndRemoveBOMBatch {

    private static final String PROPS_DIR_PATH = "C:\\Users\\Qikai\\Desktop\\Properties_dir";

    private static final String NEW_FILE_SUFFIX = "_en.properties";

    private static boolean BOM_HEADER_READ = false;

    public static void main(String[] args) throws IOException {

        File propsDir = new File(PROPS_DIR_PATH);
        if (propsDir.isDirectory()) {
            File[] propsFileList = propsDir.listFiles();

            if (propsFileList == null) return;

            for (File aFile : propsFileList) {
                String oldName = aFile.getName();
                // skip files processed
                if (oldName.endsWith(NEW_FILE_SUFFIX)) {
                    continue;
                }
                // concat new file name
                String newName = oldName.substring(0, oldName.indexOf(".")).concat(NEW_FILE_SUFFIX);

                FileInputStream fin = new FileInputStream(PROPS_DIR_PATH + File.separator + oldName);
                FileOutputStream fout = new FileOutputStream(PROPS_DIR_PATH + File.separator + newName);
                FileChannel fcin = fin.getChannel();
                FileChannel fcout = fout.getChannel();
                // fast copy
                ByteBuffer buffer = ByteBuffer.allocateDirect(1024);

                while (true) {
                    buffer.clear();
                    // read
                    int r = fcin.read(buffer);
                    // read end
                    if (r == -1) {
                        break;
                    }

                    // flips this buffer
                    buffer.flip();

                    // deal with BOM
                    if (!BOM_HEADER_READ) {
                        BOM_HEADER_READ = true;
                        // the first three bytes of encoding UTF-8 with BOM
                        if (buffer.get(0) == -17 && buffer.get(1) == -69 && buffer.get(2) == -65) {
                            // skip first three bytes
                            buffer.position(3);
                        }
                    }

                    // UTF-8 output
                    Charset utf8 = Charset.forName("UTF-8");
                    CharsetDecoder decoder = utf8.newDecoder();
                    CharsetEncoder encoder = utf8.newEncoder();
                    CharBuffer cb = decoder.decode(buffer);
                    ByteBuffer outputData = encoder.encode(cb);
                    fcout.write(outputData);
                }
                // reset flag
                BOM_HEADER_READ = false;

                // wind up
                fin.close();
                fout.close();
            }
        }
    }

}
```
