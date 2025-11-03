## 自定义组件（以及属性、事件）

### 使用组件：

```vue
<template>
  <div>
    <h1>My App</h1>
    <my-component></my-component>
  </div>
</template>

<script>
import MyComponent from './MyComponent.vue'//从其他文件导入组件
    
export default {//vue2写法，vue3不需要写这一段，import完就能直接使用
  components: {
    MyComponent
  }
}
</script>
```

### 获取组件引用

参考：[vue3中获取ref元素的几种方式_vue3 ref-CSDN博客](https://blog.csdn.net/Leonnnn2020/article/details/140262080#:~:text=在模板 中，给需要引用的 元素 添加` ref `属性，然后在 Vue 实例的`this.%24,``` 在 Vue 实例的方法 中，可以通过`this.%24 ref s.myDiv`来访问该 元素。)

方法一：（直接定义和组件ref同名的ref变量）

```vue
<template>
   <div ref='divDom'></div> 
</template>
<script setup>
import { ref} from 'vue'
const divDom = ref();
onMounted(()=>{
    console.log('获取dom元素',divDom)
})
```

方法二：（通过document.getElementxxx）

```vue
<template>
    <li  id="itemR"> getElementById获得"li" </li> 
</template>
<script setup>
import {nextTick} from 'vue'
let el
nextTick(()=>{el=document.getElementById('itemR');//通过id获取元素
              console.log(el)；
             })
</script>
```

方法三：（this.$refs.标识符 /this.$refs["标识符"]    this可以使用proxy替代）

```vue
<template>
  <div id="app">
    <div ref="hello">小猪课堂</div>
  </div>
</template>
<script>
export default {
  mounted() {
    console.log(this.$refs.hello); // <div>小猪课堂</div>
      //或者：console.log(this.$refs["hello"]); // <div>小猪课堂</div>
  },
};
</script>
```



### 定义属性：

通过`props`属性接收父组件传递的数据。

#### props

vue3：在使用 `<script setup>` 的单文件组件中，props 可以使用 `defineProps()` 宏来声明

defineProps 是 Vue 3 中新引入的函数，用于定义子组件的 props。

与 Vue 2 中的 props 不同，defineProps 更加明确和类型安全，它使用 TypeScript 或者 PropTypes 来明确指定 props 的类型和默认值，从而提供更好的开发体验和代码健壮性。



vue2：

1.字符串形式定义（vue2写法）

```html
<script setup>
const props = defineProps(['foo'])

console.log(props.foo)
</script>
//或者
export default {
  props: ['foo'],
  setup(props) {
    // setup() 接收 props 作为第一个参数
    console.log(props.foo)
  }
}
```

2.除了使用字符串数组来声明 props 外，还可以使用对象的形式：

```js
// 使用 <script setup>
defineProps({
  title: String,
  likes: Number
})
js
// 非 <script setup>
export default {
  props: {
    title: String,
    likes: Number
  }
}
```

#### props使用

组件的属性可以由父组件动态地传递，并在组件中进行使用。

```vue
//my-component.vue
<template>
  <div>
    <h1>{{ title }}</h1>
  </div>
</template>

//vue2
<script>
export default {
  props: ['title']
}
</script>

//vue3
<script>
const props = defineProps({
  // 数据
  title: {
    type: String,//设置类型
    default: null,//设置默认值
  },
  // 后续添加其他的属性使用逗号隔开
  xxxx,
  value: [Number, String, Array]   //不指定默认值可以直接设置类型，使用[]设置类型范围有哪些
});
</script>


//使用组件并传递参数
<template>
<my-component title:"标题" />    //在props中定义的属性可以直接使用了
</template>
```

在上述代码中，我们定义了一个属性`title`，通过`props`选项接收。

或者vue3使用defineProps，将所需属性使用**字典**的形式列出



注：在模板中可以使用`{{ title }}`来显示属性的值。



简单来说就是：

```vue
//定义
defineProps({
  greetingMessage: String
})

//传递
<MyComponent greeting-message="hello" />
```

#### props绑定数据/传递数据

使用 `v-bind` 或缩写 `:` 来进行动态绑定的 props：（可以传输任何类型）

```html
<!-- 根据一个变量的值动态传入 -->
<BlogPost :title="post.title" />

<!-- 根据一个更复杂表达式的值动态传入 -->
<BlogPost :title="post.title + ' by ' + post.author.name" />

<!-- 虽然 `42` 是个常量，我们还是需要使用 v-bind -->
<!-- 因为这是一个 JavaScript 表达式而不是一个字符串 -->
<BlogPost :likes="42" />

<!-- 根据一个变量的值动态传入 -->
<BlogPost :likes="post.likes" />

<!-- 虽然这个对象字面量是个常量，我们还是需要使用 v-bind -->
<!-- 因为这是一个 JavaScript 表达式而不是一个字符串 -->
<BlogPost
  :author="{
    name: 'Veronica',
    company: 'Veridian Dynamics'
  }"
 />

<!-- 根据一个变量的值动态传入 -->
<BlogPost :author="post.author" />
```

一个对象绑定多个props

```vue
//定义对象
const post = {
  id: 1,
  title: 'My Journey with Vue'
}


<BlogPost v-bind="post" />
//这实际上等价于：
<BlogPost :id="post.id" :title="post.title" />
```

注意：v-bind一般和响应式变量搭配使用，不然传入的数据不会及时刷新，即v-bind一般只是传入数据，要双向保定使用v-model，不过vmodel有限制，参见[vmodel和vbind](#vbind vmodel)

vue2：

写在data(){}内的就是响应式的

[javascript - Vue 中的 Props 与 Data 细微差别，你知道吗？ - 终身学习者 - SegmentFault 思否](https://segmentfault.com/a/1190000021651417)

vue3：

参见：[响应式变量](#响应式变量)

### 定义事件：

Vue的自定义事件是基于Vue实例的`$emit`方法和父组件的`v-on`指令来实现的。

`$emit`方法触发事件，`v-on`用于绑定事件的处理函数（`v-on`可以缩写为`@`）

以下为vue2示例

```vue
//子组件
<template>
  <button @click="notifyParent">触发事件</button>
</template>

<script>
export default {
  methods: {
    notifyParent() {
      // 触发名为 'custom-event' 的自定义事件，并传递数据
      this.$emit('custom-event', '这是从子组件传递的数据');
    }
  }
};
</script>


//父组件
<template>
  <div>
      //此处直接使用刚刚定义的事件，并绑定事件
    <child-component @custom-event="handleCustomEvent"></child-component>
    <p>从子组件接收的数据：{{ receivedData }}</p>
  </div>
</template>

<script>
import ChildComponent from './ChildComponent.vue';

export default {
  components: {
    'child-component': ChildComponent
  },
  data() {
    return {
      receivedData: ''
    };
  },
  methods: {
    handleCustomEvent(data) {
      // 处理从子组件传递的数据
      this.receivedData = data;
    }
  }
};
</script>
```

vue3

```vue
//子组件CounterButton.vue
<template>
  <div>
    <button @click="handleClick">增加计数</button>
  </div>
</template>

<script setup>
import { defineEmits } from 'vue';

const emit = defineEmits();

const handleClick = () => {
  emit('increment');
};
</script>

<style scoped>
button {
  padding: 10px 20px;
  font-size: 16px;
}
</style>

//父组件
<template>
  <div>
    <h1>计数器应用</h1>
    <CounterDisplay :count="count" />
    <CounterButton @increment="incrementCount" />
  </div>
</template>

<script setup>
import { ref } from 'vue';
import CounterDisplay from './CounterDisplay.vue';
import CounterButton from './CounterButton.vue';

const count = ref(0);

const incrementCount () => {
  count.value++;
};
</script>

<style scoped>
h1 {
  text-align: center;
}
</style>

```

参考：[自定义事件 — Vue.js (vuejs.org)](https://v2.cn.vuejs.org/v2/guide/components-custom-events.html#自定义组件的-v-model)



### 组件V-model

原理：[组件 v-model | Vue.js (vuejs.org)](https://cn.vuejs.org/guide/components/v-model)



v-model默认绑定一个属性，可以使用`defineModel()`来定义相关绑定选项





[组件 v-model | Vue.js (vuejs.org)](https://cn.vuejs.org/guide/components/v-model)



### 插槽：

Slots允许在组件中插入额外的内容，类似于React中的子组件。Slots可以帮助我们更好地封装组件，并提供更大的灵活性。下面是一个使用Slots的示例：

```vue
//my-component.vue
<template>
  <div>
    <slot></slot>
  </div>
</template>

<script>
export default {}
</script>

//使用组件
<template>
  <div>
    <my-component>//组件标签内包含其他元素，这些元素会替换掉插槽
      <h1>My Title</h1>
      <p>My Content</p>
    </my-component>
  </div>
</template>

<script>
import MyComponent from './MyComponent.vue'//导入组件

export default {
  components: {
    MyComponent
  }
}
</script>
```

在上述代码中，我们通过组件的标签内部插入了一个`<h1>`标签和一个`<p>`标签，这些内容会被插入到`MyComponent`组件的插槽中。



参考：[Vue3中的组件：组件的定义、组件的属性和事件、组件的Slots和动态组件-腾讯云开发者社区-腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/2299992)

#### 具名插槽

`<slot>` 元素可以有一个特殊的 attribute `name`，用来给各个插槽分配唯一的 ID，以确定每一处要渲染的内容：

```vue
<div class="container">
  <header>
    <slot name="header"></slot>
  </header>
  <main>
    <slot></slot>
  </main>
  <footer>
    <slot name="footer"></slot>
  </footer>
</div>
```

父元素使用时

```vue
<BaseLayout>
  <template v-slot:header>
    <!-- header 插槽的内容放这里 -->
  </template>
</BaseLayout>
```

此处v-slot可以简写为#

即`<template #header>`

#### 具名插槽的绑定使用

```vue
<template>
  <div>
    <slot #default="scope">
      <p>{{ scope }}</p>
    </slot>
  </div>
</template>

```

上述代码使用`=`将插槽的内容赋值给名为`scope`的变量

因此可以通过scope获取相关的值



## Vue3 中值传递与引用传递

### 引用类型的属性

如果对象的属性是引用类型（如<font style="color:red">**数组**、**对象**</font>），那么赋值时只是复制了引用，两个对象的该属性会指向同一个内存地址。这意味着修改其中一个对象的属性值会影响到另一个对象。



**浅拷贝只复制对象的属性值，而不复制对象的引用。使用`Object.assign`方法或扩展运算符进行对象赋值时，会进行浅拷贝。**

### 浅拷贝

#### Object.assign方法

将一个或多个对象的属性合并到目标对象中。这种方法可以实现对象的浅拷贝，即只复制对象的属性值，而不复制对象的引用。

```js
data() {
  return {
    user: {
      name: 'John',
      age: 25,
      email: 'john@example.com'
    }
  }
},
methods: {
  updateUser() {
    this.user = Object.assign({}, this.user, {
      name: 'Alex',
      age: 30,
      email: 'alex@example.com'
    })
  }
}
```



#### 扩展运算符（…）

```js
data() {
  return {
    user: {
      name: 'John',
      age: 25,
      email: 'john@example.com'
    }
  }
},
methods: {
  updateUser() {
    this.user = {
      ...this.user,
      name: 'Alex',
      age: 30,
      email: 'alex@example.com'
    }
  }
}
```



### 深拷贝



## $  #  ?  ?.  ?? 

$

1.用来表示变量，比如变量 `var s='asdsd'`或`var $s='asdasd'`;

2.es6中也可以采用${XXX}来在字符串中插入变量（这个记住要利用v-bind）

3.Vue 实例还暴露了一些有用的实例属性与方法。它们都有前缀 $，以便与用户定义的属性区分开来。例如：

```js
var data = { a: 1 }
var vm = new Vue({
  el: '#example',
  data: data
})
 
vm.$data === data // => true
vm.$el === document.getElementById('example') // => true
 
// $watch 是一个实例方法
vm.$watch('a', function (newValue, oldValue) {
  // 这个回调将在 `vm.a` 改变后调用
})
```

#

一般表示文档位置或者其他超链接



?.可选链运算符

`?.` 运算符的功能类似于 `.` 链式运算符，不同之处在于，在引用为空 ([nullish](https://developer.mozilla.org/zh-CN/docs/Glossary/Nullish) ) ([`null`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/null) 或者 [`undefined`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/undefined)) 的情况下**不会引起错误**，该表达式短路返回值是 `undefined`。

与函数调用一起使用时，如果给定的函数不存在，则返回 `undefined`。

```js
const adventurer = {
  name: 'Alice',
  cat: {
    name: 'Dinah',
  },
};

const dogName = adventurer.dog?.name;
console.log(dogName);
// Expected output: undefined

console.log(adventurer.someNonExistentMethod?.());
// Expected output: undefined
```

??

空值凝聚

```js
const value1 = 0 ?? 'default string';
console.log(value1);
//输出 0
//    undefined

const value1 = 0 || 'default string';
console.log(value1);
//输出default string
//    undefined

const value2 = '' || 1000;
console.log(value2);
//输出1000
//    undefined

```

使用（`||`）时，若一个变量的值为 **0** 或一个**空字符串**，它将被视为**未定义或 NULL**，并返回默认值（右边的值）。



空值凝聚的工作原理与逻辑 OR 运算符完全一样，只是当左边的值为 `undefined` 或 `null` 时，你会得到右边的值。



## v-for

四种用法：[VUE语法：v-for指令的四种用法_vue v-for-CSDN博客](https://blog.csdn.net/qq_42586648/article/details/97231928)

#### 循环普通数组

vfor取出的是数据和下标，数据部分若是嵌套就按照类的方式访问

```vue
//土蛋方法：
<p>{{list[0]}}</p>
<p>{{list[1]}}</p>
<p>{{list[2]}}</p>
<p>{{list[3]}}</p>
<p>{{list[4]}}</p>
 
//v-for方法：
<p v-for="(item,i) in list">{{i}},{{item}}</p>
 
//数组数据部分：
data:{
    list:[1,2,3,4,5,6]
},
```

#### 循环对象数组

```vue
//v-for用法：
<p v-for="(user,i) in list">{{user.id}},{{user.name}},{{i}}</p>
 
//数组数据部分:
list:[
    {id:1,name:"zs1"},
    {id:2,name:"zs2"},
    {id:3,name:"zs3"},
    {id:4,name:"zs4"},
]
```

#### v-for循环对象

```vue
//v-for方法
<p v-for="(val,key,i) in user">{{val}},{{key}}</p>
 
//对象部分：
user：{
    id:1,
    name:"巧克力"
    gender:"萌妹"
}
```

#### v-for迭代数组

```
//in后面我们放过 普通数组 对象数组 对象 还可以放数字
//注意：如果使用 v-for 迭代数字的话，前面的count值从1开始
<p v-for="count in 10">这是第{{count}}次循环</p>
```



## 响应式变量

参考：[响应式基础 | Vue.js (vuejs.org)](https://cn.vuejs.org/guide/essentials/reactivity-fundamentals)



ref

使用 [`ref()`](https://cn.vuejs.org/api/reactivity-core.html#ref) 函数来声明响应式状态

`ref()` 接收参数，并将其包裹在一个带有 `.value` 属性的 **ref 对象**中返回：

```js
import { ref } from 'vue'

const count = ref(0)

console.log(count) // { value: 0 }  //包裹住了，访问值使用value属性
console.log(count.value) // 0
```

reactive

与将内部值包装在特殊对象中的 ref 不同，`reactive()` 将使对象本身具有响应性：

```vue
<script>
import { reactive } from 'vue'

const state = reactive({ count: 0 })
</script>
#模板中使用
<button @click="state.count++">
  {{ state.count }}
</button>
```

`reactive()` 返回的是一个原始对象的 [Proxy](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy)，它和原始对象是不相等的：

```js
const raw = {}
const proxy = reactive(raw)

// 代理对象和原始对象不是全等的
console.log(proxy === raw) // false
```

局限性：

1. **有限的值类型**：它只能用于对象类型 (对象、数组和如 `Map`、`Set` 这样的[集合类型](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects#keyed_collections))。它不能持有如 `string`、`number` 或 `boolean` 这样的[原始类型](https://developer.mozilla.org/en-US/docs/Glossary/Primitive)。

2. **不能替换整个对象**：由于 Vue 的响应式跟踪是通过属性访问实现的，因此我们必须始终保持对响应式对象的相同引用。这意味着我们不能轻易地“替换”响应式对象，因为这样的话与第一个引用的响应性连接将丢失：

   ```js
   let state = reactive({ count: 0 })
   
   // 上面的 ({ count: 0 }) 引用将不再被追踪
   // (响应性连接已丢失！)
   state = reactive({ count: 1 })
   ```

3. **对解构操作不友好**：当我们将响应式对象的原始类型属性解构为本地变量时，或者将该属性传递给函数时，我们将**丢失**响应性连接：

   ```js
   const state = reactive({ count: 0 })
   
   // 当解构时，count 已经与 state.count 断开连接
   let { count } = state
   // 不会影响原始的 state
   count++
   
   // 该函数接收到的是一个普通的数字
   // 并且无法追踪 state.count 的变化
   // 我们必须传入整个对象以保持响应性
   callSomeFunction(state.count)
   ```

ref不会解包时失去连接：

```js
const count = ref(0)
const state = reactive({
  count
})

console.log(state.count) // 0
state.count = 1
console.log(count.value) // 1

const otherCount = ref(2)

state.count = otherCount//此时原始 ref 现在已经和 state.count 失去联系，指向otherCount
console.log(state.count) // 2
console.log(count.value) // 1   原来的count不受影响，仍是原值
```

总之：推荐使用ref作为响应

toref和torefs

toRef 和 toRefs 可以用来复制 reactive 里面的属性然后转成 ref，而且它既保留了响应式，也保留了引用，也就是你从 reactive 复制过来的属性进行修改后，除了视图会更新，原有 ractive 里面对应的值也会跟着更新，如果你知道 浅拷贝 的话那么这个引用就很好理解了，它复制的其实就是引用 + 响应式 ref

原文链接：https://blog.csdn.net/cookcyq__/article/details/121618833

不加 s 和 加 s 的区别就是这样：

- toRef: 复制 reactive 里的单个属性并转成 ref
- toRefs: 复制 reactive 里的所有属性并转成 ref

## 计算属性computed

主要用于减少模板的计算，返回值为一个**计算属性 ref**。**计算属性值会基于其响应式依赖被缓存**。

```vue
<script setup>
import { reactive, computed } from 'vue'

const author = reactive({
  name: 'John Doe',
  books: [
    'Vue 2 - Advanced Guide',
    'Vue 3 - Basic Guide',
    'Vue 4 - The Mystery'
  ]
})

// 一个计算属性 ref
const publishedBooksMessage = computed(() => {
  return author.books.length > 0 ? 'Yes' : 'No' //响应式依赖表达式
})
</script>

<template>
  <p>Has published books:</p>
  <span>{{ publishedBooksMessage }}</span>
</template>
```

Vue 的计算属性会自动追踪**响应式依赖**。它会检测到 `publishedBooksMessage` 依赖于 `author.books`，所以当 `author.books` 改变时，任何依赖于 `publishedBooksMessage` 的绑定都会同时更新。

而如果不改变，会立即返回先前的计算结果，而不用重复执行函数。

因此相比于调用方法可以减少很多的重复计算。

注意：

1.计算属性默认是只读的，可以写，但是不推荐写入

2.有些表达式**不属于**响应式依赖，使得计算属性不更新，比如

```js
const now = computed(() => Date.now())
```



## 同步与异步执行

异步：

异步函数async和await的用法

要是有await必须将函数定义为async类型

使用await执行函数时后续操作会等待该函数执行完再往下

就和平常一样，顺序执行

```js
function doubleAfter2seconds(num) {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve(2 * num)
        }, 2000);
    } )
}
async function testResult() {
    let result = await doubleAfter2seconds(30);
    console.log(result);
}
//doubleAfter2seconds(30) 执行完毕，doubleAfter2seconds(30) 返回的promise 开始执行，2秒 之后，promise resolve 了， 并返回了值为60， 这时await 才拿到返回值60， 然后赋值给result， 暂停结束，代码才开始继续执行，执行 console.log语句。
```

同步：

以then为例，该函数执行和后续几乎是并行的，不会等待

```js
async function timeout() {
    return 'hello world'
}
timeout().then(result => {
    console.log(result);
})
console.log('虽然在后面，但是我先执行');

//输出：虽然在后面，但是我先执行
//	   hello world
```



参考：[vue中异步函数async和await的用法 - zhouj2019 - 博客园 (cnblogs.com)](https://www.cnblogs.com/zhoujuan/p/11692818.html)

### promise

用法：

```js
function getData(url){
   
    return new Promise((resolve, reject) => {
   
        $.ajax({
   
            url,
            success(data){
   
                resolve(data);
            },
            error(err){
   
                reject(err);
            }
        });
    });
}

const url1 = '/data1.json';
const url2 = '/data2.json';
const url3 = './data3.json';
getData(url1).then(data1 => {
   
    console.log(data1);
    return getData(url2);
}).then(data2 => {
   
    console.log(data2);
    return getData(url3);
}).then(data3 => {
   
    console.log(data3);
}).catch(err => console.error(err));
```

上述代码通过promise执行请求网络的操作，操作若是成功进入then，失败则进入catch



promise的三种状态

- `pending` ：等待状态，即在过程中，还没有结果。比如正在网络请求，或定时器没有到时间。
- `fulfilled` ：满足状态，即事件已经解决了，并且成功了；当我们主动回调了 `fulfilled` 时，就处于该状态，并且会回调 `then` 函数。
- `rejected` ：拒绝状态，即事件已经被拒绝了，也就是失败了；当我们主动回调了 `reject` 时，就处于该状态，并且会回调 `catch` 函数。

总结：

- 只会出现pending → fulfilled，或者pending → rejected 状态，即要么成功要么失败；
- 不管是成功的状态还是失败的状态，结果都不可逆。

示例：

```js
const p1 = Promise.resolve(100); //执行resolve进入fulfilled状态，触发then
console.log('p1', p1);//输出promise对象，不是那个100
p1.then(data => {
    console.log('data', data);
}).catch(err => {
    console.error('err', err);
});
```

结果：

![状态的表现演示1](https://ucc.alicdn.com/tkuc6v2pkzeao/developer-article1613399/20240927/42976a0421ef4783aff6ec656c740643.png?x-oss-process=image/resize,w_1400/format,webp)

```js
const p2 = Promise.reject('404'); //rejected
console.log('p2', p2);
p2.then(data => {  
    console.log('data2', data);
}).catch(err => {  
    console.log('err2', err);
})
```

结果：

![状态的表现演示2](https://ucc.alicdn.com/tkuc6v2pkzeao/developer-article1613399/20240927/3823404b41cb4d74afa02d47ac3ffff4.png?x-oss-process=image/resize,w_1400/format,webp)

注意点：（注意嵌套）

- `then` 正常返回 `fulfilled` ，里面有报错则返回 `rejected` ；
- `catch` 正常返回 `fulfilled` ，里面有报错则返回 `rejected` 。

```js
const p3 = Promise.reject('my error').catch(err => {
    console.error(err);
});
console.log('p3', p3); //fulfilled状态，注意！触发后续.then回调
p3.then(() => {
    console.log(100);
});
//上述代码执行catch正常，会进入fulfilled状态，因此可以触发后续嵌套的then
//会输出100

const p4 = Promise.reject('my error').catch(err => {
    throw new Error('catch err');
});
console.log('p4', p4); //rejected状态，触发.catch回调函数
p4.then(() => {
    console.log(200);
}).catch(() => {
    console.log('some err');
});
//上述代码执行catch抛出异常，会进入rejected状态，继续触发后续嵌套的catch
//会输出100
```

因此多个then和catch嵌套的promise代码要注意

参考：[解决异步问题，教你如何写出优雅的promise和async/await，告别callback回调地狱！-阿里云开发者社区](https://developer.aliyun.com/article/1613399)



## 解包/解构

`{{变量}}`

模板中使用上述写法进行解包，访问变量的值

在模板中访问 ref 类型的属性,直接访问,无需` .value`
模板中访问 ref 类型的属性 `.value` 是没有数据的
模板中访问 reactive 类型的对象的属性 `.value` 是没有数据的

在 js 当中对于 ref 类型的属性的访问和赋值都需要通过` .value` 来进行
在 js 当中对于 ractive 类型的对象的属性是直接访问的,可以直接 `对象.属性名`,不用 .value

```vue
<template>
  <div>
	<!--在模板中访问 ref 类型的属性,直接访问,无需 .value-->
    <h2>name : {{ name }}</h2>
    <!-- 模板中访问 ref 类型的属性 .value 是没有数据的-->
    <h2>name.value : {{ name.value }}</h2>
    <h2>user : {{ user }}</h2>
    <h2>user.name : {{ user.name }}</h2>
    <!-- 模板中访问 reactive 类型的对象的属性 .value 是没有数据的-->
    <h2>user.name.value : {{ user.name.value }}</h2>
  </div>
</template>
```



## vbind vmodel

v-bind

1. 用途: v-bind用于将数据绑定到DOM元素的属性(attribute)上。它可以用来动态设置元素的各种属性，例如class, style, href等。
2. 单向数据绑定: v-bind实现的是单向数据绑定，即数据的变化会导致视图的更新，但视图的变化（如用户输入）不会直接通过v-bind反向更新到数据层。需要其他机制（例如事件监听）来实现从视图到数据的更新。
3. 语法: v-bind可以使用简写语法:。例如，v-bind:href="url"可以简写为:href="url"。

v-model

1. 用途: v-model用于在表单输入元素（如<input>, <textarea>, <select>等）和应用状态之间创建双向数据绑定。它通过自动选择合适的方式来更新元素的值，并且在用户输入时更新相应的数据。主要用于处理表单输入和应用数据之间的同步。
2. 双向数据绑定: v-model实现的是双向数据绑定，即数据的变化会导致视图更新，视图的变化（用户的输入行为）也会自动更新数据层。
3. 修饰符: v-model支持使用修饰符来处理数据，例如.lazy、.number和.trim等，这些修饰符可以用来控制如何从用户输入中同步数据。



### v-model的原理

官方有说到，v-model的原理其实是背后有两个操作：

1. **v-bind绑定value属性的值；**
2. **v-on绑定input事件监听到函数中，函数会获取最新的值赋值到绑定的属性中；**

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/47ed14e500902473fbebbeaf5a962da9.png)

也就是说使用v-model时，已经默认帮我们绑定了组件内的一个变量，并关联了一个事件

如checkbox、select

默认关联名为value的属性

如果要指定绑定某属性可以使用 `v-model:xxx='xxxxxx'`，其中xxx为组件的属性名

xxxxx为要绑定的变量

如：

```vue
//子组件TODOItem.vue
props:{
    info:String,
    info2:String
  },

<h>{{info}}</h>

//父组件
a=ref("haha")

<TODOItem v-model:info="a"/>
```



## Store

Store 是保存状态(state)和业务逻辑的实体, store 不应该与我们的组件绑定. 换句话说, store 就是全局状态.
store 有三个关键概念, 分别是 state, getters 和 actions, 这与 Vue 组件中的 data, computed 和 methods 是相对应的概念.

（个人理解，就是state）

通过 `defineStore` 函数定义 `store`

- ```
  defineStore
  ```

  接收两个参数

  - `id`: 唯一的标识, `string` 类型. `Pinia` 使用 `id` 与开发者工具建立联系.
  - 第二个参数可以是一个函数, 也可以是一个对象.

  i.如果要传入对象类型作为第二个参数, 在对象中可以配置:

  - `state`: 状态, 即数据. 📕注意 `state` 是一个函数, 函数的返回值才是真正定义的数据
  - `getters`: 计算属性
  - `actions`: 修改状态的方法

  (简单来说第一个就是数据，第二个就是获取数据的值，第三个就是对数据进行操作)

  ii.在函数中可以通过 `ref`/`reactive` 定义响应式数据, 通过 `computed` 和 `watch` 定义计算属性和侦听器, 再定义一些修改数据的方法, 并通过返回对象的形式将其中一些数据暴露出去.

  ```js
  //对象
  export const useCounterStore = defineStore('counter', {
    state: () => {
      return {
        count: 0,
      }
    },
    getters: {
      doubleCount: (state) => {
        return state.count * 2;
      }
    },
    actions: {
      increment(a: number) {
        this.count += a
      }
    }
  })
  
  //函数
  import { defineStore } from 'pinia';
  import { ref } from 'vue';
  
  export const useNameStore = defineStore('name', () => {
    const name = ref('tom');
    function setName(newName: string) {
      name.value = newName;
    }
    return { 
      name,
      setName
    }
  });
  ```

  

- `defineStore` 返回一个函数, 一般约定将返回值命名为 `use...`.





## pom xml

需要设置parent包

要不然找不到路径

```vue
 <parent>
        <artifactId>ruoyi</artifactId>
        <groupId>com.ruoyi</groupId>
        <version>3.8.7</version>
    </parent>
```





## 插槽

#default=“scope”

具体来说，`#default="scope"`是用于在父组件中定义插槽的语法。通过给插槽添加`#default`指令，并使用`="scope"`将默认内容绑定到`scope`变量上，可以在插槽内部访问该变量并进行相关操作。

使用template中的作用域插槽，它的作用是在外部获取组件内的数据 ,这里是为了获取这一行的数据，我们让slot-scope值为scope，那么由scope.row就可以得到数据了。如图所示：

```vue
<el-table-column lable="状态" align="center">
   <template #default="scope">
     <span>{{ scope.row["state"] == 1 ? "未使用" : "已使用" }}</span>
   </template>
</el-table-column>
```

旧写法：

```vue
<el-table-column label="问题状态" align="center" prop="status">
   <template slot-scope="scope">
     <span>{{scope.row.status==0?'待整改':(scope.row.status==1?'已整改':'已关闭')}</span>
   </template> 
</el-table-column>
```

[详解Vue3——#default=“scope”_#default="scope-CSDN博客](https://blog.csdn.net/TianXuab/article/details/132215253)

https://blog.csdn.net/cdd9527/article/details/126501032



## 页面跳转



[vue实现页面跳转并传参的八种方法-CSDN博客](https://blog.csdn.net/qq_38244874/article/details/110390047)



## el-select不显示选项文本

如果 `el-select` 组件在选择后不显示文本，这通常是由于数据绑定或组件使用方式不正确导致的。以下是一些可能导致这个问题的原因以及相应的解决方案：

1. **确保 `v-model` 正确绑定**：
   `v-model` 应该绑定到一个数据属性上，这个属性将存储选中项的 `value`。确保这个属性在组件的 `data` 函数中被正确初始化。
2. **检查 `el-option` 的 `label` 和 `value`**：
   确保每个 `el-option` 组件的 `label` 和 `value` 属性都被正确设置。`label` 是显示给用户的文本，而 `value` 是与该选项相关联的值。



## el-table  data、prop

```vue
<script>
tableData: [{
        date: '2016-05-02',
        name: '王小虎',
        address: '上海市普陀区金沙江路 1518 弄'
      }, {
        date: '2016-05-04',
        name: '王小虎',
        address: '上海市普陀区金沙江路 1517 弄'
      }, {
        date: '2016-05-01',
        name: '王小虎',
        address: '上海市普陀区金沙江路 1519 弄'
      }],
</script>
可以使用el-table将数据展示为表格，代码如下：

<el-table :data="tableData">
      <el-table-column prop="date" label="日期" width="180">
      </el-table-column>
      <el-table-column prop="name" label="姓名" width="180">
      </el-table-column>
      <el-table-column prop="address" label="地址">
      </el-table-column>
    </el-table>
```

data属性用来**绑定数据**列表，**prop属性用于指定该列绑定数据列表的属性名**

el-table-column表示表格的一列，label是列的显示名称，width用于指定列宽度。

## 暴露属性

在 Vue 3 中，当我们使用 `<script setup>` 语法糖时，组件默认不会自动暴露内部的任何状态或方法给外部使用，为了显式暴露某些属性或方法，可以使用 defineExpose

```js
<script setup>
import { ref } from 'vue'

const a = 1
const b = ref(2)

defineExpose({
  a,
  b
})
</script>
```

参考：[详细分析Vue3中的defineExpose（附Demo）-CSDN博客](https://blog.csdn.net/weixin_47872288/article/details/138899701)



## 类型断言as

这个用法属于TypeScript

用于检查变量类型，

```ts
import type { PropType } from 'vue'

interface Book {
  title: string
  author: string
  year: number
}

export default {
  props: {
    book: {
      // 提供一个比 `Object` 更具体的类型
      type: Object as PropType<Book>,
      required: true
    }
  }
}
```

例如：在用运行时 props 声明时给一个 prop 标注更复杂的类型定义。

## scope、template和slot-scope

scope

```
<div id="app">
  <tb-list :data="data">
    <template scope="scope">
      <div class="info" :s="JSON.stringify(scope)">
        <p>姓名:{{scope.row.name}}</p>
        <p>年龄: {{scope.row.age}}</p>
        <p>性别: {{scope.row.sex}}</p>
        <p>索引：{{scope.$index}}</p>
      </div>
    </template>
  </tb-list>
</div>


```

template和slot-scope

```vue
<template>
  <el-table :data="tableData">
    <el-table-column label="序号">
      <template slot-scope="scope">
        <span>{{ scope.$index + 1 }}</span>
      </template>
    </el-table-column>
    <el-table-column label="姓名">
      <template slot-scope="scope">
        <span>{{ scope.row.name }}</span>
      </template>
    </el-table-column>
    <el-table-column label="年龄">
      <!-- 支持直接通过 {} 去解构数据 -->
      <template slot-scope="{row}">
        <span>{{ row.age }}</span>
      </template>
    </el-table-column>
  </el-table>
</template>
```

总结：可以通过scope获取表单的数据，例如`scope.row`获取某行数据，`scope.$index`获取行标

## watch监视无效的情况

1.一般监视的变量为ref 或者reactive类型的









## 前后端传参：

前端传json/字典类型

```
export function updateUserPwd(oldPassword, newPassword) {
  const data = {
    oldPassword,
    newPassword
  }
  return request({
    url: '/system/user/profile/updatePwd',
    method: 'put',
    params: data
  })
}
```

```
//后端接收
public AjaxResult updatePwd(String oldPassword, String newPassword){
...
}
```

传表单数据formdata

```
let formData = new FormData();
    formData.append("avatarfile", data, options.filename);
    uploadAvatar(formData)
    
export function uploadAvatar(data) {
  return request({
    url: '/system/user/profile/avatar',
    method: 'post',
    data: data
  })
}
```

```
public AjaxResult avatar(@RequestParam("avatarfile") MultipartFile file) throws Exception
    {
    }
```

传json





## vue2和vue3区别

### 1.组件上的小区别

如`el-date-picker`控件，其2.0时间格式为`yyyy-MM-dd HH-mm-ss`

3.0格式为`YYYY-MM-DD HH-mm-ss`即大小写不同

### 2.dict使用区别

3.0调用useDict函数来获取字典集合，并直接使用字典内的值

```vue
界面
<dict-tag :options="data_input_status" :value="scope.row.inputStatus"/>

脚本内声明使用
<script>
const { data_input_status } = proxy.useDict('data_input_status');
const { data_check_status } = proxy.useDict('data_check_status');
    ...
</script>
```

2.0则在export内写上dict字段就能使用了

```vue
<script >
export default {
  name:"InputPlan",
  dicts:['data_input_status','data_check_status'],//指定使用的字典集合
  data() {}
    ...
</script>

界面内
 <dict-tag :options="dict.type.data_input_status" :value="scope.row.inputStatus"/>



```

## 全局属性

### router和route

route可以获取访问路径的参数，即url附带的请求参数

router可以去访问某个路径（相当于route是url，router访问跳转者）





## vue模块导入与全局挂载

### 父子组件的变量挂载与使用（provide / inject）

正常情况下，父子组件之间的数据传递需要用到 props 属性，这里就会有问题，如果父组件的数据需要送到 N 层子组件，那么就要传递 N 次 props 属性，非常繁琐，而 provide / inject 就是用来解决 props 多层嵌套的问题，有了它，只需声明一次数据就够了，使用方式也很简单。

假设父组件是 `A.vue` ，子组件是 `B.vue`，子子组件是`C.vue`，如果 C.vue 子子组件想要 A.vue 父组件的数据，我们只需在 A.vue 父组件调用 provide 函数导出数据， 然后 C.vue 调用 inject 函数导入数据就可以了，无须经过 B.vue 组件层，
案例如下：

```vue
// A.vue
setup() {
 const info = reactive({
    name: 'Tony',
    age: 99
  })
  provide('userInfo', info) // 导出
 }

// C.vue
setup() {
  const info = inject('userInfo') // 导入
}
```

参考：[Vue3 理解 provide / inject 的作用、使用_vue3中使用inject引入的变量怎么使用-CSDN博客](https://blog.csdn.net/cookcyq__/article/details/121619003)

**provide只能提供给子组件使用，**

**父组件要想使用子组件的直接使用ref来获取属性（前提是子组件把数据暴露出来）**

```vue
//子组件
provide("viewer", _viewer);
defineExpose({
  _viewer
})

//父组件
<Viewer class="view3d" ref="viewerRef">

const { proxy } = getCurrentInstance() as ComponentInternalInstance
const _viewer = (proxy?.$refs.viewerRef as any)._viewer;
```

参考：[子组件ref变量和defineExpose | 前端技术文档 (yeshenzhy.github.io)](https://yeshenzhy.github.io/learn-web/vue3/refDefineExpose.html#子组件)



### cesium导入

```bat
npm i cesium  vite-plugin-cesium #安装cesium以及相关插件
```

首先找到vue的vite.config.js 文件

```js
//js配置
//顶部导入
import vue from '@vitejs/plugin-vue'
import cesium from 'vite-plugin-cesium'

export default defineConfig({
  plugins: [
    vue(),cesium()//导入cesium
  ],
  resolve: {
    alias: {
      '@': fileURLToPath(new URL('./src', import.meta.url))
    }
  }
})
```

### tdesign导入：

```
npm i tdesign-vue-next
```

[Vue Next for Web | TDesign (tencent.com)](https://tdesign.tencent.com/vue-next/getting-started)

使用时可以按需使用或者全部导入

按需导入时

使用那个就在main.js文件

加入`app.use(Txxx);`



## 



