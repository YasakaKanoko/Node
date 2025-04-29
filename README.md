# <samp>Node</samp>

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

  <samp>bun 在初始化时就已经下载了 `@types/node` 是一个即开即用的构建工具</samp>

  <samp>可以使用 bun 内置的 `--hot` 和 `--watch` 实现热加载</samp>

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

## <samp>同步与异步</samp>

<samp>commonjs 与 ES Modules 的区别</samp>

- <samp>[从 commonjs 迁移到 ES Module 以便获得与浏览器有更良好的兼容性](https://typescript.tv/errors/#ts80001)</samp>

- <samp>[CommonJS loads modules synchronously, ES modules are asynchronous](https://blog.logrocket.com/commonjs-vs-es-modules-node-js/)</samp>

  <samp>commonjs 在加载模块时是同步的，而 ES modules 是异步加载的</samp>

<samp>同步加载具有相互依赖关系</samp>

<samp>参考：[异步与同步的相似与不同](https://www.mendix.com/blog/asynchronous-vs-synchronous-programming/)</samp>

> <samp>两种导入有什么区别？</samp>

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

