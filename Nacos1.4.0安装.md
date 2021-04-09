# Nacos1.4.0安装

1. ##### 下载

   访问nacos官网https://nacos.io/zh-cn/index.html

   ![image-20201231153732765](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20201231153732765.png)

   点击"V1.4.0 版本说明"，进入githup，选择1.4.0进行下载，我这里下载的是1.4.0版本，当前最新的版本是2.0.0预发布版本

   ![image-20201231154405951](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20201231154405951.png)

2. ##### 安装

   解压nacos-server-1.4.0.zip，打开nacos-server-1.4.0目录

   ![image-20201231154713002](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20201231154713002.png)

3. ##### 启动

   打开bin目录，在命令窗口执行startup.cmd -m standalone，这句话的意思是以单机模式启动nacos，nacos是支持集群模式的，如果不指定单机或集群模式，直接点击startup.cmd进行启动会报错。

   ![image-20201231155228251](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20201231155228251.png)

   

   如果结尾输出：“Nacos started successfully in stand alone mode. use embedded storage”代表启动成功了，如果启动报错的话，参考我另外一篇博客https://blog.csdn.net/weixin_35944810/article/details/111149844

   

   ##### 4.访问控制台

   输入http://localhost:8848/nacos/index.html进行登录，nacos默认端口是8848

   ![image-20201231160012286](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20201231160012286.png)

   

   用户名、密码默认为：nacos，输入登录进去

   ![image-20201231160050728](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20201231160050728.png)

   

   恭喜你成功安装并启动成功！！！如果你觉得很赞，就给我点个赞吧，O(∩_∩)O哈哈~

​		

### 







