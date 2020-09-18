# 什么是JSON：

JSON：JavaScript对象表示法（JavaScript Object Notation）。
JSON是存储和交换文本信息的语法。类似 XML。
JSON比XML更小、更快，更易解析。

JSON是轻量级的文本数据交换格式；JSON独立于语言，JSON使用JavaScript语法来描述数据对象，但是JSON仍然独立于语言和平台。JSON解析器和JSON库支持许多不同的编程语言；JSON具有自我描述性，更易理解。JSON文本格式在语法上与创建JavaScript对象的代码相同。由于这种相似性，无需解析器，JavaScript程序能够使用内建的eval()函数，用JSON数据来生成原生的JavaScript对象。

```html
<html>
<body>
<h2>在JavaScript中创建JSON对象</h2>
 
<p>
Name: <span id="jname"></span><br />
Age: <span id="jage"></span><br />
Address: <span id="jstreet"></span><br />
Phone: <span id="jphone"></span><br />
</p>
 
<script type="text/javascript">
var JSONObject= {
"name":"Bill Gates",
"street":"Fifth Avenue New York 666",
"age":56,
"phone":"555 1234567"};
document.getElementById("jname").innerHTML=JSONObject.name
document.getElementById("jage").innerHTML=JSONObject.age
document.getElementById("jstreet").innerHTML=JSONObject.street
document.getElementById("jphone").innerHTML=JSONObject.phone
</script>
 
</body>
</html>
```
## JSON与XML的相同之处：
- JSON是纯文本；
- JSON具有“自我描述性”（人类可读）；
- JSON具有层级结构（值中存在值）；
- JSON可通过JavaScript进行解析；
- JSON数据可使用AJAX进行传输；
## JSON与XML的不同之处：
- 没有结束标签；
- 更短；
- 读写的速度更快；
- 能够使用内建的JavaScript eval()方法进行解析；
- 使用数组；
- 不使用保留字；
## 为什么使用JSON：
- 对于AJAX应用程序来说，JSON比XML更快更易使用：
- 使用XML
- 读取XML文档；
- 使用XML DOM来循环遍历文档；
- 读取值并存储在变量中；
- 使用JSON
- 读取JSON字符串；
- 用eval()处理JSON字符串；
## JSON语法规则：
- JSON语法是JavaScript对象表示法语法的子集。
- 数据在名称/值对中
- 数据由逗号分隔
- 花括号保存对象
- 方括号保存数组
## JSON名称/值对
JSON数据的书写格式是：名称/值对。
名称/值对包括字段名称（在双引号中），后面写一个冒号，然后是值：
"firstName" : "John"
这很容易理解，等价于这条JavaScript语句：
firstName = "John"
## JSON值：
- 数字（整数或浮点数）
- 字符串（在双引号中）
- 逻辑值（true 或 false）
- 数组（在方括号中）
- 对象（在花括号中）
- null
## JSON对象：
JSON对象在花括号中书写：
对象可以包含多个名称/值对：
{ "firstName":"John" , "lastName":"Doe" }
这一点也容易理解，与这条JavaScript语句等价：
firstName = "John"
lastName = "Doe"
## JSON数组：
```json
JSON数组在方括号中书写：
数组可包含多个对象：
{
"employees": [
{ "firstName":"John" , "lastName":"Doe" },
{ "firstName":"Anna" , "lastName":"Smith" },
{ "firstName":"Peter" , "lastName":"Jones" }
]
}
```
在上面的例子中，对象 "employees" 是包含三个对象的数组。每个对象代表一条关于某人（有姓和名）的记录。
## JSON使用JavaScript语法：
```json
因为JSON使用JavaScript语法，所以无需额外的软件就能处理JavaScript中的JSON。
通过JavaScript，您可以创建一个对象数组，并像这样进行赋值：
例子
var employees = [
{ "firstName":"Bill" , "lastName":"Gates" },
{ "firstName":"George" , "lastName":"Bush" },
{ "firstName":"Thomas" , "lastName": "Carter" }
];
可以像这样访问JavaScript对象数组中的第一项：
employees[0].lastName;
返回的内容是：
Gates
可以像这样修改数据：
employees[0].lastName = "Jobs";
```