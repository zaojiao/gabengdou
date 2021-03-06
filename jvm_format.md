## jvm学习-子篇( format的学习进展 )

创建一个test的文件
```
package com.ny.inf.strategy.srv;
public class Test {

    private String tf;

    public String getTf() {
        return tf;
    }

    public void setTf(String tf) {
        this.tf = tf;
    }
}

```
对应的结构文件：
```
cafe babe 0000 0033 0018 0a00 0400 1409
0003 0015 0700 1607 0017 0100 0274 6601
0012 4c6a 6176 612f 6c61 6e67 2f53 7472
696e 673b 0100 063c 696e 6974 3e01 0003
2829 5601 0004 436f 6465 0100 0f4c 696e
654e 756d 6265 7254 6162 6c65 0100 124c
6f63 616c 5661 7269 6162 6c65 5461 626c
6501 0004 7468 6973 0100 1e4c 636f 6d2f
6e6e 2f69 6e66 2f73 7472 6174 6567 792f
7372 762f 5465 7374 3b01 0005 6765 7454
6601 0014 2829 4c6a 6176 612f 6c61 6e67
2f53 7472 696e 673b 0100 0573 6574 5466
0100 1528 4c6a 6176 612f 6c61 6e67 2f53
7472 696e 673b 2956 0100 0a53 6f75 7263
6546 696c 6501 0009 5465 7374 2e6a 6176
610c 0007 0008 0c00 0500 0601 001c 636f
6d2f 6e6e 2f69 6e66 2f73 7472 6174 6567
792f 7372 762f 5465 7374 0100 106a 6176
612f 6c61 6e67 2f4f 626a 6563 7400 2100
0300 0400 0000 0100 0200 0500 0600 0000
0300 0100 0700 0800 0100 0900 0000 2f00
0100 0100 0000 052a b700 01b1 0000 0002
000a 0000 0006 0001 0000 0003 000b 0000
000c 0001 0000 0005 000c 000d 0000 0001
000e 000f 0001 0009 0000 002f 0001 0001
0000 0005 2ab4 0002 b000 0000 0200 0a00
0000 0600 0100 0000 0800 0b00 0000 0c00
0100 0000 0500 0c00 0d00 0000 0100 1000
1100 0100 0900 0000 3e00 0200 0200 0000
062a 2bb5 0002 b100 0000 0200 0a00 0000
0a00 0200 0000 0c00 0500 0d00 0b00 0000
1600 0200 0000 0600 0c00 0d00 0000 0000
0600 0500 0600 0100 0100 1200 0000 0200
13
```

### 分析下结构
```
ClassFile {
    u4             magic;
    u2             minor_version;
    u2             major_version;
    u2             constant_pool_count;
    cp_info        constant_pool[constant_pool_count-1];
    u2             access_flags;
    u2             this_class;
    u2             super_class;
    u2             interfaces_count;
    u2             interfaces[interfaces_count];
    u2             fields_count;
    field_info     fields[fields_count];
    u2             methods_count;
    method_info    methods[methods_count];
    u2             attributes_count;
    attribute_info attributes[attributes_count];
}
```

### 关于常量池
常量池中主要存放两大类常量：字面量（Literal）和符号引用（Symbolic References）。
字面量比较接近Java语言层面的常量概念，如文本字符串、声明为final的常量值等。而符号引用则属于编译原理方面的概念，包括下面三类常量：
* 类和接口的全限定名 （Fully Qualified Name）
* 字段的名称和描述符 （Descriptor）
* 方法的名称和描述
---（这一段摘自《深入理解Java虚拟机》，周志明 著）

常量池大小u2是0x0018，16进制即：24，由于是从1开始，所以大小为23个
紧接着我们分析常量池中的第一个常量，第一位u1类型的标志位

