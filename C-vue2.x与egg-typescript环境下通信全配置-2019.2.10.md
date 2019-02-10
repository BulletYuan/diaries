# egg

* **安装依赖**

```bash
npm i egg-socket.io --save
```

* **引入并配置socket**

1. `/config/plugin.ts`

```TypeScript
const plugin: EggPlugin = {
  io: {
    enable: true,
    package: 'egg-socket.io',
  },
};
```

2. `/config/config.default.ts`

```TypeScript
// 配置socket
config.io = {
    init: {}, // passed to engine.io
    namespace: {
        // 命名空间
        '/': {
        connectionMiddleware: [
            'auth', // 这个是连接中间件， 只在connection的时候触发
        ],
        packetMiddleware: [], // 这个会在每次消息的时候触发
        },
    },
};
```

* **创建io业务文件夹**

1. `/app/io` socket.io业务文件夹

2. `/app/io/controller` socket.io业务逻辑控制器

3. `/app/io/middleware` socket.io中间件

* **controller**

1. `/app/io/controller/hello.ts` socket接口业务

```TypeScript
import { Controller } from 'egg';

// 此处声明接口是为了弥补egg-socket.io在ts下的缺陷
// 需要声明后才可以在其他地方通过io.hello来引用该业务控制器
// 否则ts下编译时会报错，找不到app.io.xxx某接口控制器这样的错误
declare module 'egg' {
    interface CustomController {
        helloCtr: HelloController;
    }
}
export default class HelloController extends Controller {
    public async helloFn(){
        const {ctx} = this;
        const req = ctx.args[0] || {}; // 获取客户端传来的数据包
        console.log(`来自客户端的数据包：${JSON.stringify(req)}`);
        ctx.socket.emit('helloRES', {data:'hello there!'}); // 向客户端发送数据包，客户端需要监听helloRES事件
    }
}
```

* **middleware**

1. `/app/io/middleware/auth.ts` socket中间件，在config中 `connectionMiddleware` 引用，每次连接时就会调用

```TypeScript
import { Context } from 'egg';

export default function() {
    return async (ctx: Context, next: any) => {
        const {socket} = ctx;
        socket.emit('res','connected'); // 连接成功后向客户端发送数据包，客户端需要监听res事件
        await next();
    };
}
```

* **引入路由**

1. `/app/router.ts`

```TypeScript
import { Application } from 'egg';

export default (app: Application) => {
  const { io } = app;
  io.of('/').route('hello', io.controller.helloCtr.helloFn); // 客户端触发远端hello事件，服务端即可调用helloFn业务控制器事件
};
```

=======

# vue

* **安装依赖**

```bash
npm i vue-socket.io --save
npm i socket.io-client --save
```

* **引入并配置socket**

1. `/src/main.js`

```JavaScript
import Vue from 'vue';
import App from './App.vue';
import VueSocketIO from 'vue-socket.io';
import socketio from 'socket.io-client';

const SocketInstance = socketio.connect('http://127.0.0.1:7001'); // 服务端ip地址及端口
Vue.use(new VueSocketIO({
    debug: false,
    connection: SocketInstance
})); // 初始化VueSocketIO对象
// 很多教程直接用 Vue.use(VueSocketIO, socketio('http://127.0.0.1:3000/'), store);
// 我这里会报错，提示VueSocketIO是类而不是方法，所以就先实例化，参数可以参考 https://www.npmjs.com/package/vue-socket.io

Vue.config.productionTip = false

new Vue({
  render: h => h(App),
}).$mount('#app');
```

* **监听及调用事件**

1. `/src/App.vue`

```typescript
export default {
  name: "app",
  sockets:{
    connect(){
          console.log(`连接上服务端ws服务了`);
    },
    // 这里更圆润一些，还可以监听error(){},disconnect(){}等这样的异常事件回调
    res(v) {
      console.log(`监听服务端返回res事件的返回值:${v}`);
    },
    helloRES(v){
      console.log(`监听服务端返回helloRES事件的返回值:${v}`);
    }
  },
  methods:{
    start(){
        this.$socket.emit(`hello`, {
        value:'hello there'
      });
    }
  }
}
```