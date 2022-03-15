# 新版idea总是报xx程序包不存在实际上包已导入



![image-20210422193245258](C:\Users\zrulin\AppData\Roaming\Typora\typora-user-images\image-20210422193245258.png)

使用IDEA写Java工程时，使用Maven导入依赖包，程序写好后，代码没有报错，但是执行时就会报图中的错误。

如何解决？
网上找了很多解决方法，都没有解决问题。本人是使用IDEA的新手，百度查了很多资料，我的问题已经解决。

1.删除工程目录下的 .iml 文件，删除之前可以看下文件内容；

![image-20210422193310138](C:\Users\zrulin\AppData\Roaming\Typora\typora-user-images\image-20210422193310138.png)

2.打开命令行或者IDEA底部Terminal窗口，将目录调整到工程目录下，执行 mvn idea:module

![image-20210422193326889](C:\Users\zrulin\AppData\Roaming\Typora\typora-user-images\image-20210422193326889.png)

3.重新生成 .iml 文件，重新生成之后再看下文件内容，是不是多了很多东西；
4.IDEA菜单选择 Rebuild Project (可选操作)；

执行程序，问题解决；