下边是引用https://docs.oracle.com/javase/specs/jvms/se7/html/jvms-4.html
jvm官方文档里的部分说明常量池的项目类型：
```
Table 4.3. Constant pool tags
Constant Type	Value
CONSTANT_Class	7
CONSTANT_Fieldref	9
CONSTANT_Methodref	10
CONSTANT_InterfaceMethodref	11
CONSTANT_String	8
CONSTANT_Integer	3
CONSTANT_Float	4
CONSTANT_Long	5
CONSTANT_Double	6
CONSTANT_NameAndType	12
CONSTANT_Utf8	1
CONSTANT_MethodHandle	15
CONSTANT_MethodType	16
CONSTANT_InvokeDynamic	18
```
然后我们继续分析
### 第1个常量
u1的类型标志是：0a，也就是：CONSTANT_Methodref 即：方法的符号引用
那么方法的符号引用的结构式：
```
CONSTANT_Methodref_info {
    u1 tag;
    u2 class_index;
    u2 name_and_type_index;
}
```
那么解下来的u2即类的索引值为：0x0004，即第4个常量；
再接下来是u2类型的名称和类型索引值为：0x0014，即第20个常量

以上第一个常量类型为CONSTANT_Methodref 方法的符号引用，已经分析完了，我们继续：

### 第2个常量：
类型09:CONSTANT_Fieldref 是字段的符号引用
字段的符号应用结构是：
```
CONSTANT_Fieldref_info {
    u1 tag;
    u2 class_index;
    u2 name_and_type_index;
}
```
09类型后，u2表示类索引的值是：0x0003，表示类在第三个常量
然后u2是:0x0015 名称和类型的索引在第21个常量

### 第3个常量：
类型07：CONSTANT_Class，代表一个类或接口的符号引用，结构是：
```
CONSTANT_Class_info {
    u1 tag;
    u2 name_index;
}
```
标志位不再说了，所有常量的第一个u1都是标志位，那么后边的u2类型就是值了，索引是：0x0016即22，第22个常量是类的名称

### 第4个常量：
类型07：同上
索引在：0x0017 第23，第23个常量是类或者接口的名称

### 第5个常量：
类型：01 CONSTANT_Utf8
结构：
```
CONSTANT_Utf8_info {
    u1 tag;
    u2 length;
    u1 bytes[length];
}
```
字符串长度：0002,那长度就是2个字节
即：0x74 0x66 表示：tf,即咱们java文件里定义的变量名称：tf

### 第6个常量
类型01 CONSTANT_Utf8
结构同上
字符串长度：0x0012即：18
值：4c6a 6176 612f 6c61 6e67 2f53 7472
696e 673b
翻译过来是：Ljava/lang/String;
是咱们定义的名称为tf的变量的类型，java.lang.String类型
为什么是L开头点变斜杠，这是JNI的java类型，补一下JNI的知识就好了

### 第7个常量
类型 01 结构同上
长度：0x0006 内容：3c 696e 6974 3e
翻译过来是：<init>
这一部分为一些代码中没有出现的内容，是自动生成的常量，会被字段表、方法表、属性表引用到，用来描述一些不方便使用“固定字节”进行表达的内容
    
### 第8个常量
类型 01 结构同上
长度 0x0003 内容：2829 56
翻译过来：()V
解释同上第7个常量，后续弄清楚再来写

### 第9个常量
类型 01 结构同上
长度 0x0004 内容：436f 6465
翻译过来：Code

### 第10个常量
类型 01 结构同上
长度 0x000f 内容：4c 696e
654e 756d 6265 7254 6162 6c65
翻译过来：LineNumberTable

### 第11个常量
类型 01 结构同上
长度 0x0012 即：18 内容：4c
6f63 616c 5661 7269 6162 6c65 5461 626c
65
翻译过来：LocalVariableTable

### 第12个常量
类型 01 结构同上
长度 0x0004 内容：7468 6973
翻译过来：this

### 第13个常量
类型 01 结构同上
长度 0x001e 内容：4c 636f 6d2f
6e6e 2f69 6e66 2f73 7472 6174 6567 792f
7372 762f 5465 7374 3b
翻译过来：Lcom/nn/inf/strategy/srv/Test;
包名

### 第14个常量
类型 01 结构同上
长度 0x0005 内容：6765 7454 66
翻译过来：getTf
get的方法名

### 第15个常量
类型 01 结构同上
长度 0x0014 即：20 内容：2829 4c6a 6176 612f 6c61 6e67
2f53 7472 696e 673b
翻译过来：()Ljava/lang/String;

### 第16个常量
类型 01 结构同上
长度 0x0005 内容：73 6574 5466
翻译过来：setTf

### 第17个常量
类型 01 结构同上
长度 0x0015 长度：21 内容：28 4c6a 6176 612f 6c61 6e67 2f53
7472 696e 673b 2956
翻译过来：(Ljava/lang/String;)V

