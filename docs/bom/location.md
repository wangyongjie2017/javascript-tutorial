# Location 对象，URL 对象，URLSearchParams 对象

URL 是互联网的基础设施之一。浏览器提供了一些原生对象，用来管理 URL。

## Location 对象

`Location`对象是浏览器提供的原生对象，提供 URL 相关的信息和操作方法。通过`window.location`和`document.location`属性，可以拿到这个对象。

### 属性

`Location`对象提供以下属性。

- Location.href：整个 URL。
- Location.protocol：当前 URL 的协议，包括冒号（`:`）。
- Location.host：主机，包括冒号（`:`）和端口（默认的80端口和443端口会省略）。
- Location.hostname：主机名，不包括端口。
- Location.port：端口号。
- Location.pathname：URL 的路径部分，从根路径`/`开始。
- Location.search：查询字符串部分，从问号`?`开始。
- Location.hash：片段字符串部分，从`#`开始。
- Location.username：域名前面的用户名。
- Location.password：域名前面的密码。
- Location.origin：URL 的协议、主机名和端口。

```javascript
// 当前网址为
// http://user:passwd@www.example.com:4097/path/a.html?x=111#part1
document.location.href
// "http://user:passwd@www.example.com:4097/path/a.html?x=111#part1"
document.location.protocol
// "http:"
document.location.host
// "www.example.com:4097"
document.location.hostname
// "www.example.com"
document.location.port
// "4097"
document.location.pathname
// "/path/a.html"
document.location.search
// "?x=111"
document.location.hash
// "#part1"
document.location.username
// "user"
document.location.password
// "passwd"
document.location.origin
// "http://user:passwd@www.example.com:4097"
```

这些属性里面，只有`origin`属性是只读的，其他属性都可写。

注意，如果对`Location.href`写入新的 URL 地址，浏览器会立刻跳转到这个新地址。

```javascript
// 跳转到新网址
document.location.href = 'http://www.example.com';
```

这个特性常常用于让网页自动滚动到新的锚点。

```javascript
document.location.href = '#top';
// 等同于
document.location.hash = '#top';
```

直接改写`location`，相当于写入`href`属性。

```javascript
document.location = 'http://www.example.com';
// 等同于
document.location.href = 'http://www.example.com';
```

另外，`Location.href`属性是浏览器唯一允许跨域写入的属性，即非同源的窗口可以改写另一个窗口的`Location.href`属性，导致后者的网址跳转。`Location`的其他属性都不允许跨域写入。

### 方法

**（1）Location.assign()**

`assign`方法接受一个 URL 字符串作为参数，使得浏览器立刻跳转到新的 URL。如果参数不是有效的 URL 字符串，则会报错。

```javascript
// 跳转到新的网址
document.location.assign('http://www.example.com')
```

**（2）Location.replace()**

`replace`方法接受一个 URL 字符串作为参数，使得浏览器立刻跳转到新的 URL。如果参数不是有效的 URL 字符串，则会报错。

它与`assign`方法的差异在于，`replace`会在浏览器的浏览历史`History`里面删除当前网址，也就是说，一旦使用了该方法，后退按钮就无法回到当前网页了，相当于在浏览历史里面，使用新的 URL 替换了老的 URL。它的一个应用是，当脚本发现当前是移动设备时，就立刻跳转到移动版网页。

```javascript
// 跳转到新的网址
document.location.replace('http://www.example.com')
```

**（3）Location.reload()**

`reload`方法使得浏览器重新加载当前网址，相当于按下浏览器的刷新按钮。

它接受一个布尔值作为参数。如果参数为`true`，浏览器将向服务器重新请求这个网页，并且重新加载后，网页将滚动到头部（即`scrollTop === 0`）。如果参数是`false`或为空，浏览器将从本地缓存重新加载该网页，并且重新加载后，网页的视口位置是重新加载前的位置。

```javascript
// 向服务器重新请求当前网址
window.location.reload(true);
```

**（4）Location.toString()**

`toString`方法返回整个 URL 字符串，相当于读取`Location.href`属性。

## URL 对象

`URL`对象是浏览器的原生对象，可以用来构造、解析和编码 URL。一般情况下，通过`window.URL`可以拿到这个对象。

### 构造函数

`URL`对象本身是一个构造函数，可以生成 URL 实例。

它接受一个表示 URL 的字符串作为参数。如果参数不是合法的 URL，会报错。

```javascript
var url = new URL('http://www.example.com/index.html');
url.href
// "http://www.example.com/index.html"
```

如果参数是另一个 URL 实例，构造函数会自动读取该实例的`href`属性，作为实际参数。

如果 URL 字符串是一个相对路径，那么需要表示绝对路径的第二个参数，作为计算基准。

