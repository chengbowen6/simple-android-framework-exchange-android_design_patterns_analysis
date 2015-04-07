# Android源码设计模式分析开源项目

## 提示
**该开源库已经停止维护**，该系列文章我们会根据技术发展、实战需求以及读者您的反馈重写所有章节，并且加入更加深入的核心机制分析以及模式在Android开发中的实战，**完善后的文稿将会编撰成书出版**，以便帮助大家更系统的学习，此时更真心地希望大家能够给出完善建议，以便本书更贴近您的需求，联系邮箱为 : simplecoder.h@gmail.com，谢谢。


## 简述
该项目通过分析Android系统中的设计模式来提升大家对设计模式的理解，从源码的角度来剖析既增加了对Android系统本身的了解，也从优秀的设计中领悟模式的实际运用以及它适用的场景，避免在实际开发中的生搬硬套。如果你对面向对象的六大开发原则还不太熟悉，那么在学习模式之前先学习一下[面向对象的六大原则](oop-principles/oop-principles.md)是非常有必要的。

<b id="schedule"></b>
## 任务表 ( 一期截止 2015.3.20 )
| 	模式名 		 | 		分析者    |     状态      |
| ------------- | ------------- |--------------|
|    [单例模式](singleton/mr.simple)   	 | [Mr.Simple](https://github.com/bboyfeiyu)|   完成  |
|    [Builder模式](builder/mr.simple)  	 | [Mr.Simple](https://github.com/bboyfeiyu)|   完成  |
|    [外观模式](facade/elsdnwn)   	     | [elsdnwn](https://github.com/elsdnwn)、[Mr.Simple](https://github.com/bboyfeiyu)|   完成  |
|    [模板方法](template-method/mr.simple)   | [Mr.Simple](https://github.com/bboyfeiyu) | 完成  |
|    [适配器模式](adapter/mr.simple)     | [Mr.Simple](https://github.com/bboyfeiyu) | 完成  |
|    [观察者模式](observer/mr.simple)    | [Mr.Simple](https://github.com/bboyfeiyu) |  完成 |
|    [策略模式](strategy/gkerison)      | [GKerison](https://github.com/GKerison) | 完成 |
|    [代理模式](proxy/singwhatiwanna)   | [singwhatiwanna](https://github.com/singwhatiwanna) |  完成  |
|    [迭代器模式](iterator/haoxiqiang) | [Haoxiqiang](https://github.com/Haoxiqiang)|  完成 |
|    [责任链模式](chain-of-responsibility/AigeStudio) | [AigeStudio](https://github.com/AigeStudio)|  完成  |
|    [命令模式](command/lijunhuayc) | [lijunhuayc](https://github.com/lijunhuayc)|  完成  |
|    [桥接模式](bridge/shen0834) | [shen0834](https://github.com/shen0834)|  完成  |
|    [原型模式](prototype/mr.simple) | [Mr.Simple](https://github.com/bboyfeiyu)|  完成  |

## 参考资料
* [GOF的设计模式：可复用面向对象软件的基础](http://item.jd.com/10057319.html)
* [设计模式之禅](http://item.jd.com/11414555.html)
* [Java与模式](http://item.jd.com/10094286.html)
* [java-design-patterns](https://github.com/iluwatar/java-design-patterns)
* [Java之美[从菜鸟到高手演变]之设计模式](http://blog.csdn.net/zhangerqing/article/details/8194653)
