[合集 - Vona：真正好用的Node.js框架(16)](https://github.com)

[1.快来玩玩便捷、高效的Demo练习场06-17](https://github.com/zhennann/p/18932602)[2.使用这个model操作数据库，一爽到底06-30](https://github.com/zhennann/p/18958238)[3.Prisma不能优雅的支持DTO，试试Vona ORM吧08-05](https://github.com/zhennann/p/19023597)[4.如何基于动态关系进行ORM关联查询，并动态推断DTO？08-08](https://github.com/zhennann/p/19028145)[5.这个Database Transaction功能多多，你用过吗？08-21](https://github.com/zhennann/p/19050467)[6.Node.js 主流ORM框架动态分表方案大盘点08-24](https://github.com/zhennann/p/19055594)[7.能够动态推断与生成DTO是Node生态的一个重要里程碑09-04](https://github.com/zhennann/p/19072969)[8.在Vona ORM中实现多数据库/多数据源09-24](https://github.com/zhennann/p/19108669)[9.Vona ORM分表全攻略09-25](https://github.com/zhennann/p/19111296)[10.VonaJS多租户同时支持共享模式和独立模式09-26](https://github.com/zhennann/p/19113145)[11.VonaJS提供的读写分离，直观，优雅🌼09-29](https://github.com/zhennann/p/19118176):[nuts坚果](https://zhongshanyuan.com)[12.Node生态中最优雅的数据库事务处理机制09-30](https://github.com/zhennann/p/19120080)[13.AOP编程有三大场景：控制器切面，内部切面，外部切面，你get到了吗？10-10](https://github.com/zhennann/p/19132514)[14.VonaJS AOP编程：全局中间件全攻略10-11](https://github.com/zhennann/p/19134406)[15.VonaJS AOP编程：魔术方法10-23](https://github.com/zhennann/p/19159543)

16.VonaJS AOP编程大杀器：外部切面10-27

收起

在VonaJS框架中，AOP编程包括三方面：`控制器切面`、`内部切面`和`外部切面`。

1. `控制器切面`: 为 Controller 方法切入逻辑，包括：Middleware、Guard、Interceptor、Pipe和Filter
2. `内部切面`: 在 Class 内部，为任何 Class 的任何方法切入逻辑，包括：AOP Method和魔术方法
3. `外部切面`: 在不改变 Class 源码的前提下，从外部为任何 Class 的任何方法切入逻辑

VonaJS中的`外部切面`，可以类比于Spring Boot中的`AOP切面`和`AOP织入`概念。VonaJS的`外部切面`不需要什么`前置通知`、`后置通知`、`异常通知`和`环绕通知`，只需提供一个同名方法就可以了。之所以可以这么简洁，是因为使用了洋葱圈模型。

此外，VonaJS的`外部切面`支持完整的类型推断与智能代码提示，开发体感比Spring Boot优雅太多。

下面，我们就来考察一下VonaJS的`外部切面`到底是个什么样？为什么可以成为AOP编程的🚀大杀器🔪

## 创建目标Class

可以针对任何 Class 实现外部切面。下面，以 Service 为例，在模块 demo-student 中创建一个 Service `test`，代码如下：

```
@Service()
export class ServiceTest extends BeanBase {
  private _name: string;

  protected __init__() {
    this._name = '';
  }

  protected async __dispose__() {
    this._name = '';
  }

  get name() {
    return this._name;
  }

  set name(value) {
    this._name = value;
  }

  actionSync(a: number, b: number) {
    return a + b;
  }

  async actionAsync(a: number, b: number) {
    return Promise.resolve(a + b);
  }
}
```

## 创建外部切面

接下来，创建一个外部切面`log`，为 Class `ServiceTest`的属性和方法分别提供扩展逻辑

### 1. Cli命令

```
$ vona :create:bean aop log --module=demo-student
```

### 2. 菜单命令

```
右键菜单 - [模块路径]: Vona Aspect/Aop
```

## AOP定义

```
import { BeanAopBase } from 'vona';
import { Aop } from 'vona-module-a-aspect';

@Aop({ match: 'demo-student.service.test' })
export class AopLog extends BeanAopBase {}
```

* `@Aop`: 此装饰器用于实现`外部切面`
* `match`: 用于将 Class `AopLog`与 Class `ServiceTest`关联，`ServiceTest`的 beanFullName 是`demo-student.service.test`

| 名称 | 类型 | 说明 |
| --- | --- | --- |
| match | string|regexp|(string|regexp)[] | 针对哪些 Class 启用 |

## 切面：同步方法

为`ServiceTest#actionSync`输出运行时长

在 VSCode 编辑器中，输入代码片段`aopactionsync`，自动生成代码骨架:

```
action: AopAction<ClassSome, 'action'> = (_args, next, _receiver) => {
  return next();
};
```

调整代码，然后添加 log 逻辑

```
actionSync: AopAction<ServiceTest, 'actionSync'> = (_args, next, _receiver) => {
  const timeBegin = Date.now();
  const res = next();
  const timeEnd = Date.now();
  console.log('actionSync: ', timeEnd - timeBegin);
  return res;
};
```

* `actionSync`: 提供与`ServiceTest`同名的方法`actionSync`

## 切面：异步方法

为`ServiceTest#actionAsync`输出运行时长

在 VSCode 编辑器中，输入代码片段`aopaction`，自动生成代码骨架:

```
action: AopAction<ClassSome, 'action'> = async (_args, next, _receiver) => {
  return await next();
};
```

调整代码，然后添加 log 逻辑

```
actionAsync: AopAction<ServiceTest, 'actionAsync'> = async (_args, next, _receiver) => {
  const timeBegin = Date.now();
  const res = await next();
  const timeEnd = Date.now();
  console.log('actionAsync: ', timeEnd - timeBegin);
  return res;
};
```

* `actionAsync`: 提供与`ServiceTest`同名的方法`actionAsync`

## 切面：getter

为`ServiceTest#get name`输出运行时长

在 VSCode 编辑器中，输入代码片段`aopgetter`，自动生成代码骨架:

```
protected __get_xxx__: AopActionGetter<ClassSome, 'xxx'> = function (next, _receiver) {
  const value = next();
  return value;
};
```

调整代码，然后添加 log 逻辑

```
protected __get_name__: AopActionGetter<ServiceTest, 'name'> = function (next, _receiver) {
  const timeBegin = Date.now();
  const value = next();
  const timeEnd = Date.now();
  console.log('get name: ', timeEnd - timeBegin);
  return value;
};
```

* `__get_name__`: 对应`ServiceTest`的 getter 方法`get name`

## 切面：setter

为`ServiceTest#set name`输出运行时长

在 VSCode 编辑器中，输入代码片段`aopsetter`，自动生成代码骨架:

```
protected __set_xxx__: AopActionSetter<ClassSome, 'xxx'> = function (value, next, _receiver) {
  return next(value);
}
```

调整代码，然后添加 log 逻辑

```
protected __set_name__: AopActionSetter<ServiceTest, 'name'> = function (value, next, _receiver) {
  const timeBegin = Date.now();
  const res = next(value);
  const timeEnd = Date.now();
  console.log('set name: ', timeEnd - timeBegin);
  return res;
};
```

* `__set_name__`: 对应`ServiceTest`的 setter 方法`set name`

## 切面：`__init__`

为`ServiceTest#__init__`输出运行时长

在 VSCode 编辑器中，输入代码片段`aopinit`，自动生成代码骨架:

```
protected __init__: AopActionInit<ClassSome> = (_args, next, _receiver) => {
  next();
};
```

调整代码，然后添加 log 逻辑

```
protected __init__: AopActionInit<ServiceTest> = (_args, next, _receiver) => {
  const timeBegin = Date.now();
  next();
  const timeEnd = Date.now();
  console.log('__init__: ', timeEnd - timeBegin);
};
```

* `__init__`: 提供与`ServiceTest`同名的方法`__init__`

## 切面：`__dispose__`

为`ServiceTest#__dispose__`输出运行时长

在 VSCode 编辑器中，输入代码片段`aopdispose`，自动生成代码骨架:

```
protected __dispose__: AopActionDispose<ClassSome> = async (_args, next, _receiver) => {
  await next();
};
```

调整代码，然后添加 log 逻辑

```
protected __dispose__: AopActionDispose<ServiceTest> = async (_args, next, _receiver) => {
  const timeBegin = Date.now();
  await next();
  const timeEnd = Date.now();
  console.log('__dispose__: ', timeEnd - timeBegin);
};
```

* `__dispose__`: 提供与`ServiceTest`同名的方法`__dispose__`

## 切面：`__get__`

为`ServiceTest`扩展魔术方法

* 参见: [魔术方法](https://github.com)

在 VSCode 编辑器中，输入代码片段`aopget`，自动生成代码骨架:

```
protected __get__: AopActionGet<ClassSome> = (_prop, next, _receiver) => {
  const value = next();
  return value;
};
```

调整代码，然后添加自定义字段`red`

```
protected __get__: AopActionGet<ServiceTest> = (prop, next, _receiver) => {
  if (prop === 'red') return '#FF0000';
  const value = next();
  return value;
};
```

* `__get__`: 约定的魔术方法名称

通过接口类型合并的机制为颜色提供类型定义

```
declare module 'vona-module-demo-student' {
  export interface ServiceTest {
    red: string;
  }
}
```

## 切面：`__set__`

为`ServiceTest`扩展魔术方法

* 参见: [魔术方法](https://github.com)

在 VSCode 编辑器中，输入代码片段`aopset`，自动生成代码骨架:

```
protected __set__: AopActionSet<ClassSome> = (_prop, value, next, _receiver) => {
  return next(value);
};
```

调整代码，为自定义字段`red`设置值

```
private _colorRed: string | undefined;

protected __set__: AopActionSet<ServiceTest> = (prop, value, next, _receiver) => {
  if (prop === 'red') {
    this._colorRed = value;
    return true;
  }
  return next(value);
};
```

* `__set__`: 约定的魔术方法名称
* 如果为`prop`设置了值，返回`true`，否则调用`next`方法

然后调整`__get__`的逻辑:

```
protected __get__: AopActionGet = (prop, next, _receiver) => {
- if (prop === 'red') return '#FF0000';
+ if (prop === 'red') return this._colorRed;
  const value = next();
  return value;
}
```

## 切面：`__method__`

为`ServiceTest`的任何方法扩展逻辑

在 VSCode 编辑器中，输入代码片段`aopmethod`，自动生成代码骨架:

```
protected __method__: AopActionMethod<ClassSome> = (_method, _args, next, _receiver) => {
  return next();
};
```

调整代码，然后为方法`actionSync`和`actionAsync`添加 log 逻辑

```
protected __method__: AopActionMethod<ServiceTest> = (method, _args, next, _receiver) => {
  if (method !== 'actionSync' && method !== 'actionAsync') {
    return next();
  }
  const timeBegin = Date.now();
  function done(res) {
    const timeEnd = Date.now();
    console.log(`method ${method}: `, timeEnd - timeBegin);
    return res;
  }
  const res = next();
  if (res?.then) {
    return res.then((res: any) => {
      return done(res);
    });
  }
  return done(res);
};
```

* `__method__`: 约定的魔术方法名称
* `res?.then`: 判断返回值是否是 Promise 对象，进行不同处理，从而兼容`同步方法`和`异步方法`

## AOP顺序

针对同一个目标 Class，可以关联多个 AOP。所以，VonaJS 提供了两个参数，用于控制 AOP 的执行顺序

### 1. dependencies

比如，还有一个 AOP `demo-student:log3`，我们希望执行顺序如下：`demo-student:log3` > `Current`

```
@Aop({
  match: 'demo-student.service.test',
+ dependencies: 'demo-student:log3',
})
class AopLog {}
```

### 2. dependents

`dependents`的顺序刚好与`dependencies`相反，我们希望执行顺序如下：`Current` > `demo-student:log3`

```
@Aop({
  match: 'demo-student.service.test',
+ dependents: 'demo-student:log3',
})
class AopLog {}
```

## AOP启用/禁用

可以控制 AOP 的`启用/禁用`

### 1. Enable

`src/backend/config/config/config.ts`

```
// onions
config.onions = {
  aop: {
    'demo-student:log': {
+     enable: false,
    },
  },
};
```

### 2. Meta

可以让 AOP 在指定的运行环境生效

| 名称 | 类型 | 说明 |
| --- | --- | --- |
| flavor | string|string[] | 参见: [运行环境与Flavor](https://github.com) |
| mode | string|string[] | 参见: [运行环境与Flavor](https://github.com) |

* 举例

```
@Aop({
+ meta: {
+   flavor: 'normal',
+   mode: 'dev',
+ },
})
class AopLog {}
```

## 查看当前生效的AOP清单

可以直接在目标 Class action 中输出当前生效的 AOP 清单

```
class ServiceTest {
  protected async __dispose__() {
+   this.bean.onion.aop.inspect();
    this._name = '';
  }
```

* `this.bean.onion`: 取得全局 Service 实例 `onion`
* `.aop`: 取得与 AOP 相关的 Service 实例
* `.inspect`: 输出当前生效的 AOP 清单

当方法被执行时，会自动在控制台输出当前生效的 AOP 清单，效果如下：

![aop-1]()

## 资源

* Github：[https://github.com/vonajs/vona](https://github.com)
* 文档：[https://vona.js.org/](https://github.com)