### 第18个常量
类型 01 结构同上
长度 0x000a 内容：53 6f75 7263
6546 696c 65
翻译过来：SourceFile

### 第19个常量
类型 01 结构同上
长度 0x0009 内容：5465 7374 2e6a 6176
61
翻译过来：Test.java

### 第20个常量
类型 0c 即：CONSTANT_NameAndType
结构：
```
CONSTANT_NameAndType_info {
    u1 tag;
    u2 name_index;
    u2 descriptor_index;
}
```
name_index: 0x0007 索引：#7      // <init>
descriptor_index: 0008 索引：#8  // ()V

### 第21个常量
类型 0c 结构同上
即：CONSTANT_NameAndType
结构：
```
CONSTANT_NameAndType_info {
    u1 tag;
    u2 name_index;
    u2 descriptor_index;
}
```
name_index: 0x0005 索引：#5      // tf
descriptor_index: 0006 索引：#6  // Ljava/lang/String;

### 第22个常量：
类型：01 CONSTANT_Utf8
结构：
```
CONSTANT_Utf8_info {
    u1 tag;
    u2 length;
    u1 bytes[length];
}
```
字符串长度：001c 即：636f
6d2f 6e6e 2f69 6e66 2f73 7472 6174 6567
792f 7372 762f 5465 7374
即：com/nn/inf/strategy/srv/Test

### 第23个常量：
类型：01 CONSTANT_Utf8
结构：同上
字符串长度：0010 即：6a 6176
612f 6c61 6e67 2f4f 626a 6563 74
即：java/lang/Object


### u2：access_flags
值：0x0021
```
Table 4.1. Class access and property modifiers
Flag Name	Value	Interpretation
ACC_PUBLIC	0x0001	Declared public; may be accessed from outside its package.
ACC_FINAL	0x0010	Declared final; no subclasses allowed.
ACC_SUPER	0x0020	Treat superclass methods specially when invoked by the invokespecial instruction.
ACC_INTERFACE	0x0200	Is an interface, not a class.
ACC_ABSTRACT	0x0400	Declared abstract; must not be instantiated.
ACC_SYNTHETIC	0x1000	Declared synthetic; not present in the source code.
ACC_ANNOTATION	0x2000	Declared as an annotation type.
ACC_ENUM	0x4000	Declared as an enum type.
```
access_flags的信息表
我们是一个普通的public修饰的类，并且是用jdk1.2之后的编译器编译，所以access_flags标志位：0x0001|0x0020 = 0x0021
也即表示是：ACC_PUBLIC和ACC_SUPER
ACC_SUPER是jdk1.2之后都有的，用来区分invokespecial指令的语义，因为1.2之后语义发生变化，所以jdk1.2之后的版本这个都是真

### this_class u2
0x0003 常量池的#3项
最终指向的值是String 即：com/nn/inf/strategy/srv/Test

### super_class u2
0x0004 常量池的#4项
最终指向值String 即：java/lang/Object

### interfaces_count u2
0x0000 也即：0
即表示该类没有实现任何接口

### interfaces[interfaces_count]  u2
由于 interfaces_count 为0，因此这一项不再占用任何字节，也即没有字节表示

### fields_count u2
0x0001
接口或类中声明的变量的个数，本例中为1

### fields[fields_count] 字段表
结构：
```
field_info {
    u2             access_flags;
    u2             name_index;
    u2             descriptor_index;
    u2             attributes_count;
    attribute_info attributes[attributes_count];
}
```

access_flags 0x0002 代表private私有属性
name_index 0x0005 为常量池中第5个常量，值：tf，即常量名称
descriptor_index 0x0006 为常量池第6个常量，值："Ljava/lang/String;", 注意分号代表结束
attributes_count 为0x0000 表示0个属性

### methods_count
0x0003
方法表中方法的个数是3

