# Vue 基础

特点：组件化模式、声明式编码、使用虚拟 DOM+优秀的 diff 算法

## 1.初识 Vue

```js
Vue.config.productionTip = false; // 关闭Vue生产提示
```

1. 创建 Vue 实例

   ```js
   new Vue({
     el: "#root", //el用于指定当前Vue实例为哪个容器服务，值通常为css选择器字符串
     data: {
       // data中用于存储数据，数据供el所指定的容器去使用
       name: "尚硅谷",
     },
   });
   ```

2. 总结

   1. 想让 Vue 工作，就必须创建一个 Vue 实例，且要传入一个配置对象；
   2. root 容器里的代码依然符合 html 规范，只不过混入了一些特殊的 Vue 语法；
   3. root 容器里的代码被称为【Vue 模板】；
   4. Vue 实例和容器是一一对应的；
   5. 真实开发中只有一个 Vue 实例，并且会配合着组件一起使用；
   6. {{xxx}}中的 xxx 要写 js 表达式，且 xxx 可以自动读取到 data 中的所有属性；
   7. 一旦 data 中的数据发生改变，那么模板中用到该数据的地方也会自动更新；

   ```
   注意区分：js表达式 和 js代码(语句)
      1. 表达式：一个表达式会产生一个值，可以放在任何一个需要值的地方：
          (1). a
          (2). a+b
          (3). demo(1)
          (4). x === y ? 'a' : 'b'

      2. js代码(语句)
          (1). if(){}
          (2). for(){}
   ```

## 2.模板语法

    Vue模板语法有2大类：
      1. 插值语法：
          功能：用于解析标签体内容。
          写法：{{xxx}}，xxx是js表达式，且可以直接读取到data中的所有属性。
      2. 指令语法：
          功能：用于解析标签(包括：标签属性、标签体内容、绑定事件......)。
          举例：v-bind:href="xxx" 或 简写为 :href="xxx"，xxx同样要写表达式，且可以直接读取到data中的所有属性。
      备注：Vue中有很多的指令，且形式都是 v-xxx

## 3.数据绑定

```
<!-- 普通写法 -->
单向数据绑定：<input type="text" v-bind:value="name"><br />
双向数据绑定：<input type="text" v-model:value="name">

<!-- 简写 -->
单向数据绑定：<input type="text" :value="name"><br />
双向数据绑定：<input type="text" v-model="name">
```

> `v-model` 只能应用在表单类元素中(输入类元素)

总结：

Vue 中有两种数据绑定的方式：

1.  单向绑定(`v-bind`)：数据值能从 data 流向页面。
2.  双向绑定(`v-model`)：数据不仅能从 data 流向页面，还可以从页面流向 data。

备注：

- 双向绑定一般都应用在表单类元素上（如：input、select 等）
- v-model:value 可以简写为 v-model，因为 v-model 默认手机的就是 value 值。

## 4.el 与 data 的两种写法

1.  el 有两种写法
    (1). new Vue 时配置 el 属性。

    ```js
    const vm = new Vue({
      el: "#root", // 第一种写法
    });
    ```

    (2). 先创建 Vue 实例，随后再通过 vm.$mount('#root')指定 el 的值。

    ```js
    const vm = new Vue({});

    vm.$mount("#root"); // 第二种写法
    ```

2.  data 有两种写法
    (1). 对象式

    ```js
    new Vue({
      el: "#root",
      // data的第一种写法：对象式
      data: {
        name: "尚硅谷",
      },
    });
    ```

    (2). 函数式

    ```js
    new Vue({
      el: "#root",
      data() {
        return {
          name: "尚硅谷",
        };
      },
    });
    ```

    > 如何选择：目前哪种写法都可以，以后学习到组件时，data 必须使用函数式，否则会报错。

3.  一个重要的原则；

- 由 Vue 管理的函数，一定不要写箭头函数，一旦写了箭头函数，this 就不再是 Vue 实例了。

## 5.MVVM 模型

1. M：模型(Model)：data 中的数据
2. V：视图(View)：模板代码
3. VM：视图模型(ViewModel)：Vue 实例

观察发现：

1. data 中所有的属性，最后都出现在了 vm 身上。
2. vm 身上所有的属性及 Vue 原型上所有属性，在 Vue 模板中都可以直接使用。

## 6.数据代理

1.  回顾 Object.defineProperty

    ```js
    let number = 3;
    let person = {
      name: "张三",
      sex: "男",
      // age: 18
    };
    // console.log(Object.keys(person)) // name sex age

    // 添加的属性默认不可被枚举
    Object.defineProperty(person, "age", {
      // value: 18,
      // enumerable: true, // 控制属性是否可以枚举 默认：false
      // writable: true, // 控制属性是否可以被修改 默认：false
      // configurable: true, // 控制属性是否可以被删除 默认：false

      // 当有人读取person的age属性时，get函数就会被调用，且返回值就是age的值
      get() {
        console.log("有人读取age属性了");
        return number;
      },

      // 当有人修改person的age属性时，set函数(setter)就会被调用，且会收到修改的具体值
      set(value) {
        console.log("有人修改了age属性，且值是：", value);
        number = value;
      },
    });

    console.log(Object.keys(person)); // name sex

    console.log(person);
    ```

2.  数据代理

    ```js
    let obj = { x: 100 };
    let obj2 = { y: 200 };

    Object.defineProperty(obj2, "x", {
      get() {
        return obj.x;
      },
      set(value) {
        obj.x = value;
      },
    });
    ```

    > 数据代理，通过一个对象代理对另一个对象中属性的操作（读/写）

