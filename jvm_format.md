## jvm format的学习进展

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
长度 0x0004 内容：7468 6973
翻译过来：this

### 第15个常量
类型 01 结构同上
长度 0x0005 内容：6765 7454 66
翻译过来：getTf
get的方法名

### 第16个常量
类型 01 结构同上
长度 0x0014 即：20 内容：2829 4c6a 6176 612f 6c61 6e67
2f53 7472 696e 673b
翻译过来：()Ljava/lang/String;

### 第17个常量
类型 01 结构同上
长度 0x0005 内容：73 6574 5466
翻译过来：setTf

### 第18个常量
类型 01 结构同上
长度 0x0015 长度：21 内容：28 4c6a 6176 612f 6c61 6e67 2f53
7472 696e 673b 2956
翻译过来：(Ljava/lang/String;)V

### 第19个常量
类型 01 结构同上
长度 0x000a 内容：53 6f75 7263
6546 696c 65
翻译过来：SourceFile

### 第20个常量
类型 01 结构同上
长度 0x0009 内容：5465 7374 2e6a 6176
61
翻译过来：Test.java

### 第21个常量
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

### 第22个常量
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

### 第23个常量：
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

### 第24个常量：
类型：01 CONSTANT_Utf8
结构：同上
字符串长度：0010 即：6a 6176
612f 6c61 6e67 2f4f 626a 6563 74
即：java/lang/Object