### methods[methods_count] 方法表
看到方法表，发现方法的名称、方法描述符、属性表
其中方法描述代表了方法的返回值、参数等信息
```
method_info {
    u2             access_flags;
    u2             name_index;
    u2             descriptor_index;
    u2             attributes_count;
    attribute_info attributes[attributes_count];
}
```
```
Table 4.5. Method access and property flags

Flag Name	Value	Interpretation
ACC_PUBLIC	0x0001	Declared public; may be accessed from outside its package.
ACC_PRIVATE	0x0002	Declared private; accessible only within the defining class.
ACC_PROTECTED	0x0004	Declared protected; may be accessed within subclasses.
ACC_STATIC	0x0008	Declared static.
ACC_FINAL	0x0010	Declared final; must not be overridden (§5.4.5).
ACC_SYNCHRONIZED	0x0020	Declared synchronized; invocation is wrapped by a monitor use.
ACC_BRIDGE	0x0040	A bridge method, generated by the compiler.
ACC_VARARGS	0x0080	Declared with variable number of arguments.
ACC_NATIVE	0x0100	Declared native; implemented in a language other than Java.
ACC_ABSTRACT	0x0400	Declared abstract; no implementation is provided.
ACC_STRICT	0x0800	Declared strictfp; floating-point mode is FP-strict.
ACC_SYNTHETIC	0x1000	Declared synthetic; not present in the source code.
```
第一个：
access_flags 0x0001 代表ACC_PUBLIC  
name_index 0x0007 方法简单名称，常量池中第7项即：<init>  
descriptor_index 0x0008 方法描述符，常量池第8项：()V， 表示没有参数返回值为Void  
attributes_count 0x0001 属性表中属性个数，即 1  
attributes[attributes_count] 接下来就是属性了  
属性表结构为：
    ```
    attribute_info {
    u2 attribute_name_index;
    u4 attribute_length;
    u1 info[attribute_length];
    }
    ```  

属性表的名称索引值：
```
Table 4.6. Predefined class file attributes
Attribute	Section	Java SE	class file
ConstantValue	§4.7.2	1.0.2	45.3
Code	§4.7.3	1.0.2	45.3
StackMapTable	§4.7.4	6	50.0
Exceptions	§4.7.5	1.0.2	45.3
InnerClasses	§4.7.6	1.1	45.3
EnclosingMethod	§4.7.7	5.0	49.0
Synthetic	§4.7.8	1.1	45.3
Signature	§4.7.9	5.0	49.0
SourceFile	§4.7.10	1.0.2	45.3
SourceDebugExtension	§4.7.11	5.0	49.0
LineNumberTable	§4.7.12	1.0.2	45.3
LocalVariableTable	§4.7.13	1.0.2	45.3
LocalVariableTypeTable	§4.7.14	5.0	49.0
Deprecated	§4.7.15	1.1	45.3
RuntimeVisibleAnnotations	§4.7.16	5.0	49.0
RuntimeInvisibleAnnotations	§4.7.17	5.0	49.0
RuntimeVisibleParameterAnnotations	§4.7.18	5.0	49.0
RuntimeInvisibleParameterAnnotations	§4.7.19	5.0	49.0
AnnotationDefault	§4.7.20	5.0	49.0
BootstrapMethods	§4.7.21	7	51.0
```
Code的结构为：
```
Code_attribute {
    u2 attribute_name_index;
    u4 attribute_length;
    u2 max_stack;
    u2 max_locals;
    u4 code_length;
    u1 code[code_length];
    u2 exception_table_length;
    {   u2 start_pc;
        u2 end_pc;
        u2 handler_pc;
        u2 catch_type;
    } exception_table[exception_table_length];
    u2 attributes_count;
    attribute_info attributes[attributes_count];
}
```

属性为：
attribute_name_index 0x0009 属性名称索引，第9项常量池：Code, Code是类文件中  
，最重要的一个属性，如果把类文件的内容分为代码和元数据两部分的话，那么Code属性就是里边唯一一个描述描述代码的部分了  
attribute_length 0x0000002f 属性长度为：47   
max_stack 0x0001 操作数栈深度最大值：1  
max_locals 局部变量所需存储空间 0x0001，计量单位是：slot，是虚拟机为局部变量分配内存所使用的最小单位，byte、char、float、int、short、boolean和returnAddress等长度不超过32位的数据结构，每个局部变量占用一个Slot，而double和long这两种64位局部变量真用两个Slot存储  
code_length 0x00 0000 05 指用来存储java代码的字节码指令的长度，本例长度为：5  
接下来就是5个指令：2a b7 00 01 b1  
0x2a：助记符 aload_0 将第一个引用类型本地变量推送至栈顶  
0xb7：助记符 invokespecial 调用超类构造方法，实例初始化方法，私有方法  
该指令有一个u2类型的参数说明调用哪一个，指向常量池中一个CONSTANT_METHOD_INFO的类型常量，即此方法的方法符号引用  
0x0001：即常量池中第一个常量，是init()方法的方法描述符  
0xb1：助记符 return 从当前方法返回void  

