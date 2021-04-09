# spring cloud与spring boot版本选型

我们知道spring cloud是一个技术架构的整合，俗称spring全家桶，里面每个服务都是用spring boot搭建起来的，所以这里就存在一个问题，那么spring cloud的版本与spring boot的版本怎么选呢？如果此时的你，正好对这个问题比较疑惑，希望我的这篇文章能帮助到你。

访问spring cloud官网（https://spring.io/projects/spring-cloud），往下翻找到如下图所示内容：

![image-20201120165957220](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20201120165957220.png)

以图中标注为例进行说明，Greenwich是指spring cloud的版本（cloud的版本是以伦敦地铁站的站名进行命名的），2.1.x是指spring boot的版本，也是说可以选大于2.1且小于2.2的版本，如果你不按照官网的规定进行选择好合适的版本，后面的开发会很容易遇到环境方面的问题，所以需要谨慎。