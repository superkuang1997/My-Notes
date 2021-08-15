# Thymeleaf🍃

## 参考文档

👉  [Thymeleaf 官方文档](https://www.thymeleaf.org/doc/tutorials/3.0/usingthymeleaf.html)

👉  [Thymeleaf + Spring](https://www.thymeleaf.org/doc/tutorials/3.0/thymeleafspring.html)



## 名称空间

```html
<html lang="en" xmlns:th="http://www.thymeleaf.org">
```



# 表达式语法🍃

## Simple expressions

- Variable Expressions（变量表达式）： `${...}`

  用于访问容器上下文环境中的变量，功能同 jstl 中 `${...}`



- Selection Variable Expressions（选择表达式）: `*{...}`

  选择表达式计算的是选定的对象，而不是整个环境变量映射；只要是没有选择的对象，选择表达式与变量表达式的语法是完全一样的。

- Message Expressions（消息表达式）: `#{...}`

  通常与 `th:text` 属性一起使用，声明了 `th:text` 的标签的文本是 #{} 中的 key 所对应的 value，而标签内的文本将不会显示。

- Link URL Expressions: `@{...}`

- Fragment Expressions: `~{...}`



## Literals

- Text literals: `'one text'`, `'Another one!'`,…
- Number literals: `0`, `34`, `3.0`, `12.3`,…
- Boolean literals: `true`, `false`
- Null literal: `null`
- Literal tokens: `one`, `sometext`, `main`,…



## Text operations

- String concatenation: `+`
- Literal substitutions: `|The name is ${name}|`



## Arithmetic operations

- Binary operators: `+`, `-`, `*`, `/`, `%`
- Minus sign (unary operator): `-`



## Boolean operations

- Binary operators: `and`, `or`
- Boolean negation (unary operator): `!`, `not`



## Comparisons and equality

- Comparators: `>`, `<`, `>=`, `<=` (`gt`, `lt`, `ge`, `le`)
- Equality operators: `==`, `!=` (`eq`, `ne`)



## Conditional operators

- If-then: `(if) ? (then)`
- If-then-else: `(if) ? (then) : (else)`
- Default: `(value) ?: (defaultvalue)`



## Special tokens

- No-Operation: `_`



# 设置属性🍃

## 字面量

### 字符串

字面量可以用单引号表示： `'xxx'`

```html
<h1 th:text="'Example'"> Hello World</h1>
```



### 数字

```html
<p>今年是 <span th:text="2018">1900</span>.</p>
<p>两年后将会是 <span th:text="2018 + 2">1902</span>.</p>
```



### 布尔值

```html
<div th:if="true">
    你填的是true
</div>
```



## 设置属性

`th:attr="属性1=值1, 属性2=值2"`

如果有多个属性的值要设置，则用逗号分隔

不优雅的写法：

```html
<form action="#" method="post" th:attr="action=@{/template/example1}, method='get'">
    用户名：<input type="text" name="id"/>
    密码： <input type="text" name="password"/>
    <input type="submit" value="Subscribe!" th:attr="value='订阅'"/>
</form>
```



优雅的写法：

```html
<form action="#" method="post" th:action="@{/template/example1}" th:method="'get'">
    用户名：<input type="text" name="id"/>
    密码： <input type="text" name="password"/>
    <input type="submit" value="Subscribe!" th:value="订阅"/>
</form>
```





# 内置对象🍃

## 表达式基本对象

| 对象              | 作用                                                |
| ----------------- | --------------------------------------------------- |
| `#ctx`            | 获取 Thymeleaf 上下文对象                           |
| `#vars`           | 获取 Thymeleaf 上下文变量                           |
| `#locate`         | 获取上 Thymeleaf 上下文环境                         |
| `#requset`        | 如果是 web 程序，可以获取 `HttpServletRequest` 对象 |
| `#response`       | 如果是 web 程序，可以获取 `HttpServletReponse` 对象 |
| `#session`        | 如果是 web 程序，可以获取 `HttpSession` 对象        |
| `#servletContext` | 如果是 web 程序，可以获取 `HttpServletContext` 对象 |



## 表达式实用程序对象

| 对象         | 作用                                 |
| ------------ | ------------------------------------ |
| `#dates`     | 处理 `java.util.date` 的工具对象     |
| `#calendars` | 处理 `java.util.calendar` 的工具对象 |
| `#numbers`   | 用来对数字格式化的方法               |
| `#strings`   | 用来处理字符串的方法                 |
| `#bools`     | 用来判断布尔值的方法                 |
| `#arrays`    | 用来处理数组的方法                   |
| `#lists`     | 用来处理 List 集合的方法             |
| `#sets`      | 用来处理 set 集合的方法              |
| `#maps`      | 用来处理 map 集合的方法              |
| `#ids`       | 处理迭代对象的方法                   |





# 迭代🍃





# 条件🍃





# 模板布局🍃

## 抽取公共页面

```html
<div th:fragment="copy">
      &copy; 2011 The Good Thymes Virtual Grocery
</div>
```



## 选择公共页面

引用 `th:fragment="copy"` 的公共页面

```html
<div th:insert="~{footer :: copy}"></div>
```

`~{}` 是可省略的：

```html
<div th:insert="footer :: copy"></div>
```

也可以使用 CSS 选择器，如果公共页面的 id=copy，则可以这样引用

```html
<div th:insert="~{footer :: #copy}"></div>
```







## 插入公共页面

放置公共页面有三种方式：

```html
<body>

  ...

  <div th:insert="footer :: copy"></div>

  <div th:replace="footer :: copy"></div>

  <div th:include="footer :: copy"></div>
  
</body>
```



结果如下：

```html
<body>

  ...

  <div>
    <footer>
      &copy; 2011 The Good Thymes Virtual Grocery
    </footer>
  </div>

  <footer>
    &copy; 2011 The Good Thymes Virtual Grocery
  </footer>

  <div>
    &copy; 2011 The Good Thymes Virtual Grocery
  </div>
  
</body>
```



# 行内写法🍃



[[]]
