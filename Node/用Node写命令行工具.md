### 命令的入口文件：scripts/frame.js
和普通js文件的不同点就是要加一句： #!/usr/bin/env node
```javascript
#!/usr/bin/env node

console.log('afasfasd')
```
### package.json文件中定义bin
```json
{
  "bin": {
    "frame": "scripts/frame.js"
  },
  "name": "frame",
  "version": "1.0.0",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "description": ""
}
```
### 链接node程序到全局
执行 `npm link`

会在`/usr/local/bin`下生成一个全局命令`frame`

该命令的库文件在 `/usr/local/lib/node_modules/frame`里面



### uuid 实例

```javascript
#!/usr/bin/env node
var assert = require('assert');

function usage() {
  console.log('Usage:');
  console.log('  uuid');
  console.log('  uuid v1');
  console.log('  uuid v3 <name> <namespace uuid>');
  console.log('  uuid v4');
  console.log('  uuid v5 <name> <namespace uuid>');
  console.log('  uuid --help');
  console.log('\nNote: <namespace uuid> may be "URL" or "DNS" to use the corresponding UUIDs defined by RFC4122');
}

var args = process.argv.slice(2);

if (args.indexOf('--help') >= 0) {
  usage();
  process.exit(0);
}
var version = args.shift() || 'v4';

switch (version) {
  case 'v1':
    var uuidV1 = require('../v1');
    console.log(uuidV1());
    break;

  case 'v3':
    var uuidV3 = require('../v3');

    var name = args.shift();
    var namespace = args.shift();
    assert(name != null, 'v3 name not specified');
    assert(namespace != null, 'v3 namespace not specified');

    if (namespace == 'URL') namespace = uuidV3.URL;
    if (namespace == 'DNS') namespace = uuidV3.DNS;

    console.log(uuidV3(name, namespace));
    break;

  case 'v4':
    var uuidV4 = require('../v4');
    console.log(uuidV4());
    break;

  case 'v5':
    var uuidV5 = require('../v5');

    var name = args.shift();
    var namespace = args.shift();
    assert(name != null, 'v5 name not specified');
    assert(namespace != null, 'v5 namespace not specified');

    if (namespace == 'URL') namespace = uuidV5.URL;
    if (namespace == 'DNS') namespace = uuidV5.DNS;

    console.log(uuidV5(name, namespace));
    break;

  default:
    usage();
    process.exit(1);
}

```

