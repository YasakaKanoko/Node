# <samp>Node.js</samp>

<samp>Node 环境是完全独立于浏览器 ( DOM 和 BOM )</samp>

<samp>run `node index.js` in the terminal</samp>

```js
console.log(`Hello Node.js v${process.versions.node}`); 
// Hello Node.js v22.14.0
```

<samp>在 terminal 直接键入 `node` 会打开 REPL 工具</samp>

> <samp>`Ctrl` + `D` 退出</samp>

```sh
> node
Welcome to Node.js v22.14.0.
Type ".help" for more information.

> JSON.stringify({name: 'alex'});
'{"name":"alex"}'
```

## <samp>环境</samp>

- <samp>`node`</samp>

  ```sh
  npm init -y
  
  npm i -D @types/node
  
  npm i -D nodemon
  ```

  <samp>配置</samp>

  ```js
  "scripts": {
    "start": "nodemon --watch src -e js --exec node ./src/index.js",
  }
  ```

- <samp>`bun`</samp>

  ```sh
  bun init
  ```

  <samp>bun 是一个即开即用的构建工具，在初始化时就已经下载了 `@types/node` </samp>

  <samp>可以使用 bun 内置的 `--hot` 和 `--watch` 实现热加载</samp>

  > <samp>`--hot` 是重新加载整个项目，`--watch` 是监视单文件</samp>
  
  ```js
  "scripts": {
    "start": "bun --watch run index.js",
  }
  ```

## <samp>读写文件</samp>

<samp><b>读取文件</b>：需注意这个读取操作是异步的</samp>

```js
import { readFile } from 'node:fs/promises';
import path from 'node:path';

const data = path.resolve(__dirname, 'data.json');
console.log(await readFile(data, 'utf8'));
```

<samp>执行命令</samp>

```sh
bun run index.js
```

<samp>输出结果</samp>

```js
[
  {
    "name": "Laravel",
    "tag": "PHP"
  },
  {
    "name": "Django",
    "tag": "Python"
  },
  {
    "name": "NestJS",
    "tag": "NodeJS"
  }
]
```

> <samp>在 Node.js 官网中给出的实现并不好，它存在一个历史遗留问题；没有 ES Modules 之前，一直使用的模块化方式是 commonjs ，后续为了兼容 ES 需将文件名改为 `*.mjs`</samp>

<samp><b>写入文件</b></samp>

<samp><b>覆盖</b></samp>

```js
import { readFile,writeFile } from 'node:fs/promises';
import path from 'node:path';

const data = path.resolve(__dirname, 'data.json');
await writeFile(data, 'Hello world!', 'utf8');
console.log(await readFile(data, 'utf8'));
```

<samp><b>追加</b>：追加有两种方式</samp>

<samp>一种是 `writeFile()` 增加 `flag` 参数</samp>

> <samp>设置 `writeFile()` 的 `options` 参数 `flag: 'a'` </samp>
>
> ```js
> import { readFile, writeFile } from 'node:fs/promises';
> import path from 'node:path';
> 
> const data = path.resolve(__dirname, 'data.json');
> await writeFile(data, 'Hello world!', { flag: 'a', encoding: 'utf8' });
> console.log(await readFile(data, 'utf8'));
> ```

<samp>另一种是通过 `appendFile()`</samp>

```js
import { readFile, appendFile } from 'node:fs/promises';
import path from 'node:path';

const data = path.resolve(__dirname, 'data.json');
await appendFile(data, 'Hello world!', 'utf8');
console.log(await readFile(data, 'utf8'));
```

> <samp>练习：模拟一个恺撒密码</samp>
>
> <samp>将加密数据左移或右移指定位数，返回解密的结果</samp>

