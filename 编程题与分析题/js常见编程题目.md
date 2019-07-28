<!-- TOC -->

- [用 Array 的 reduce 方法实现 map 方法（头条一面）](#%E7%94%A8-array-%E7%9A%84-reduce-%E6%96%B9%E6%B3%95%E5%AE%9E%E7%8E%B0-map-%E6%96%B9%E6%B3%95%E5%A4%B4%E6%9D%A1%E4%B8%80%E9%9D%A2)
- [求一个字符串的字节长度](#%E6%B1%82%E4%B8%80%E4%B8%AA%E5%AD%97%E7%AC%A6%E4%B8%B2%E7%9A%84%E5%AD%97%E8%8A%82%E9%95%BF%E5%BA%A6)
- [数组扁平化](#%E6%95%B0%E7%BB%84%E6%89%81%E5%B9%B3%E5%8C%96)
- [手写原生 ajax](#%E6%89%8B%E5%86%99%E5%8E%9F%E7%94%9F-ajax)

<!-- /TOC -->

### 用 Array 的 reduce 方法实现 map 方法（头条一面）

### 求一个字符串的字节长度

假设：一个英文字符占用一个字节，一个中文字符占用两个字节

```js
function GetBytes(str) {
  var len = str.length
  var bytes = len
  for (var i = 0; i < len; i++) {
    if (str.charCodeAt(i) > 255) bytes++
  }
  return bytes
}
alert(GetBytes('你好,as'))
```

### 数组扁平化

数组扁平化是指将一个多维数组变为一维数组

```js
[1, [2, 3, [4, 5]]]  ------>    [1, 2, 3, 4, 5]
```

递归法：

```js
function flatten(arr) {
  let res = []
  arr.map(item => {
    if (Array.isArray(item)) {
      res = res.concat(flatten(item))
    } else {
      res.push(item)
    }
  })
  return res
}
```

### 手写原生 ajax

简单 GET 请求

```js
function ajax(url, cb) {
  let xhr
  if (window.XMLHttpRequest) {
    xhr = new XMLHttpRequest()
  } else {
    xhr = ActiveXObject('Microsoft.XMLHTTP')
  }
  xhr.onreadystatechange = function() {
    if (xhr.readyState == 4 && xhr.status == 200) {
      cb(xhr.responseText)
    }
  }
  xhr.open('GET', url, true)
  xhr.send()
}
```

POST 请求则需要设置`RequestHeader`告诉后台传递内容的编码方式以及在 send 方法里传入对应的值

```js
xhr.open("POST", url, true);
xhr.setRequestHeader("Content-Type": "application/x-www-form-urlencoded");
xhr.send("key1=value1&key2=value2");
```



## [20道JS原理题助你面试一臂之力](https://juejin.im/post/5d2ee123e51d4577614761f8?utm_source=gold_browser_extension)

