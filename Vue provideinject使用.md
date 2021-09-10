# Vue provide/inject使用

## 前言

想象一个场景，子组件依赖父组件传递的`props`，如果有多个这样的`props`，其实已经很烦了，当有多个子组件，或者孙组件也依赖于由上至下的`props`时，写那么多`props`简直是要了亲命了。

为了解决这种麻烦，产生了`vuex`。但是我们肯定希望组件的高可复用性，不引入`vuex`就能实现功能，那就要考虑`vue`本身是否支持这种能力了。熟悉`React`开发的人应该会想到`Context`这个高阶组件，它通过`Provider`和`Consumer`，可以使得无限嵌套的父子组件共有一个数据源。`Vue`也有一个类似的，连名字都很相似，就是这篇文章的主人公`provide`和`inject`。

[依赖注入](https://cn.vuejs.org/v2/guide/components-edge-cases.html)，官网有个例子，讲述了为何我们需要 `provide`，它是可以终结复杂数据嵌套的神器，代码：

```javascript
// parent组件
<template>
  <div>
    <div>{{test}}</div>
    <button @click="changeTest">修改test的值</button>
    <son></son>
  </div>
</template>

<script>
import Son from "./Son";
export default {
  name: "parent",
  components: { Son },
  provide() {
    return {
      injectData: this.test
    };
  },
  data() {
    return {
      test: "测试",
    };
  },
  methods: {
    changeTest() {
      this.test = "测试后";
    }
  }
};
</script>

//son组件
<template>
  <div>{{injectData}}</div>
</template>


<script>
export default {
  name: "son",
  inject: ["injectData"],
  mounted() {
    // eslint-disable-next-line
    console.log(this.injectData)
  },
};
</script>
```

`parent`组件使用`provide`提供一个`injectData`，`son`组件通过`inject`获取到`parent`注入的数据，以上就是它的最简用法。有一点需要注意，我们可以将`inject`得到的数据直接赋值给子组件的`data`或`props`，但是这个是在`vue`版本`2.1`之后才有的功能，这版本之前，会在`data`，`props`得到之后再得到注入的值。

使用`provide/inject`我们需要注意的是：

```
provide/inject`这对选项需要一起使用，以允许一个祖先组件向其所有子孙后代注入一个依赖，不论组件层次有多深，并在起上下游关系成立的时间里始终生效。
 provide 选项应该是：`一个对象或返回一个对象的函数`
 inject 选项应该是：`一个字符串数组，或 一个对象，对象的 key 是本地的绑定名
```

如果我们`parent`组件里`provide`是如下代码：

```javascript
provide: {
    injectData: this.test
}
```

我们会发现`this.test`所得到的永远是`undefined`

```javascript
 provide: {
      injectData:() => {
        //eslint-disable-next-line
        console.log(this)
      }
  },
```

我们打印出来的`this`永远是`undefined`，所以我们最好切记使用`return`返回一个对象，直接赋值对象仅在我们需要传递的是`静态数据`时再使用。

最开始的例子测试中，我们会得到，`provide`传递的`test`数据并不是响应式的，当`parent`改变了`test`的值后，`son`组件无法得到改变后的值，这就产生了一个问题，我们肯定是希望父子组件拿到的数据是相同的。

![](https://api2.mubu.com/v3/document_image/fd13cc9a-c178-4603-9be9-05d4f7ea4762-3807603.jpg)

我们先看看官网是如何说的，哦，并不直接是可响应的，而是要我们人为使它是可响应的，之前的测试是符合的，因为我们`provide`的只是一个字符串，`可响应的`我们很快想到`对象`，`数组`这些。这样又产生一个问题，是`provide`传递的值就必须是个对象，还是说，传递的可以是个指针，指向的是个对象呢？实践出真知，测试一下。

```javascript
provide() {
    return {
      injectData: this.test
    };
  },
  data() {
    return {
      test: {
        test: "测试"
      },
    };
  },
```

我们先将`provide`的改成上面的，结果是响应式的，我们再测试一下另外一种。

```javascript
provide() {
    return {
      injectData: {
        test: this.test
      }
    };
  },
  data() {
    return {
      test: "测试"
    };
  },
```

测试得出结果，上面的不是响应式的。事实证明，必须是`inject`能拿到的数据对象响应式，我们才能得到响应式结果。

其实这样也蛮麻烦的，比如如果父组件有超多需要传递下去的数据，还有个超简洁的处理方式。

```javascript
provide() {
  return {
      injectData: this
  }
}
```

我们可以将`父组件`整个传递到所需组件中去，并且组件实例本身就是响应式的，这样足以满足我们大部分组件需求。

使用时，我这边还是遇到了一种场景，不是`provide`的问题，但是与之有点关系。当我们将组件实例注入之后，子组件是能拿到组件的所有数据的，但是当父组件内里数据改变时，子组件是监听不到数据的变化的，页面也无法刷新，我是取了个巧，对数组进行 `splice`或者`concat`等其他会触发数组更新监听的操作，对象的话可以使用`...`或`Object.assign`。`$set`没什么用 ==！响应式更新机制，还是有很多不便的地方，刷新的解决方式还有待商榷，希望有更好的方法。

## 参考链接

[Vue provide/inject使用](https://www.jianshu.com/p/e574b832fdbc)