```js
import { readFile, writeFile } from "node:fs/promises";
import path, { resolve } from "node:path";

const data_path = path.resolve(__dirname, 'data.txt'); // GVE^]1XLYVWHE]1Z1QI1JMJX]
const key_path = path.resolve(__dirname, 'keychain.txt'); // 4
const decrypted_data = resolve(__dirname, 'decrypted-data.txt');

// Read Files
const data = await readFile(data_path, 'utf8');
const key = await readFile(key_path, 'utf8');

// 右移
/* 思路: 将字符串通过split拆分成数组, 将数组的每一项都通过charCodeAt返回Unicode编码, 返回字符数组后再通过join返回指定字符串 */
const rightMoveDecryptedData = data.split('').map((curChar) => {
  const decryptedCharCode = curChar.charCodeAt(0) + key;
  return String.fromCharCode(decryptedCharCode);
}).join('');

// 左移
const leftMoveDecryptedData = data.split('').map((curChar) => {
  const decryptedCharCode = curChar.charCodeAt(0) - key;
  return String.fromCharCode(decryptedCharCode);
}).join('');

await writeFile(decrypted_data,leftMoveDecryptedData,'utf8');
```

## <samp>同步与异步</samp>

<samp>commonjs 与 ES Modules 的区别</samp>

