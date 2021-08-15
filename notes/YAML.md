# 一、概念

## 1.1 定义

YAML是 "YAML Ain't a Markup Language"（YAML 不是一种标记语言）的递归缩写。

YAML的意思其实是"Yet Another Markup Language"（仍是一种标记语言）。



## 1.2 特点

- 大小写敏感
- 使用缩进表示层级关系
- 缩进不允许使用tab，只允许空格
- 缩进的空格数不重要，只要相同层级的元素左对齐即可
-  `#` 表示注释





# 二、语法



## 2.1 基本语法

`k: v` 表示一对键值对

```yaml
server:
    port: 8080
    path: /hello
```



## 2.2 值的写法

### 1）字面量

普通的值（数字，字符串，布尔）

字符串默认不用加上单引号或者双引号；

- 双引号；会转义字符串里的特殊字符，特殊字符会作为其本身的特殊用法

- 单引号；不会转义特殊字符，特殊字符最终只是一个普通的字符串



### 2）数组

1. 用 `-` 表示数组中的一个元素

   ```yaml
   device:
    - iphone
    - ipad
    - mac
   ```

2. 行内写法

   ```yaml
   device: { iphone, ipad, mac }
   ```




### 3）对象、Map

1. 使用缩进

   ```yaml
   maps:
     phone: xiaomi
     pc: leveno
   ```

   

2. 行内写法

   ```yaml
   maps: { phone: xiaomi, pc: leveno }
   ```

   