```javascript
var url1 = new URL('index.html', 'http://example.com');
url1.href
// "http://example.com/index.html"

var url2 = new URL('page2.html', 'http://example.com/page1.html');
url2.href
// "http://example.com/page2.html"

var url3 = new URL('..', 'http://example.com/a/b.html')
url3.href
// "http://example.com/"
```

上面代码中，返回的 URL 实例的路径都是在第二个参数的基础上，切换到第一个参数得到的。最后一个例子里面，第一个参数是`..`，表示上层路径。

### 实例属性

URL 实例的属性与`Location`对象的属性基本一致，返回当前 URL 的信息。

- URL.href：返回整个 URL
- URL.protocol：返回协议，以冒号`:`结尾
- URL.hostname：返回域名
- URL.host：返回域名与端口，包含`:`号，默认的80和443端口会省略
- URL.port：返回端口
- URL.origin：返回协议、域名和端口
- URL.pathname：返回路径，以斜杠`/`开头
- URL.search：返回查询字符串，以问号`?`开头
- URL.searchParams：返回一个`URLSearchParams`实例，该属性是`Location`对象没有的
- URL.hash：返回片段识别符，以井号`#`开头
- URL.password：返回域名前面的密码
- URL.username：返回域名前面的用户名

```javascript
var url = new URL('http://user:passwd@www.example.com:4097/path/a.html?x=111#part1');

url.href
// "http://user:passwd@www.example.com:4097/path/a.html?x=111#part1"
url.protocol
// "http:"
url.hostname
// "www.example.com"
url.host
// "www.example.com:4097"
url.port
// "4097"
url.origin
// "http://www.example.com:4097"
url.pathname
// "/path/a.html"
url.search
// "?x=111"
url.searchParams
// URLSearchParams {}
url.hash
// "#part1"
url.password
// "passwd"
url.username
// "user"
```

这些属性里面，只有`origin`属性是只读的，其他属性都可写。

```javascript
var url = new URL('http://example.com/index.html#part1');

url.pathname = 'index2.html';
url.href // "http://example.com/index2.html#part1"

url.hash = '#part2';
url.href // "http://example.com/index2.html#part2"
```

上面代码中，改变 URL 实例的`pathname`属性和`hash`属性，都会实时反映在 URL 实例当中。

### 静态方法

**（1）URL.createObjectURL()**

`URL.createObjectURL`方法用来为上传/下载的文件、流媒体文件生成一个 URL 字符串。这个字符串代表了`File`对象或`Blob`对象的 URL。

```javascript
// HTML 代码如下
// <div id="display"/>
// <input
//   type="file"
//   id="fileElem"
//   multiple
//   accept="image/*"
//   onchange="handleFiles(this.files)"
//  >
var div = document.getElementById('display');

function handleFiles(files) {
  for (var i = 0; i < files.length; i++) {
    var img = document.createElement('img');
    img.src = window.URL.createObjectURL(files[i]);
    div.appendChild(img);
  }
}
```

上面代码中，`URL.createObjectURL`方法用来为上传的文件生成一个 URL 字符串，作为`<img>`元素的图片来源。

注意，每次使用`URL.createObjectURL`方法，都会在内存里面生成一个 URL 实例。如果不再需要该方法生成的 URL 字符串，为了节省内存，可以使用`URL.revokeObjectURL()`方法释放这个实例。

**（2）URL.revokeObjectURL()**

`URL.revokeObjectURL`方法用来释放`URL.createObjectURL`方法生成的 URL 实例。它的参数就是`URL.createObjectURL`方法返回的 URL 字符串。

下面为上一段的示例加上`URL.revokeObjectURL()`。

```javascript
var div = document.getElementById('display');

function handleFiles(files) {
  for (var i = 0; i < files.length; i++) {
    var img = document.createElement('img');
    img.src = window.URL.createObjectURL(files[i]);
    div.appendChild(img);
    img.onload = function() {
      window.URL.revokeObjectURL(this.src);
    }
  }
}
```

上面代码中，一旦图片加载成功以后，为本地文件生成的 URL 字符串就没用了，于是可以在`img.onload`回调函数里面，通过`URL.revokeObjectURL`方法卸载这个 URL 实例。

## URLSearchParams 对象

`URLSearchParams`对象是浏览器的原生对象，用来构造、解析和处理 URL 的查询字符串。

它本身也是一个构造函数，可以生成实例。参数可以为查询字符串，起首的问号`?`有没有都行，也可以为对应查询字符串的数组或对象。

```javascript
// 方法一：传入字符串
var params = new URLSearchParams('?foo=1&bar=2');
// 等同于
var params = new URLSearchParams(document.location.search);

// 方法二：传入数组
var params = new URLSearchParams([['foo', 1], ['bar', 2]]);

// 方法三：传入对象
var params = new URLSearchParams({'foo' : 1 , 'bar' : 2});
```

