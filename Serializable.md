## 什么是序列化，Java是如何实现序列化的
> * 序列化是把对象的状态信息转化为可存储或传输的形式过程，也就是把对象转化为字节序列
的过程称为对象的序列化
* 反序列化是序列化的逆向过程，把字节数组反序列化为对象，把字节序列恢复为对象的过程
成为对象的反序列化

> JDK 提供了 Java 对象的序列化方式实现对象序列化传输，主要通过输出流java.io.ObjectOutputStream和对象输入流java.io.ObjectInputStream来实现。
* java.io.ObjectOutputStream：表示对象输出流 , 它的 writeObject(Object obj)方法可以对参
数指定的 obj 对象进行序列化，把得到的字节序列写到一个目标输出流中。
* java.io.ObjectInputStream：表示对象输入流 ,它的 readObject()方法源输入流中读取字节序
列，再把它们反序列化成为一个对象，并将其返回
需要注意的是，被序列化的对象需要实现 java.io.Serializable 接口

## 如果一个子类实现了序列化，父类没有实现，那么父类中的成员变量能否被序列化？
> 对象反序列化时，如果父类未实现序列化接口，则反序列出的对象会再次调用父类的构造函数来完成属于父类那部分内容的初始化。
## 你有了解过哪些序列化技术？以及他们之间的差异性？
> ##### xml
XML 序列化的好处在于可读性好，方便阅读和调试。但是序列化以后的字节码文件比较大，
而且效率不高，适用于对性能不高，而且 QPS 较低的企业级内部系统之间的数据交换的场景，
同时 XML 又具有语言无关性，所以还可以用于异构系统之间的数据交换和协议。比如我们熟
知的 Webservice，就是采用 XML 格式对数据进行序列化的。 XML 序列化/反序列化的实现方
式有很多，熟知的方式有 XStream 和 Java 自带的 XML 序列化和反序列化两种
> ##### json
JSON（JavaScript Object Notation）是一种轻量级的数据交换格式，相对于 XML 来说， JSON
的字节流更小，而且可读性也非常好。现在 JSON 数据格式在企业运用是最普遍的
JSON 序列化常用的开源工具有很多
1. [Jackson](https://github.com/FasterXML/jackson)
2. [阿里开源的 FastJson](https://github.com/alibaba/fastjon)
3. [Google 的 GSON](https://github.com/google/gson)

这几种 json 序列化工具中， Jackson 与 fastjson 要比 GSON 的性能要好，但是 Jackson、
GSON 的稳定性要比 Fastjson 好。而 fastjson 的优势在于提供的 api 非常容易使用
> ##### Hessian
Hessian 是一个支持跨语言传输的二进制序列化协议，相对于 Java 默认的序列化机制来说，
Hessian 具有更好的性能和易用性，而且支持多种不同的语言实际上 Dubbo 采用的就是 Hessian 序列化来实现，只不过 Dubbo 对 Hessian 进行了重构，
性能更高

> ##### Protobuf
Protobuf 是 Google 的一种数据交换格式，它独立于语言、独立于平台。 Google 提供了多种
语言来实现，比如 Java、 C、 Go、 Python，每一种实现都包含了相应语言的编译器和库文件，
Protobuf 是一个纯粹的表示层协议，可以和各种传输层协议一起使用。  
Protobuf 使用比较广泛，主要是空间开销小和性能比较好，非常适合用于公司内部对性能要
求高的 RPC 调用。 另外由于解析性能比较高，序列化以后数据量相对较少，所以也可以应
用在对象的持久化场景中  
但是要使用 Protobuf 会相对来说麻烦些，因为他有自己的语法，有自己的编译器，如果需要
用到的话必须要去投入成本在这个技术的学习中  
protobuf 有个缺点就是要传输的每一个类的结构都要生成对应的 proto 文件，如果某个类发
生修改，还得重新生成该类对应的 proto 文件

> ##### kyro
Kryo 是一种非常成熟的序列化实现，已经在 Hive、 Storm）中使用得比较广泛，不过它不能
跨语言. 目前 dubbo 已经在 2.6 版本支持 kyro 的序列化机制。它的性能要优于之前的
hessian2
## transient是干嘛的？
> Transient 关键字的作用是控制变量的序列化，在变量声明前加上该关键字，可以阻止该变
量被序列化到文件中，在被反序列化后， transient 变量的值被设为初始值，如 int 型的是
0，对象型的是 null
## 有什么方法能够绕过transient的机制。这个实现机制的原理是什么？
> 通过writeObject和readObject两个方法依然能够使得 name 字段正确被序列化和反序列化  
writeObject 和 readObject 是两个私有的方法，他们是什么时候被调用的呢？从运行结果来
看，它确实被调用。而且他们并不存在于 Java.lang.Object，也没有在 Serializable 中去声明。
我们唯一的猜想应该还是和 ObjectInputStream 和 ObjectOutputStream 有关系，所以基于
这个入口去看看在哪个地方有调用，从源码层面来分析可以看到， readObject 是通过反射来调用的。
## serializable的安全性如何保证？
> Java 的序列化机制是通过判断类的 serialVersionUID 来验证版本一致性的。在进行反序列化
时， JVM 会把传来的字节流中的 serialVersionUID 与本地相应实体类的 serialVersionUID 进
行比较，如果相同就认为是一致的，可以进行反序列化，否则就会出现序列化版本不一致的
异常，即是 InvalidCastException。
## 有没有了解过protobuf，它的序列化实现原理是什么？
* 1.压缩算法
* 2.转码，字符转化为编码（ASCII码）
* 3.存储格式 采用T-L-V作为存储方式，减少了分隔符的使用 & 数据存储得紧凑    
 TAG ：字段标识号，用于标识字段
 Length ：Value的字节长度
 Value ： 消息字段经过编码后的值
* 4.负数的存储  
在计算机中，负数会被表示为很大的整数，因为计算机定义负数符号位为数字的最高位，所
以如果采用 varint 编码表示一个负数，那么一定需要 5 个比特位。所以在 protobuf 中通过
sint32/sint64 类型来表示负数，负数的处理形式是先采用 zigzag 编码（把符号数转化为无符
号数），在采用 varint 编码。
## serialVersionUID的 作 用 是 什 么 ？ 如 果 我 不 设 置serialVersionUID,有没有问题？
> Java 的序列化机制是通过判断类的 serialVersionUID 来验证版本一致性的。在进行反序列化
时， JVM 会把传来的字节流中的 serialVersionUID 与本地相应实体类的 serialVersionUID 进
行比较，如果相同就认为是一致的，可以进行反序列化，否则就会出现序列化版本不一致的
异常，即是 InvalidCastException。

> 不设置的话，接口会默认生成一个serialVersionUID，但是强烈建议用户自定义一个serialVersionUID,因为默认的serialVersinUID对于class的细节非常敏感，反序列化时可能会导致InvalidClassException这个异常。