### 属性表
属性表有很多，21项在jdk1.7上，其中Code属性最为重要，我们可能也最关注，代表的是方法代码的字节码指令；
关于字节码指令，找时间认真详细的写一下，是比较重要的部分，这里只一笔带过先；

--------------- 我是帅气的分隔符 --------------------

在JDK的bin目录里，Oracle公司已经为我们准备了专门用于分析Class文件字节码的工具：javap
如下可以跟我们手工计算的内容进行对比：
命令：javap -verbose Test.class
```
Classfile /Users/mark/eclipse/workspace/mns-strategy-srv/target/classes/com/nn/inf/strategy/srv/Test.class
  Last modified 2017-12-10; size 529 bytes
  MD5 checksum fb8b18587a4bf7298dc0d01b5ce85f90
  Compiled from "Test.java"
public class com.nn.inf.strategy.srv.Test
  minor version: 0
  major version: 51
  flags: ACC_PUBLIC, ACC_SUPER
Constant pool:
   #1 = Methodref          #4.#20         // java/lang/Object."<init>":()V
   #2 = Fieldref           #3.#21         // com/nn/inf/strategy/srv/Test.tf:Ljava/lang/String;
   #3 = Class              #22            // com/nn/inf/strategy/srv/Test
   #4 = Class              #23            // java/lang/Object
   #5 = Utf8               tf
   #6 = Utf8               Ljava/lang/String;
   #7 = Utf8               <init>
   #8 = Utf8               ()V
   #9 = Utf8               Code
  #10 = Utf8               LineNumberTable
  #11 = Utf8               LocalVariableTable
  #12 = Utf8               this
  #13 = Utf8               Lcom/nn/inf/strategy/srv/Test;
  #14 = Utf8               getTf
  #15 = Utf8               ()Ljava/lang/String;
  #16 = Utf8               setTf
  #17 = Utf8               (Ljava/lang/String;)V
  #18 = Utf8               SourceFile
  #19 = Utf8               Test.java
  #20 = NameAndType        #7:#8          // "<init>":()V
  #21 = NameAndType        #5:#6          // tf:Ljava/lang/String;
  #22 = Utf8               com/nn/inf/strategy/srv/Test
  #23 = Utf8               java/lang/Object
{
  public com.nn.inf.strategy.srv.Test();
    descriptor: ()V
    flags: ACC_PUBLIC
    Code:
      stack=1, locals=1, args_size=1
         0: aload_0
         1: invokespecial #1                  // Method java/lang/Object."<init>":()V
         4: return
      LineNumberTable:
        line 3: 0
      LocalVariableTable:
        Start  Length  Slot  Name   Signature
            0       5     0  this   Lcom/nn/inf/strategy/srv/Test;

  public java.lang.String getTf();
    descriptor: ()Ljava/lang/String;
    flags: ACC_PUBLIC
    Code:
      stack=1, locals=1, args_size=1
         0: aload_0
         1: getfield      #2                  // Field tf:Ljava/lang/String;
         4: areturn
      LineNumberTable:
        line 8: 0
      LocalVariableTable:
        Start  Length  Slot  Name   Signature
            0       5     0  this   Lcom/nn/inf/strategy/srv/Test;

  public void setTf(java.lang.String);
    descriptor: (Ljava/lang/String;)V
    flags: ACC_PUBLIC
    Code:
      stack=2, locals=2, args_size=2
         0: aload_0
         1: aload_1
         2: putfield      #2                  // Field tf:Ljava/lang/String;
         5: return
      LineNumberTable:
        line 12: 0
        line 13: 5
      LocalVariableTable:
        Start  Length  Slot  Name   Signature
            0       6     0  this   Lcom/nn/inf/strategy/srv/Test;
            0       6     1    tf   Ljava/lang/String;
}
SourceFile: "Test.java"
```

### 最后讲讲字节码指令