`URLSearchParams`会对查询字符串自动编码。

```javascript
var params = new URLSearchParams({'foo': '你好'});
params.toString() // "foo=%E4%BD%A0%E5%A5%BD"
```

上面代码中，`foo`的值是汉字，`URLSearchParams`对其自动进行 URL 编码。

`URLSearchParams`实例有遍历器接口，可以用`for...of`循环遍历（详见《ES6 标准入门》的《Iterator》一章）。

```javascript
var params = new URLSearchParams({'foo': 1 , 'bar': 2});
for (var p of params) {
  console.log(p[0] + ': ' + p[1]);
}
// foo: 1
// bar: 2
```

`URLSearchParams`没有实例属性，只有实例方法。

## URLSearchParams.toString()

`toString`方法返回实例的字符串形式。

```javascript
var url = new URL('https://example.com?foo=1&bar=2');
var params = new URLSearchParams(url.search);

params.toString() // "foo=1&bar=2'
```

### URLSearchParams.append()

`append`方法用来追加一个查询参数。它接受两个参数，第一个为键名，第二个为键值，没有返回值。

```javascript
var params = new URLSearchParams({'foo': 1 , 'bar': 2});
params.append('baz', 3);
params.toString() // "foo=1&bar=2&baz=3"
```

`append`方法不会识别是否键名已经存在。

```javascript
var params = new URLSearchParams({'foo': 1 , 'bar': 2});
params.append('foo', 3);
params.toString() // "foo=1&bar=2&foo=3"
```

上面代码中，查询字符串里面`foo`已经存在了，但是`append`依然会追加一个同名键。

### URLSearchParams.delete()

`delete`方法用来删除指定的查询参数。它接受键名作为参数。

```javascript
var params = new URLSearchParams({'foo': 1 , 'bar': 2});
params.delete('bar');
params.toString() // "foo=1"
```

### URLSearchParams.has()

`has`方法返回一个布尔值，表示查询字符串是否包含指定的键名。

```javascript
var params = new URLSearchParams({'foo': 1 , 'bar': 2});
params.has('bar') // true
params.has('baz') // false
```

### URLSearchParams.set()

`set`方法用来设置查询字符串的键值。

它接受两个参数，第一个是键名，第二个是键值。如果是已经存在的键，键值会被改写，否则会被追加。

```javascript
var params = new URLSearchParams('?foo=1');
params.set('foo', 2);
params.toString() // "foo=2"
params.set('bar', 3);
params.toString() // "foo=2&bar=3"
```

上面代码中，`foo`是已经存在的键，`bar`是还不存在的键。

### URLSearchParams.get()，URLSearchParams.getAll()

`get`方法用来读取查询字符串里面的指定键。它接受键名作为参数。

```javascript
var params = new URLSearchParams('?foo=1');
params.get('foo') // "1"
params.get('bar') // bar
```

两个地方需要注意。第一，它返回的是字符串，如果原始值是数值，需要转一下类型；第二，如果指定的键名不存在，返回值是`null`。

`getAll`方法返回一个数组，成员是指定键的所有键值。它接受键名作为参数。

```javascript
var params = new URLSearchParams('?foo=1&foo=2');
params.getAll('foo') // ["1", "2"]
```

上面代码中，查询字符串有两个`foo`键，`getAll`返回的数组就有两个成员。

### URLSearchParams.sort()

`sort`方法对查询字符串里面的键进行排序，规则是按照 Unicode 码点从小到大排列。

该方法没有返回值，或者说返回值是`undefined`。

```javascript
var params = new URLSearchParams('c=4&a=2&b=3&a=1');
params.sort();
params.toString() // "a=2&a=1&b=3&c=4"
```

上面代码中，如果有两个同名的键`a`，它们之间不会排序，而是保留原始的顺序。

### URLSearchParams.keys()，URLSearchParams.values()，URLSearchParams.entries()

这三个方法都返回一个遍历器对象，供`for...of`循环消费。它们的区别在于，`keys`方法返回的是键名的遍历器，`values`方法返回的是键值的遍历器，`entries`返回的是键值的遍历器。

```javascript
var params = new URLSearchParams('a=1&b=2');

for(var p of params.keys()) {
  console.log(p);
}
// a
// b

for(var p of params.values()) {
  console.log(p);
}
// 1
// 2

for(var p of params.entries()) {
  console.log(p);
}
// ["a", "1"]
// ["b", "2"]
```

如果直接对`URLSearchParams`进行遍历，其实内部调用的就是`entries`接口。

```javascript
for (var p of params) {}
// 等同于
for (var p of params.entries()) {}
```
