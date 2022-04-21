### 命令行脚本

// ex1.mjs

##### 使用minimist包来处理参数

引入包之后，它是一个函数，第一个参数是需要处理的命令行参数`process.argv.slice(2)`，第二个参数可选，指定命令行参数的类型。比如这里的help设置为任何值都会转换为`true`，不设置的时候就是`false`

```javascript
// ex1.mjs
import minimist from "minimist";
var args = minimist(process.argv.slice(2), {
  boolean: ["help"],
  string: ["file"],
});
console.log(args)
```

```bash
$ ./ex1.mjs --hello=first -c22 --help=1 --file=2
{ _: [], help: true, hello: 'first', c: 22, file: '2' }
```



`.mjs`文件中没有`__dirname`，需要设置`const  __dirname = path.resolve()`



##### fs模块读文件

默认是异步的 `fs.readFile(path, callbackFunc(err, content))`

读的内容默认是buffer，通过 `process.stdout.write`，写到命令行的就是编码后的内容了。

在同步方法 `fs.readFileSync()`，可以在第二个参数指定编码(e.g. `"UTF-8"`)。这样直接会进行编码。

`Buffer.toString()`将buffer编码。

##### 处理stdin

使用`get-stdin` ：`import getStdin from "get-stdin"`

使用Promise处理

`getStdin().then([process-func-name])`

用`|`将stream pipe进去处理，示例： 

```bash
$ cat files/hello.txt | ./ex1.mjs -
HELLO WORLD
```

##### 设置一个临时环境变量

在命令行命令前用`name=value`的形式进行设置

在程序中，通过`process.env.name`获取值

用例：

```javascript
var BASE_PATH = path.resolve(process.env.BASE_PATH || __dirname);
// path.join(BASE_PATH, args.file) 设置读取路径
```

```bash
$ BASE_PATH=files/ ./ex1.mjs --file=hello.txt
HELLO WORLD
```

---

### Stream

// ex2.mjs



stream分为可读的和可写的



读 inStream `process.stdin`

从文件里读`fs.createReadStream(path)`

写 `outStream.pipe(process.stdout)`

##### transform stream

`import {Transform} from "stream";`

```javascript
function processFile(inStream) {
  var upperStream = new Transform({
    transform(chunk, enc, cb) {
      this.push(chunk.toString().toUpperCase());
      setTimeout(cb, 500);
      // cb();
    },
  });
  var outStream = inStream.pipe(upperStream);

  var targetStream = process.stdout;
  outStream.pipe(targetStream);
}
```

一个chunk处理一次



写到文件里

```javascript
var targetStream = fs.createWriteStream(OUTFILE); // OUTFILE is output file path
outStream.pipe(targetStream);
```

