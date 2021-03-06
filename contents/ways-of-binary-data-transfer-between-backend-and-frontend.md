# 前后端交互二进制数据的方式

前后端有时会需要传输二进制数据，可能来源于文件、canvas，也可能来源于 protobuf 等序列化普通对象后生成的 ArrayBuffer 或 Buffer。

### 1. post form

选择文件后提交到后端。

特点：只能传输 File；页面会被刷新

### 2. XHR2

以 jquery 为例：

```js
const formData = new FormData();
formData.append("file", file);

$.ajax({
    url: "",
    data: formData,
    processData: false,
    contentType: false,
    type: "POST",
});
```

特点：只能传输 File；页面不会被刷新

### 3. websocket

```js
var connection = new WebSocket("");
var binary = new Uint8Array(100);
connection.send(binary.buffer);
```

特点：只能传输 ArrayBuffer

### 4. socket.io

```js
const socket = io("/");
socket.emit("event", {
    foo: file1,
    bar: {
        bar1: file2,
        bar2: file3
    },
    baz: ""
});
```

特点：可以传输 File、Blob、ArrayBuffer，和它们的各种组合。

在 nodejs 服务端，都以 Buffer 的形式接收：

```js
{
    foo: buffer1,
    bar: {
        bar1: buffer2,
        bar2: buffer3
    },
    baz: ""
}
```

### 5. window.fetch

```js
const formData = new FormData();
formData.append("file", file);
fetch("", {
  method: 'POST',
  body: formData
})
```

特点：可以传输 File、Blob、ArrayBuffer
