# 开发中遇到的IE兼容性问题

## 一、select在IE中样式问题

- 在ie中，select的样式和其他浏览器有很大不同，它的下拉图标很丑，并且点击下拉会默认以选中的option为中心，选中的option上面的其他option会出现在select上方，并且不同版本IE浏览器默认select的大小不一样

- 解决办法：用div+ul模拟下拉，这样既保证的样式的美观，也保证了兼容

## 二、IE中new Date(date)的返回错误的问题
  
- 因为IE对new Date（date）中的date格式有要求，不能是2019-01-10这种，只能是2019/01/10这种格式。

```exg
解决办法：对日期格式进行处理

var date=item.replace(/-/g, '/')
var itemStamp = new Date(date).getTime();
```

- 对于常见浏览器时间格式兼容

```exg
chrome:
    1."2018-1-1"
    2."2018/1/1"
    3."2018,1,1"
IE：
    1."2018/1/1"
FF:
    1."2018/1/1"
    2."2018,1,1"
```

##　三、IE、火狐、谷歌浏览器对textarea的col,row属性支持不一样

- 谷歌浏览器开发用到了textarea,设置了col和row来控制它的宽高，然后在IE（笔记本）上看了没有问题，后来在优化的时候，发现IE和firefox中的textarea在大屏幕（台式）下，宽度过长了，谷歌没有问题。

- 原因：IE、火狐、谷歌浏览器对textarea的col,row属性支持不一样

- 解决办法：不用col来控制，通过设置宽度百分比让其自适应。

## 四、IE 发送ajax url带中文请求失败的问题

- 其他浏览器在发送ajax请求时会自动对url进行编码进行请求，而IE浏览器不会，所以需要手动对有中文参数的url进行编码

- 解决办法：encodeURI(url地址)
