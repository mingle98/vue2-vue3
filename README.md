**前端必备工具推荐网站(免费图床、API和ChatAI等实用工具):**
[http://luckycola.com.cn/](http://luckycola.com.cn/)



# 一、vue3是什么?
   Vue  是一款用于构建用户界面的 JavaScript 框架。它基于标准 HTML、CSS 和 JavaScript 构建，
并提供了一套声明式的、组件化的编程模型，帮助你高效地开发用户界面。
        vuejs版本在2.7之后版本我们认为他是优化和重构的性能语法更优秀且易用的新版本,
我们一般就认为他是vue3,相比较过去的vue2的旧版本,它更加容易维护,具有更加丰富的API和语法糖,
也支持了typescript技术的使用.当然它一些新特性和功能的源码实现也和旧版相比较差异较大.

**友情链接:**
vue2官网:https://v2.cn.vuejs.org/
vue3官网:https://cn.vuejs.org/

# 二、vue3与vue2的生命周期差异对比
(1)、vue的生命周期图
![在这里插入图片描述](https://img-blog.csdnimg.cn/b7516e79127b4292b9a532e514ac0121.png#pic_center)


(2)、vue3与vue2在生命周期中的差异
![在这里插入图片描述](https://img-blog.csdnimg.cn/c9e615dfd9014f75b5e5880b87f4ebad.png#pic_center)


![在这里插入图片描述](https://img-blog.csdnimg.cn/f528212c66764ecbb9ccb34bc8841112.png#pic_center)


# 三、vue3主要的特性有哪些?


vue3的主要特点是:更小、更快、更易维护

## (一)、更快:
(1)、重构了Virtual DOM
Vue3重写了虚拟DOM的实现方法，初始渲染/更新可以提速达100%。
对于Vue2.x版本的虚拟DOM来说，Vue会遍历<template>:模板中的所有内容，并根据这些标签生成对应的虚拟DOM(虚拟DOM一般指采用key/value对象来保存标签元素的属性和内容)，当有内容改变时，遍历虚拟DOM来dff找到对应的标签元素所对应的DOM节点，并改变其内容。
而vue3增加了静态标记,改变原有vue2全量虚拟dom对比的性能负担
* vue2从根节点开始对虚拟dom进行全量对比（每个节点不论写死的还是动态的都会一层一层比较）
* vue3新增了静态标记 与上次虚拟dom对比的时候，只对比带有 patchFlags 的节点。跳过一些静态节点对比（下图编译结果中-1跟1就属于静态标记）
```js
// patchFlags 字段类型列举
export const enum PatchFlags { 
  TEXT = 1,   // 动态文本内容
  CLASS = 1 << 1,   // 动态类名
  STYLE = 1 << 2,   // 动态样式
  PROPS = 1 << 3,   // 动态属性，不包含类名和样式
  FULL_PROPS = 1 << 4,   // 具有动态 key 属性，当 key 改变，需要进行完整的 diff 比较
  HYDRATE_EVENTS = 1 << 5,   // 带有监听事件的节点
  STABLE_FRAGMENT = 1 << 6,   // 不会改变子节点顺序的 fragment
  KEYED_FRAGMENT = 1 << 7,   // 带有 key 属性的 fragment 或部分子节点
  UNKEYED_FRAGMENT = 1 << 8,   // 子节点没有 key 的fragment
  NEED_PATCH = 1 << 9,   // 只会进行非 props 的比较
  DYNAMIC_SLOTS = 1 << 10,   // 动态的插槽
  HOISTED = -1,   // 静态节点，diff阶段忽略其子节点
  BAIL = -2   // 代表 diff 应该结束
}
```


## (一)、vue3中虚拟dom的优化做了哪些事:

### (1.1) 基础虚拟dom创建
当我们创建一个这样的静态 dom 元素的时候：
![在这里插入图片描述](https://img-blog.csdnimg.cn/95da957ee16a4fbfb28c036622ecd130.png#pic_center)

Vue3 给我们编译后的 Vdom 是这个样子的：
![在这里插入图片描述](https://img-blog.csdnimg.cn/75748aefdd524cdab9b1d1e422e83418.png#pic_center)

实际上 _createBlock 函数中才是我们创建的 dom，从它身上我们可以看出，我们创建了一个 span 元素，内容为 “Hello World!”。这就是 Vdom 最基础的形式，在这里我们并不会感觉到 Vue3 与 Vue2 有什么不同。

### (1.2) patch flag 优化静态树

当我们创建了一个动态的 dom 元素：
![在这里插入图片描述](https://img-blog.csdnimg.cn/27472455fab64378a4c22a7bbbbda070.png#pic_center)


Vue3 编译后的 Vdom 是这个样子的：
![在这里插入图片描述](https://img-blog.csdnimg.cn/4f86b4b0d89647afbd5dedc50de9117e.png#pic_center)



我们发现创建动态 dom 元素的时候，Vdom 除了模拟出来了它的基本信息之外，还给它加了一个标记： 1 /* TEXT */
这个标记就叫做 patch flag（补丁标记）
patch flag 的强大之处在于，当你的 diff 算法走到 _createBlock 函数的时候，会忽略所有的静态节点，只对有标记的动态节点进行对比，而且在多层的嵌套下依然有效。
尽管 JavaScript 做 Vdom 的对比已经非常的快，但是 patch flag 的出现还是让 Vue3 的 Vdom 的性能得到了很大的提升，尤其是在针对大组件的时候。

### (1.3) patch flag 优化静态属性
当我们创建一个有属性的元素：
![在这里插入图片描述](https://img-blog.csdnimg.cn/f344b53093ff49bca6b630b7dc0a2035.png#pic_center)

Vue3 编译后的 Vdom 是这个样子的:
![在这里插入图片描述](https://img-blog.csdnimg.cn/365acc95c3c84cd08e110b7bb9875125.png#pic_center)


让我们观察它的 patch flag ，发现并没有对 id 做特殊的标记。是因为 dom 元素的静态属性在渲染的时候就已经创建了，并且是不会变动的，在后面进行更新的时候，diff 算法是不会去管它的。

### (1.4) 动态绑定
我们创建一个属性是动态绑定的元素:
![在这里插入图片描述](https://img-blog.csdnimg.cn/dda6c82d542c47c7a887cf56a126117d.png#pic_center)

Vue3 编译后的 Vdom 是这个样子的：
![在这里插入图片描述](https://img-blog.csdnimg.cn/d24026c0ae0a4dc89ec3087c7f1f0d3e.png#pic_center)


再观察它的 patch flag ，会发现变成了 9 /* TEXT, PROPS */，而且后边多了一个数组 ["id"]
这里的 patch flag 中的注释的内容告诉我们，这一个 dom 元素不止有内容 TEXT 会变化，它的属性 PROPS 也会变化。而后边的数组中的内容则是有可能发生变化的属性。

Vue3 在 Vdom 的更新时，只会关注它有变化的部分。这样的优化使 Vue3 既跳出了 Vdom 的性能瓶颈，又依然保留了可以手写 render function 的灵活性。相当于 Vue3 既有 react 的灵活性，又有基于模板的性能保证。——尤雨溪

## (二)、数据更新之后就会执行 patch 函数，下图就是 patch 函数执行的逻辑图：


## (三)、如何创建虚拟dom
vue3对外提供了h()方法用于创建虚拟DOM。所在文件路径：
```js
packages/runtime-core/src/h.ts
export function h(type: any, propsOrChildren?: any, children?: any): VNode {
  const l = arguments.length
  if (l === 2) {
    // propsOrChildren是对象且不是数组
    if (isObject(propsOrChildren) && !isArray(propsOrChildren)) {
      // propsOrChildren是vnode
      if (isVNode(propsOrChildren)) {
        return createVNode(type, null, [propsOrChildren])
      }
      // 有props无子节点
      return createVNode(type, propsOrChildren)
    } else {
      // 有子节点
      return createVNode(type, null, propsOrChildren)
    }
  } else {
    // 如果参数大于3，那么第三个参数及之后的参数都会被作为子节点处理
    if (l > 3) {
      children = Array.prototype.slice.call(arguments, 2)
    } else if (l === 3 && isVNode(children)) {
      children = [children]
    }
    return createVNode(type, propsOrChildren, children)
  }
}
在h函数会使用createVNode函数创建虚拟DOM。
exportconst createVNode =(
  __DEV__ ? createVNodeWithArgsTransform : _createVNode
)astypeof _createVNode
```

接下来看看下面这个 真实DOM结构转化成虚拟dom的效果:
```js
// 这是真实dom
<div id="container">
  <span class="text1">Hello </span>
  <span class="text2">World</span>
</div>

// 这是转化后的虚拟dom
{
  type: 'div',
  props: {
    id: 'container'
  },
  children: [
    {
      type: 'span',
      props: {
        class: 'span1'
      },
      children: 'Hello '
    },
    {
      type: 'span',
      props: {
        class: 'span2'
      },
      children: 'World'
    },
  ]
}
```

虚拟DOM（也可以称为vnode）描述了一个真实的DOM结构，它和真实DOM一样都是由很多节点组成的一个树形结构。本质其实就是一个JS对象


### (2)、事件缓存
(和计算属性一样可以被缓存,提升性能)
* vue2里绑定事件都要重新生成新的function去更新
* vue3会自动生成一个内联函数，同时生成一个静态节点。onclick时会读取缓存，如果缓存没有的话，就把传入的事件存到缓存里
```js
<div @click="handleClick">点击</div>
  
<!--编译后-->
<script>
  export function render(...) {
    return (_openBlock()._createElementVNode('div',{onClick: _ctx.todo}, '点击'))
    )
  }
</script>
<script>
  export function render(...) {
    return (_openBlock()._createElementVNode('div',{
      onClick: _cache[0] || (_cache[0] = (...args) => (_ctx.todo(...args)))
    },'点击'))
  }
</script>
```


### (3)、基于Proxy的响应式对象
Proxy API对应的Proxy对象是ES2015就已引入的一个原生对象，用于定义基本操作的自定义行为（如属性查找、赋值、枚举、函数调用等）。
从字面意思来理解，Poxy对象是目标对象的一个代理器，任何对目标对象的操作（实例化，添加/删除/修改属性等等)，都必须通过该代理器。因此我们可以把来自外界的所有操作进行拦截和过滤或者修改等操作。
vue3. Proxy 实现响应式 Demo
```js
function reactive(target){
  if(!isObject(target)){
    return target
  }
 
  const handlers = {
    //属性读取触发get()方法
    get(target,key,receiver){
      const res = Reflect.get(target,key,receiver)
      return res
    },
    //属性设置触发set()方法
    set(target,key,value,receiver){
      trigger(target,key)
      const res = Reflect.set(target,key,value,receiver)
      return res
    },
    //数据删除触发deleteProperty()方法
    deleteProperty(target,key){
      const res = Reflect.deleteProperty(target,key)
      return res
    },
  }
  const observerd = new Proxy(target,handlers)
  return observerd
}
//对象
let obj = {
  name:'zyd',
  age:26
}
let obj_= reactive(obj)
// obj_.name = 'zyd1'
// obj_.style = '1'
//数组
let arr = new Array(5).fill().map((item,i)=>i)
let arr_ =  reactive(arr)
// arr_.push(5)
arr_[1] = 100
arr_[100] = 100
// arr_.length = 0

```
在Vue2.x中，使用Object.defineProperty()来实现响应式对象，对于一些复杂的对象，还需要循环递归的给每个属性增加上getter//setter!监听器，这使得组件的初始化非常耗时，而Vue3中，composition-api提供了一种创建响应式对象的方法reactive,其内部就是利用了Proxy API来实现的，这样就可以不用针对每个属性来一一进行添加，减少开销提升性能。
```js
// vue 2.x
// 对于Object类型
 
const vm = new Vue({
  data:{
      a:1
  }
})
 
// vm.a 是响应式的
 
vm.b = 2
// vm.b 新增属性是非响应式的
 
// 对于Array类型
 
const vm = new Vue({
  data: {
    items: ['a', 'b', 'c']
  }
})
 
vm.items[1] = 'x' // 不是响应性的 （通过索引改变一个普通值）
vm.items.length = 2 // 不是响应性的 （修改length）
 ```
proxy 是针对整个对象层面的代理拦截，而非 defineProperty 针对属性层面做的劫持。

## (二)、更小:
(1)、Tree shaking支持
Tree shaking:是一个术语，通常用于描述移除JavaScript.上下文中的未引用代码(dead-code),就像一棵大树，将那些无用的叶子都摇掉。它依赖于ES2015模块语法的静态结构特性，例如import和export。这个术语和概念在打包工具rollup和wepack中普及开来。
在vue3中，对代码结构进行了优化，让其更加符合Tree shaking的结构，这样使用相关的api时，不会把所有的都打包进来，只会打包你用到的api,例如:

同时，例如<keep-alive>:和<transition>,<teleport>等内置组件，如果没有使用也不会被打包到资源里。

## (三)、更易维护:
### (1)、Vue3从Flow迁移到TypeScript
在Vue3的源码结构层面，从Flow改成了TypeScript来编写，一般来说对于JavaScript源码框架来说引入类型检测是非常重要的，不仅可以减少bug的产生，还可以规范一些接口的定义，Flowfacebook出品，是一个静态类型检测器，有了它就可以在JavaScripti运行前找出常见的bug,包括:
* 自动类型转换
* null引用
* 可怕的undefind is not a function
这些特性和typescript非常吻合，所以在Vue3中直接采用了typescript:来进行重写，从源码层面来提升项目的可维护性。

### (2)、代码目录结构遵循monorepo
monorepo:是一种管理代码的方式，它的核心观点是所有的项目在一个代码仓库中，但是代码分割到一个个小的模块中，而不是都放在sc这个目录下面。这样的分割，每个开发者大部分时只是工作在少数的几个文件夹以内的，并且也只会编译自己负责的模块，而且不会导致一个DE打不开太大的项目之类的事情，这样很多事情就简单了很多。如下图：





## (四)、hook
### (1)、关于hook
* vue3里面可以自定义hook 主要是用来存储一些复用的逻辑、变量的封装。相当于vue2里面的minins
* 虽然vue2 minins虽然实现了重复代码的提取，但是minins的缺点就是
  * 组件的data、methods、filters会覆盖mixins里的同名data、methods、filters。
  * 变量不好找，可读性不好，维护起来比较复杂
  * minins的声明周期调用的比引入他的组件快，即；minins的beforeCreate->组件的beforeCreate->mixins的Create->组件的Create
* 不同于mixin, hook是函数，这样就可以帮助我们提高代码的复用性, 让我们能在不同的组件中都利用 hooks 函数


### (2)、 自定义hook函数
一个png转baseUrl的小例子
```js
// hook/index.ts
import { onMounted } from 'vue';

type Option = {
    el:string
}

export default function(option: Option):Promise<{baseUrl:string}>{
    return new Promise((resolve)=>{
        onMounted(()=>{
            const file:HTMLImageElement = document.querySelector(option.el) as HTMLImageElement;
            file.onload = ():void=>{
                resolve({
                    baseUrl:toBase64(file)
                })
            }
        })
    
        const toBase64 = (el:HTMLImageElement):string=>{
            const canvas:HTMLCanvasElement = document.createElement('canvas')
            const ctx = canvas.getContext('2d') as CanvasRenderingContext2D
            canvas.width = el.width
            canvas.height = el.height
            ctx.drawImage(el,0,0,canvas.width,canvas.height)
            console.log(el.width);
            return canvas.toDataURL('image/png')
             
        }
    })
}

// index.vue
<template>
  <div>
  <img src="./assets/test.png" width="300" height="300" id="img">
  </div>

</template>

<script setup lang="ts">
import useBase64 from './components/bzhan/hooks'
useBase64({el:"#img"}).then(res=>{
  console.log(res.baseUrl);
  
})

</script>

```

## (五)、从optionAPI到compsitionAPI:
字面理解：组合 Api（vue 希望通过功能函数的组合去实现逻辑拆分与复用）


### 1、Options 与 Class Api，代码组织不够聚合，无法按功能去进行代码组织，导致代码散落在 data、生命周期、watch、computed 里。

### 2、vue2.x 代码复用的主要方式是提取可复用组件；纯的计算方法，可以提取为公共方法；但有些不需要模版的公共逻辑（并且与状态、事件、生命周期等紧密关联），就很难抽取，之前的 mixin、高阶组件等方案也都有他们各自带来的弊端。
vue3.x 全新的 composition-API，可以完美解决这些问题，思路与 react-hook 类似，用负责单一功能函数进行组合

### (1)、setup API
它是 vue3 中一个新的配置项，值为一个函数。所有的组合 api 都要在它里面使用。
使用介绍
1. 使用变量 或者事件 需要把名字 return 出去即可在模板中使用。
```js
export default {
  setup() {
    let name = 'zhang'
    function at() {
      console.log(1)
    }
    return {
      name,
      at,
    }
  },
}
```
2. setup 函数的两种返回值，一种就是上面常规返回一个对象，则对象中的属性、方法, 在模板中均可以直接使用，还有一种就是返回一个函数
```js
// 若返回一个渲染函数：则可以自定义渲染内容
import { h } from 'vue'
export default {
  setup() {
    return () => h('h1', '你好')
  },
}
```
4. 注意 vue3 虽然可以向下兼容 vue2，但是尽量不能混合使用。
5. Vue2.x 配置（data、methos、computed...）中可以访问到 setup 中的属性、方法
6. 但是由于 setup 中没有 this,所以 setup 中没办法读取 Vue2.x 配置中的数据和方法
7. 如果有重名, setup 优先

(2)、$refs
在vue2中使用 this.$refs.XXX 获取，vue3中setup函数没有this，所以也有单独的获取ref的方法
```js
<template>
  <input type="text" ref="inputRef" value="这是input的文本"/>
</template>

<script>
import { onMounted, ref } from "vue";
export default {
  setup() {
    const inputRef = ref(null);/// 本质是reactive({value:null})
    onMounted(() => {
      console.log(inputRef.value.value);
    });
    console.log(inputRef.value);// null dom还没形成
    return {
      inputRef,
    };
  },
};
</script>
```

(3)、 teleport组件
* teleport翻译过来的意思是远程传送.
* teleport组件是vue3中的一个内置组件，实现的就是将一个元素传送给指定的DOM节点下。

首先在项目的/public/index.html中加入一个元素，作为teleport组件的接收容器
```js
<body>
    <noscript>
      <strong>We're sorry but <%= htmlWebpackPlugin.options.title %> doesn't work properly without JavaScript enabled. Please enable it to continue.</strong>
    </noscript>
    <div id="app"></div>
    // teleport 容器
    <div id="modal"></div>
  </body>
```
然后给modal组件套上<teleport></teleport>,并设置to属性指定容器，代码如下
```js
<template>
  <teleport to="#modal">
    <div v-if="visible" class="v3-modal">
      <h2 class="v3-modal-title">{{ title }}</h2>
      <div class="v3-modal-content">
        <slot>This is a modal</slot>
      </div>
      <button @click="handleClose">close</button>
    </div>
  </teleport>
</template>
```
最后我们看看渲染的效果，modal组件已经瞬移到#modal下了


这里仅仅举几个简单的例子,感兴趣可以阅读官方文档https://cn.vuejs.org/guide/introduction.html


## (六)、基于vite的构建工具:
伴随着Vue3,Vue团队也推出了自己的开发构建工具Vite,可以在一定程度上取代vue-cli和webpack-dev-server的功能，基于此Vite主要有以下特性：
* 快速的冷启动
* 即时的模块热更新
* 真正的按需编译
Vite在开发环境下基于浏览器原生ES Modules开发，在生产环境下基于Rollup打包。