3.  Vue 中的数据代理

    (1). Vue 中的数据代理:

        通过 vm 对象来代理 data 对象中属性的操作（读/写）

    (2). Vue 中数据代理的好处：

        更加方便的操作 data 中的数据

    (3). 基本原理

        通过 Object.defineProperty()把 data 对象中所有属性添加到 vm 上。
        在 getter/setter 内部去操作（读/写）data 中对应的属性。

## 7. 事件的基本使用

> 1. 使用 v-on:xxx 或 @xxx 绑定事件，其中 xxx 是事件名；
> 2. 事件的回调需要配置在 methods 对象中，最终会在 vm 上；
> 3. methods 中配置的函数，不要用箭头函数！否则 this 就不是 vm 了；
> 4. methods 中配置的函数，都是被 Vue 所管理的函数，this 的指向是 vm 或 组件实例对象；
> 5. @click="demo" 和 @click="demo($event)" 效果一致，但后者可以传参。

## 8. 事件修饰符

Vue 的事件修饰符：

1. prevent：阻止默认事件（常用）;（e.preventDefault()）
2. stop：阻止事件冒泡（常用）;（e.stopPropagation()）
3. once：事件只触发一次（常用）;
4. capture：使用事件的 捕获模式;
5. self：只有 event.target 是当前操作的元素时才出发事件;
6. passive：事件的默认行为立即执行，无需等待事件回调执行完毕。

```
<!-- 滚动条滚动 -->
<!-- <ul @scroll="demo" class="list"> -->

<!-- 滚轮滚动 -->
<!-- <ul @wheel.passive="demo" class="list"> -->

<!-- 阻止冒泡、阻止默认事件 -->
<div @click="showInfo">
  <a href="http://www.atguigu.com" @click.stop.prevent="showInfo"></a>
</div>
```

## 9.键盘事件

```
<input type="text" placeholder="按下回车键提示输入" @keyup.13="showInfo"><br/>

<input type="text" placeholder="按下回车键提示输入" @keyup.enter="showInfo"><br/>

<input type="text" placeholder="按下回车键提示输入" @keyup.enter="showInfo"><br/>

<input type="text" placeholder="按下Ctrl+y提示输入" @keydown.ctrl.y="showInfo"><br/>

Vue.config.keyCodes.huiche = 13
<input type="text" placeholder="按下回车键提示输入" @keydown.huiche="showInfo">

console.log(e.key) // 按键名字
console.log(e.keyCode) // 按键编码
console.log(e.target.value)
```

总结：

1. Vue 中常用的按键别名：
   回车 => enter
   删除 => delete (捕获“删除”和“退格”键)
   退出 => esc
   空格 => space
   换行 => tab (特殊，必须配合 keydown 去使用)
   上 => up
   下 => down
   左 => left
   右 => right

2. Vue 未提供别名的按键，可以使用按键原始的值去绑定，但注意要转为 kebab-case(短横线命名)。

3. 系统修饰键（用法特殊）：ctrl、alt、shift、meta
   (1). 配合 keyup 使用：按下修饰键的同时，再按下其他键，随后释放其他键，事件才能被触发。
   (2). 配合 keydoem 使用：正常触发事件。

4. 也可以使用 keyCode 去指定具体的按键（不推荐）

5. Vue.config.keyCodes.自定义键名 = 键码，可以去定制按键别名

## 10.计算属性

1. 定义：要用的属性不存在，要通过已有属性计算得来。

2. 原理：底层借住了 Object.defineProperty 方法提供的 getter 和 setter。

3. get 函数什么时候执行？
   (1). 初次读取时会执行一次。
   (2). 当依赖的数据发生改变时会被再次调用。

4. 优势：与 methods 实现相比，内部有缓存机制(复用)，效率更高，调试方便。

5. 备注：
   (1). 计算属性最终会出现在 vm 上，直接读取使用即可。
   (2). 如果计算属性要被修改，那必须写 set 函数去响应修改，且 set 中要引起计算时依赖的数据发生改变。

```js
computed: {
  fullName: {
    get() {
      return this.firstName +'-'+ this.lastName
    },
    set(value) {
      this.firstName = value.split('-')[0]
      this.lastName = value.split('-')[1]
    }
  },
  // 简写
  fullName() {
    return this.firstName +'-'+ this.lastName
  }
}
```

## 11.监视属性 watch

1. 当被监视的属性变化时，回调函数自动调用，进行相关操作

2. 监视的属性必须存在，才能进行监视

3. 监视的两种写法：
   (1). new Vue 时传入 watch 配置
   (2). 通过 vm.$watch 监视

## 12.深度监视

1. Vue 中的 watch 默认不监测对象内部值的改变(一层)。

2. 配置 deep:true 可以监测对象内部值改变(多层)。

备注：  
(1). Vue 自身可以监测对象内部值的改变，但 Vue 提供的 watch 默认不可以！  
(2). 使用 watch 时根据数据的具体结构，决定是否采用深度监视。

## 13.computed 和 watch 之间的区别

1.  computed 能完成的功能，watch 都可以完成。
2.  watch 能完成的功能，computed 不一定能完成，例如：`watch 可以进行异步操作`。

两个重要的小原则：

1. `所有被 Vue 管理的函数，最好写成普通函数`，这样 this 的指向才是 vm 或 组件实例对象。
2. 所有不被 Vue 管理的函数（定时器的回调函数、ajax 的回调函数、Promise 的回调函数等），最好写成箭头函数，这样 this 的指向才是 vm 或 组件实例对象。
