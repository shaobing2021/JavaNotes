#### SpringApplication

此方法主要分为两部分：实例化SpringApplication，执行run方法

主要做了四件事情：

1.判断是普通项目还是web项目，web项目则一直运行

2.查找并加载所有可用初始化器，设置到initializers属性

3.找出所有的应用程序监听器，设置到listeners

4.推断并设置main方法的定义类，找到运行的主类