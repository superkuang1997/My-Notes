# XML🦎

XML：Extensible Markup Language 可扩展标记语言

> 在XML中，所有的标签都是自定义的

功能：存储数据

- 作为配置文件
- 在网络中传输

HTML 和 XML的区别：

- XML 的标签都是自定义的，HTML 标签是预定义的
- xml 的语法严格，html 语法松散
- xml 是存储数据的，html 是展示数据的



# 语法🦎

## 基本语法

1. xml文档的后缀名 .xml
2. xml第一行必须定义为文档声明
3. xml文档中有且仅有一个根标签
4. 属性值必须使用引号(单双都可)引起来
5. 标签必须正确关闭
6. xml标签名称区分大小写

```xml
<?xml version='1.0' ?>
<users>
    <user id='1'>
        <name>zhangsan</name>
        <age>23</age>
        <gender>male</gender>
        <br/>
    </user>

    <user id='2'>
        <name>lisi</name>
        <age>24</age>
        <gender>female</gender>
    </user>
</users>
```



## XML组成部分

### 文档声明

格式：`<?xml 属性列表 ?>`

属性列表：

- version：版本号，必须的属性
- encoding：编码方式。告知解析引擎当前文档使用的字符集，默认值：ISO-8859-1
- standalone：是否独立
  - yes：不依赖其他文件
  - no：依赖其他文件



### 指令

用于结合css展示数据

`<?xml-stylesheet type="text/css" href="style.css" ?>`

### 标签

标签名称是自定义的

* 名称可以包含字母、数字以及其他的字符 
* 名称不能以数字或者标点符号开始 
* 名称不能以字母 xml开始（包含大小写） 
* 名称不能包含空格 
### 属性

id属性值唯一

### 文本

CDATA区：在该区域中的数据会被原样展示

格式：  `<![CDATA[ 数据 ]]>`

# 约束🦎

约束：规定xml文档的书写规则

- DTD：一种简单的约束技术
- Schema：一种复杂的约束技术



## DTD

后缀名：dtd

1. 格式

```dtd
<!ELEMENT students (student*) >     // * 表示通配，零次或多次
<!ELEMENT student (name,age,sex)>   // student 中允许存在三种标签
<!ELEMENT name (#PCDATA)>		   // #PCDATA表示字符串
<!ELEMENT age (#PCDATA)>
<!ELEMENT sex (#PCDATA)>
<!ATTLIST student number ID #REQUIRED>  // student标签有一个number类型的属性“ID”, 且必需
```

2. 引入DTD
   - 内部dtd：将约束规则定义在xml文档中
   - 外部dtd：将约束的规则定义在外部的dtd文件中
     - 本地：`<!DOCTYPE 根标签名 SYSTEM "dtd文件的位置">`
     - 网络：`<!DOCTYPE 根标签名 PUBLIC "dtd文件名字" "dtd文件的位置URL">`



## Schema

后缀名：xmd



## 引入



```xml
<students xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns="http://www.itcast.cn/xml"
          xsi:schemaLocation="http://www.itcast.cn/xml  student.xsd"
>
```



1. 填写xml文档的根元素

   ```xml
   <students  xxxxx
   >
   ```

2. 引入xsi前缀：  `xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"`

   前缀是一个固定的格式

3. 引入xsd文件命名空间：`xsi:schemaLocation="http://www.itcast.cn/xml  student.xsd"`

   - Schema文件：`student.xsd`
   - 命名空间：`http://www.itcast.cn/xml`

4. 为每一个xsd约束声明一个前缀，作为标识

     `xmlns:a="http://www.itcast.cn/xml"`

   然后就可以用 `a` 表示`http://www.itcast.cn/xml`，表示受此约束

   ```xml
   <a:student number="heima_0001">
       <a:name>tom</a:name>
       <a:age>18</a:age>
       <a:sex>male</a:sex>
   </a:student>
   ```

   如果不写前缀：`xmlns="http://www.itcast.cn/xml"`，则表示默认前缀，标签中无需加前缀

   前缀的作用在于，如果一个xml受到多个Schema文件约束，可以区分标签是受哪个Schema文件约束。

   ```xml
   <beans xmlns="http://www.springframework.org/schema/beans"  // 默认前缀
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  // 前缀xsi
          xmlns:context="http://www.springframework.org/schema/context"  // 前缀context
          xmlns:mvc="http://www.springframework.org/schema/mvc"  // 前缀mvc
          xsi:schemaLocation="
           http://www.springframework.org/schema/beans
           http://www.springframework.org/schema/beans/spring-beans.xsd
           http://www.springframework.org/schema/context 
           http://www.springframework.org/schema/context/spring-context.xsd
           http://www.springframework.org/schema/mvc
           http://www.springframework.org/schema/mvc/spring-mvc.xsd"
   >
   ```

   



## 示例

```xml
<?xml version="1.0"?>
<xsd:schema xmlns="http://www.itcast.cn/xml"
        xmlns:xsd="http://www.w3.org/2001/XMLSchema"
        targetNamespace="http://www.itcast.cn/xml" elementFormDefault="qualified">
    <xsd:element name="students" type="studentsType"/>  // 自定义一个"studentsType"类型
    
    <xsd:complexType name="studentsType">  
        <xsd:sequence>  // 表示按顺序出现
            // 再定义一个"studentType"类型  最少出现0次，最多不限
            <xsd:element name="student" type="studentType" minOccurs="0" maxOccurs="unbounded"/>
        </xsd:sequence>
    </xsd:complexType>
    
    <xsd:complexType name="studentType">
        <xsd:sequence>  
            <xsd:element name="name" type="xsd:string"/>  // 字符串类型
            <xsd:element name="age" type="ageType" />     // 自定义类型
            <xsd:element name="sex" type="sexType" />     // 自定义类型
        </xsd:sequence>
        // 定义一个属性，自定义类型"numberType"，且必需
        <xsd:attribute name="number" type="numberType" use="required"/>
    </xsd:complexType>
    
    // 上方自定义的类型"ageType"
    <xsd:simpleType name="ageType">
        <xsd:restriction base="xsd:integer">
            <xsd:minInclusive value="0"/>
            <xsd:maxInclusive value="256"/>
        </xsd:restriction>
    </xsd:simpleType>    
    
    // 上方自定义的类型"sexType"
    <xsd:simpleType name="sexType">
        <xsd:restriction base="xsd:string">
            <xsd:enumeration value="male"/>  // enumeration表示枚举，只能在male和female中选择
            <xsd:enumeration value="female"/>
        </xsd:restriction>
    </xsd:simpleType>
    
    // 上方自定义的类型"numberType"
    <xsd:simpleType name="numberType">
        <xsd:restriction base="xsd:string">
            <xsd:pattern value="heima_\d{4}"/>  // 自定义模式
        </xsd:restriction>
    </xsd:simpleType>
</xsd:schema> 

```



# 解析🦎

解析：操作xml文档，将文档中的数据读取到内存中



## Jsoup相关对象

### Jsoup

### Document

### Elements

### Element

### Node



## selector

selector是用Java实现CSS选择器，语法相似略有不同，参考Selector类中定义的语法。

方法：

- `Elements select(String cssQuery)`

## XPath

XPath，即XML路径语言，它是一种用来确定XML（标准通用标记语言的子集）文档中某部分位置的语言