- <samp>[从 commonjs 迁移到 ES Module 以便获得与浏览器有更良好的兼容性](https://typescript.tv/errors/#ts80001)</samp>

- <samp>[CommonJS loads modules synchronously, ES modules are asynchronous](https://blog.logrocket.com/commonjs-vs-es-modules-node-js/)</samp>

  > <samp>同步加载具有相互依赖关系</samp>
  >
  > <samp>参考：[异步与同步的相似与不同](https://www.mendix.com/blog/asynchronous-vs-synchronous-programming/)</samp>

<samp>更深的区别</samp>

- <samp>CommonJS 的 `require` 是同步的，加载和执行模块时会阻塞当前流程，这相当于在一个函数作用域中执行并返回 `exports` 或 `module.exports` 对象给调用者</samp>

- <samp>但是，ESM 的静态 `import` 是同步的，而 ESM 动态的 `import()` 是异步的</samp>

  > <samp>参考：[commonjs 不会消失](https://bun.sh/blog/commonjs-is-not-going-away)</samp>
  >
  > <samp>ESM 的 `import` 和 `export` 只能出现在文件顶层，而 ESM 最初设计在浏览器中运行，文件的加载需要通过**网络加载**。因此，给定一个 ES 模块，浏览器无需查看导入或导出的内容，执行前整个模块都将被解析(这会涉及往返多次网络请求)</samp>
  >
  > <samp>在大型项目中，ESM 速度较慢，与 `require` 不同在于，要么在使用语句时加载整个模块，要么导入时使用 await 表达式，这样的代码必须返回一个 Promise ( 可能带来额外的 microticks 和开销 )</samp>

<samp>Promise 形式导入</samp>

```js
import * as fs from 'node:fs/promises';
```

<samp>以回调的形式导入</samp>

```js
import * as fs from 'node:fs';
```

```js
import { readFile as readFileSync } from "node:fs";
import path from "node:path";

const data_name = path.resolve(__dirname, 'data.json');
readFileSync(data_name, 'utf8', (err, data) => {
  if (err) throw err;
  console.log(data);
});
```

## <samp>输入输出</samp>

<samp>通过 `createInterface` 创建一个接口实例，同时处理输入流和输出流，通常用于命令行工具开发</samp>

```javascript
import * as readline from 'node:readline/promises';
import { stdin, stdout } from 'node:process';

const readLine = readline.createInterface({
  input: stdin,
  output: stdout
});
const name = await readLine.question(`What's your name?\n`);
const age = await readLine.question(`How old are you?\n`);
const birthYear = new Date().getFullYear() - Number(age);

const answer = await readLine.question(
  `Welcome: ${name}, Your birth year is ${birthYear} right? (y/n)\n`
);
if (answer[0].toUpperCase() !== 'Y') {
  console.log(`I know , you were born at ${birthYear - 1}!`)
}

readLine.close();
/*
What's your name?
Alex
How old are you?
24
Welcome: Alex, Your birth year is 2001 right? (y/n)
n
I know , you were born at 2000! */
```

## <samp>服务器</samp>

<samp>请求</samp>

```xml
Request URL: https://github.com/
Request Method: GET
Status Code: 200 OK
Remote Address: 127.0.0.1:7890
Referrer Policy: strict-origin-when-cross-origin
```

<samp>响应</samp>

<samp>`Content-Type`：响应的文件类型</samp>

> <samp>如果返回 `text/html`，`Preview` 将返回的一些静态网页资源，表明该网页需要通过客户端进一步渲染</samp>

<samp><b>搭建简易服务器</b></samp>

- <samp>Node.js</samp>

  ```js
  // server.mjs
  import { createServer } from 'node:http';
  
  const server = createServer((req, res) => {
    res.writeHead(200, { 'Content-Type': 'text/plain' });
    res.end('Hello World!\n');
  });
  
  // starts a simple http server locally on port 3000
  server.listen(3000, '127.0.0.1', () => {
    console.log('Listening on http://127.0.0.1:3000');
  });
  
  // run with `node server.mjs`
  ```

  > <samp>即便不写类型，浏览器也能正确地翻译，为了兼容旧版浏览器，仍需加上类型</samp>
  >
  > <samp>[常见 MIME 类型](https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/MIME_types/Common_types)</samp>

- <samp>bun</samp>

  ```js
  const server = Bun.serve({
    port: 3000,
    fetch(req) {
      return new Response('Hello World!');
    },
  });
  console.log(`Listening on http://localhost:${server.port}`)
  ```

## <samp>路由</samp>

<samp>从上述例子中，可以发现，服务器只能返回一种类型的数据</samp>

<samp>路由：服务器根据用户访问路径的不同，返回相应的页面</samp>

```js
// server.mjs
import { createServer } from 'node:http';
import { readFile } from 'node:fs/promises';


const server = createServer(async (req, res) => {
  if (req.url === '/') {
    const htmlFile = await readFile('./index.html', 'utf8');
    res.writeHead(200, { "content-type": "text/html" });
    res.end(htmlFile);
  }
  if (req.url === '/data') {
    const jsonFile = await readFile('./data.json', 'utf8');
    res.writeHead(200, { "content-type": "application/json" });
    res.end(jsonFile);
  }
});

// port, hostname
server.listen(3000, '127.0.0.1', () => {
  console.log('Listening on http://127.0.0.1:3000');
});

// run with `node server.mjs`
```

<samp>以下是 `Bun.serve` 写法</samp>

```js
const server = Bun.serve({
  port: 3000,
  hostname: "127.0.0.1",
  async fetch(req) {
    const url = new URL(req.url);
    if (url.pathname === '/') {
      const file = Bun.file('./index.html');
      return new Response(file, {
        headers: {
          "content-type": "text/html"
        }
      });
    }
    if (url.pathname === '/data') {
      const file = Bun.file('./data.json');
      return new Response(file, {
        headers: {
          "content-type": "application/json"
        }
      });
    }
    return new Response('Not found', { status: 404 });
  }
});

console.log(`Listening on ${server.url}`);
```

<samp>通过路由，使用相对路径返回对应多媒体类的文件，这种方式是不可行的，这些媒体资源需要托管</samp>

## <samp>服务器架构</samp>

<samp>服务器渲染 (server-side rendering, SSR for short) ：指在服务器端加载组件并渲染成 HTML，然后将 HTML 发送至浏览器，用户首次加载时就能看到页面的完整内容，加快首屏加载速度</samp>

<samp>客户端渲染 (Client-side rendering, CSR for short)：也称单页应用 (Single-page application, SPA for short) 是指整个网站只有一个 HTML 文件，所有内容和路由切换都在客户端通过 JavaScript 动态处理</samp>

> <samp>参考</samp>
>
> - <samp>[什么是 CSR](https://prismic.io/blog/client-side-rendering)</samp>
> - <samp>[SPA](https://developer.mozilla.org/en-US/docs/Glossary/SPA)</samp>
> - <samp>[CSR vs SSR](https://prismic.io/blog/client-side-vs-server-side-rendering)</samp>

## <samp>Express</samp>

<samp>参考：[使用 Express 和 Bun 构建 HTTP 服务器](https://bun.sh/guides/ecosystem/express)</samp>

```sh
bun add express
```

```js
import express from 'express';

const app = express();
const port = 3000;

app.get('/', (req, res) => {
  res.send('Hello World!');
});

app.listen(port, () => {
  console.log(`Local: http://localhost:${port}`)
});
```
