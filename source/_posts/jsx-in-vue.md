---
title: 在 Vue 中使用 JSX
date: 2022-10-23 11:09:49
categories: 技术文章
tags:
    - Vue
    - jsx
---

在使用 Vue 开发项目时绝大多数情况下都是使用模板来写 HTML，但是有些时候页面复杂又存在各种条件判断来显示/隐藏和拼凑页面内容，或者页面中很多部分存在部分DOM结构一样的时候就略显捉襟见肘，会写大量重复的代码，会出现单个 .vue 文件过长的情况，这个时候我们就需要更多的代码控制，这时候可以使用渲染函数。

<!-- more -->

渲染函数想必平时几乎没有人去写，因为写起来很痛苦（本人也没有写过）。更多的是在 Vue 中使用 JSX 语法。写法上和在 React 中差不多，但是功能上还是没有 React 中那么完善。

在写 JSX 的过程中不得考虑一个样式的问题，虽然可以直接在 .vue 文件中不写 `<template>` 部分，只写 `<script>` 和 `<style>` 部分，而不用担心样式作用域问题。但是更多的时候还是推荐直接使用 .js 的方式来写组件，这个时候就涉及到样式作用域的问题了。

在 React 的生态中，有很多 CSS-IN-JS 的解决方案，比如 styled-jsx、emotion、styled-components 等，目前最活跃和用户量最多的是 styled-components ，目前已经拥有良好的生态圈子。如果需要在样式中作一些像 Sass/Less 中的颜色计算，可以使用 polished 来实现，当然不止这么简单的功能。但是在 Vue 中可使用的方案就太少了，因为 Vue 使用模板来写 HTM L本身是开箱即用的样式 scoped，在使用 JSX 写组件的时候就面临着样式问题，一种方案是在组件包裹 `<div>` 中取一个特殊的名字，然后样式都嵌套写在这个 class 下面，但是难免会遇到命名冲突的情况，而且每次还得变着花样取名称。此外，就是引入 CSS-IN-JS 在 Vue 对应的实现，但目前来看 Styled-components 官方提供了一个 Vue 版本的叫 vue-styled-components 和 emotion 的 vue-emotion，但是用的人实在太少。像 styled-components 进行了重大更新和变化，但是 Vue 版本的还是最初的版本，而且有时候还出现样式不生效的情况。

## 基本用法

首先需要约定一下，使用 JSX 组件命名采用首字母大写的驼峰命名方式，样式可以少的可以直接基于 vue-styled-components 写在同一个文件中，复杂的建议放在单独的 _Styles.js 文件中，当然也可以不采用 CSS-IN-JS 的方式，使用 Less/Sass 来写，然后在文件中 import 进来。

下面是一个通用的骨架：

``` jsx
import styled from 'vue-styled-components'

const Container = styled.div`
  height: 100%;
`

const Dashboard = {
  name: 'Dashboard',

  render() {
    return (
      <Container>内容</Container>
    )
  }
}

export default Dashboard
```

### 插值

在JSX中使用单个括号来绑定文本插值：

``` jsx
<span>Message: {this.message}</span>
// 类似于v-html
<div domPropsInnerHTML={this.dangerHtml}/>
// v-model
<el-input v-model={this.vm.name} />
```

在 jsx 中不需要把 v-model 分成事件绑定和赋值二部分分开来写，因为有相应的 babel 插件来专门处理。

### 样式

在 JSX 中可以直接使用 class="xx" 来指定样式类，内联样式可以直接写成 style="xxx"：

``` jsx
<div class="btn btn-default" style="font-size: 12px;">Button</div>

// 动态指定
<div class={`btn btn-${this.isDefault ? 'default' : ''}`}></div>
<div class={{'btn-default': this.isDefault, 'btn-primary': this.isPrimary}}></div>
<div style={{color: 'red', fontSize: '14px'}}></div>
```

### 遍历

在 JSX 中没有 v-for 和 v-if 等指令的存在，这些全部需要采用 js 的方式来实现：

``` jsx
{ /* 类似于v-if */ }
{
  this.withTitle && <Title />
}

{ /* 类似于v-if 加 v-else */ }
{
  this.isSubTitle ? <SubTitle /> : <Title />
}

{ /* 类似于v-for */ }
{
  this.options.map(option => {
    return (
      <div>{option.title}</div>
    )
  })
}
```

### 事件绑定

事件绑定需要在事件名称前端加上 on 前缀，原生事件添加 nativeOn ：

``` jsx
// 对应@click
<el-button onClick={this.handleClick}>Click me</el-button>
// 对应@click.native
<el-button nativeOnClick={this.handleClick}>Native click</el-button>
// 传递参数
<el-button onClick={e => this.handleClick(this.id)}>Click and pass data</el-button>
```

注意：如果需要给事件处理函数传参数，需要使用箭头函数来实现。如果不使用箭头函数那么接收的将会是事件的对象event属性。

### 高级部分

在 Vue 中基于 jsx 也可以把组件拆分成一个个小的函数式组件，但是有一个限制是必需有一个外层的包裹元素，不能直接写类似：

``` jsx
const Demo = () => (
  <li>One</li>
  <li>Two</li>
)
```

必需写成：

``` jsx
const Demo = () => (
  <div>
    <li>One</li>
    <li>Two</li>
  </div>
)
```

而在React中可以使用空标签 `<></>` 和 `<react.Fragment></react.Fragment>` 来实现包裹元素，这里的空标签其实只是 react.Fragment 的一个语法糖。同时在 React 16 中直接支持返回数组的形式：

``` jsx
const Demo = () => [
  <li>One</li>
  <li>Two</li>
]
```

那么在 Vue 中就只能通过遍历来实现类似的功能，大体思路就是把数据先定义好数据然后直接一个 map 生成，当然如果说元素的标签是不同类型的那就需要额外添加标识来判断了。

``` jsx
export default {
  data() {
    return {
      options: ['one', 'two']
    }
  },

  render() {
    const LiItem = () => this.options.map(option => <li>{option}</li>)

    return (
      <div>
        <ul>
          <LiItem />
        </ul>
      </div>
    )
  }
}
```

### 事件修饰符

在基础部分简单介绍了事件的绑定用法，这里主要是补充一下事件修饰符的写法。

在模板语法中 Vue 提供了很多事件修饰符来快速处理事件的冒泡、捕获、事件触发频率、按键识别等。可以直接查看官方文档的事件和按键修饰符部分，这里把相关内容原样搬运过来：

| 修饰符                       | 前缀 |
| ---------------------------- | ---- |
| .passive                     | &    |
| .capture                     | !    |
| .once                        | ~    |
| .capture.once或.once.capture | ~!   |

使用方式如下：

``` jsx
<el-button {...{
 '!click': this.doThisInCapturingMode,
 '!keyup': this.doThisOnce,
 '~!mouseover': this.doThisOnceInCapturingMode
}}>Click Me!</el-button>

```

下面给出的事件修饰符是需要在事件处理函数中写出对应的等价操作：

| 修饰符                              | 处理函数中的等价操作                                                                |
| ----------------------------------- | ----------------------------------------------------------------------------------- |
| .stop                               | event.stopPropagation()                                                             |
| .prevent                            | event.preventDefault()                                                              |
| .self                               | if (event.target !== event.currentTarget) return                                    |
| 按键： .enter, .13                  | if (event.keyCode !== 13) return (对于别的按键修饰符来说，可将 13 改为另一个按键码) |
| 修饰键： .ctrl, .alt, .shift, .meta | if (!event.ctrlKey) return (将 ctrlKey 分别修改为 altKey、shiftKey 或者 metaKey)    |

下面是在事件处理函数中使用修饰符的例子：

``` jsx
export default {
  methods: {
    keyup(e) {
      // 对应`.self`
      if (e.target !== e.currentTarget) return

      // 对应 `.enter`, `.13`
      if (!e.shiftKey || e.keyCode !== 13) return
      
      // 对应 `.stop`
      e.stopPropagation()

      // 对应 `.prevent`
      e.preventDefault()

      // ...
    }
  }
}
```

### ref 和 refInFor

在 Vue 中 ref 被用来给元素或子组件注册引用信息。引用信息将会注册在父组件的 $refs 对象上。如果在普通的 DOM 元素上使用，引用指向的就是 DOM 元素；如果用在子组件上，引用就指向组件。

注意：

- 因为 ref 本身是作为渲染结果被创建的，在初始渲染的时候你不能访问它们 - 它们还不存在
- $refs 不是响应式的，因此你不应该试图用它在模板中做数据绑定。

当 v-for 用于元素或组件的时候，引用信息将是包含 DOM 节点或组件实例的数组。

假如在jsx中想要引用遍历元素或组件的时候，例如：

``` jsx
const LiArray = () => this.options.map(option => (
  <li ref="li" key={option}>{option}</li>
))
```

会发现从 this.$refs.li 中获取的并不是期望的数组值，这个时候就需要使用 refInFor 属性，并置为 true 来达到在模板中 v-for 中使用 ref 的效果：

``` jsx
const LiArray = () => this.options.map(option => (
  <li ref="li" refInFor={true} key={option}>{option}</li>
))
```

### 插槽（v-slot）

在 jsx 中可以使用 this.$slots 来访问静态插槽的内容。

注意：在 Vue 2.6.x 版本后废弃了 slot 和 slot-scope，在模板中统一使用新的统一语法 v-slot 指令。v-slot 只能用于 Vue 组件和 template 标签。

``` jsx
<div class="page-header__title">
  {this.$slots.title ? this.$slots.title : this.title}
</div>
```

等价于模板的

``` vue
<div class="page-header__title">
  <slot name="title">{{ title }}</slot>
</div>
```

在 Vue 官方文档中提到：父级模板里的所有内容都是在父级作用域中编译的；子模板里的所有内容都是在子作用域中编译的。因此像下面的示例是无法正常工作的

``` vue
<current-user>
  {{ user.firstName }}
</current-user>
```

在 `<current-user>` 组件中可以访问到 user 属性，但是提供的内容却是在父组件渲染的。如果想要达到期望的效果，这个时候就需要使用作用域插槽了。下面是改写后的代码，更多知识点可以直接查看官方文档的作用域插槽。

``` vue
<!-- current-user组件定义部分 -->
<span>
  <slot v-bind:user="user">
    {{ user.lastName }}
  </slot>
</span>

<!-- current-user 使用 -->
<current-user>
  <template v-slot:default="slotProps">
    {{ slotProps.user.firstName }}
  </template>
</current-user>
```

上面的示例其实就是官方的示例，这里需要说明的是，其实在Vue中所谓的作用域插槽功能类似于 React 中的 Render Props 的概念，只不过在 React 中我们更多时候不仅提供了属性，还提供了操作方法。但是在 Vue 中更多的是提供数据供父作用域渲染展示，当然我们也可以把方法提供出去，例如：

``` vue
<template>
  <div>
    <slot v-bind:injectedProps="slotProps">
      {{ user.lastName }}
    </slot>
  </div>
</template>

<script>
export default {
  data() {
    return {
      user: {
        firstName: 'snow',
        lastName: 'wolf'
      }
    }
  },

  computed: {
    slotProps() {
      return {
        user: this.user,
        logFullName: this.logFullName
      }
    }
  },

  methods: {
    logFullName() {
      console.log(`${this.firstName} ${this.lastName}`)
    }
  }
}
</script>
```

在父组件中使用：

``` vue
<current-user>
  <template v-slot:default="{ injectedProps }">
    <div>{{ injectedProps.user.firstName }}</div>
    <el-button @click="injectedProps.logFullName">Log Full Name</el-button>
  </template>
</current-user>
```

在上面的代码中我们实际上使用解构的方式来取得 injectedProps，基于解构的特性还可以重命名属性名，在 prop 为 undefined 的时候指定初始值。

``` vue
<current-user v-slot="{ user = { firstName: 'Guest' } }">
  {{ user.firstName }}
</current-user>
```

如果组件只有一个默认的插槽还可以使用缩写语法，将 `v-slot:default="slotProps"` 写成 `v-slot="slotProps"`，命名插槽写成 `v-slot:user="slotProps"`，如果想要动态插槽名还可以写成 `v-slot:[dynamicSlotName]`，此外具名插槽同样也有缩写语法，例如 `v-slot:header` 可以被重写为 `#header`

上面介绍了很多插槽相关的知识点足已说明其在开发过程中的重要性。说了很多在模板中如何定义和使用作用域插槽，现在进入正题如何在 jsx 中同样使用呢？

``` jsx
export default {
  // current-user components
  data() {
    return {
      user: {
        firstName: 'snow',
        lastName: 'wolf'
      }
    }
  },

  computed: {
    slotProps() {
      return {
        user: this.user,
        logFullName: this.logFullName
      }
    }
  },

  methods: {
    logFullName() {
      console.log(`${this.firstName} ${this.lastName}`)
    }
  },

  render() {
    return (
      <div>
        {
          this.$scopedSlots.subTitle({
            injectedProps: this.slotProps
          })
        }
      </div>
    )
  }
}
```

然后在父组件中以 jsx 使用：

``` jsx
<current-user {...{
  scopedSlots: {
    subTitle: ({ injectedProps }) => (
      <div>
        <h3>injectedProps.user</h3>
        <el-button onClick={injectedProps.logFullName}>Log Full Name</el-button>
      </div>
    )
  }
}}></current-user>
```

### 指令

这里需要注意的是在 jsx 中所有 Vue 内置的指令除了 v-show 以外都不支持，需要使用一些等价方式来实现，比如 v-if 使用三目运算表达式、v-for 使用 Array.map() 等。

对于自定义的指令可以使用 v-name={value} 的语法来写，需要注意的是指令的参数、修饰符此种方式并不支持。以官方文档指令部分给出的示例 v-focus 使用为例，介绍二种解决办法：

1. 直接使用对象传递所有指令属性

``` vue
<input type="text" v-focus={{ value: true }} />
```

2. 使用原始的vNode指令数据格式

``` jsx
export default {
  directives：{
    focus: {
      inserted: function(el) {
        el.focus()
      }
    }
  },

  render() {
    const directives = [
      { name: 'focus', value: true }
    ]

    return (
      <div>
        <input type="text" {...{ directives }} />
      </div>
    )
  }
}
```

### 过滤器

过滤器其实在开发过程中用得倒是不多，因为更多时候可以通过计算属性来对数据做一些转换和筛选。这里只是简单提及一下并没有什么可以深究的知识点。

在模板中的用法如下：

``` vue
<!-- 在双花括号中 -->
{{ message | capitalize }}

<!-- 在 `v-bind` 中 -->
<div v-bind:id="rawId | formatId"></div>
```

在jsx中使用方法为：

``` jsx
<div>{this.$options.filters('formatDate')('2019-07-01')}</div>
```

注意：由于 Vue 全局的过滤器只用于模板中，如果需要用于组件的方法中，可以把过滤器方法单独抽离出一个公共Js文件，然后引入组件中，然后用于方法中。

### 一些简单经验分享

并不是说我们在开发 Vue 项目的时候一定要使用 jsx 的方式来写，但是多掌握一种方式来灵活变通，提高工作效率，扩展思路何尝不值得一试。而且，在有些场景下释放 js 的完全编程能力会让你更加能够得心应手。其实在使用模板方式的时候我们并没有完全采用组件的思维方式来做，或者说是做得不彻底，不纯粹，拆分的粒度不够。更多 的时候并没有考虑到组件怎么切分和抽象，多人协作的时候如何处理依赖并明确自己的功能点。

#### 关于DOM属性、HTML属性和组件属性

在 React 中所有数据均挂载在 props 下，Vue 则不然，仅属性就有三种：组件属性 props，普通 html 属性 attrs 和 DOM 属性 domProps。在 Angular 的文档中关于插值绑定部分是重点说明了 DOM 属性和 HTML 属性的区别，在大多数情况下两者都有对应的同名属性，也就是 1:1 映射关系，但是也有例外的情况，比如 HTML 中 colspan，DOM 中的 textContent。HTML 属性的值指定了初始值，并且不能改变，而 DOM 属性的值表示当前值，是可以改变的。

然后在 Vue 的模板语法中是不区分 DOM 属性和 HTML 属性的，例如：

``` vue
<template>
 <div>
  <div>输入的值：{{ title }}</div>
  <input type="text" value="我是DOM属性值" v-model="title" @input="logTitle" />
 </div>
</template>

<script>
export default {
  data() {
    return {
      title: ''
    }
  },

  methods: {
    logTitle(e) {
      // 输出DOM属性
      console.log(e.target.value)
      // 输出HTML属性
      console.log(e.target.getAttribute('value'))
    }
  }
}
</script>
```

运行示例可以看到 input 的初始值被设置为了“我是 DOM 属性值"，当我们在输入框中添加或者删除文字时，HTML 属性始终没有变化，而绑定的 DOM 值一值在变动。然后再看一下在 jsx 中的实现：

``` jsx
<div>输入值：{ this.title }</div>
<input type="text" value="我是DOM属性" v-model={this.title} onInput={this.logTitle} />
```

同样运行后会发现在 jsx 写法中并没有直接将 HTML 属性初始化为 DOM 属性值，即输入框中当前值为空字符串，这符合预期的行为。

此外在模板语法中是无法区分 HTML 属性和 DOM 属性命名一样的场景，但是在 jsx 中可以很好的区分：

``` jsx
<Demo title="我是组件属性" domPropsTitle="我是DOM属性" />
```

结果会就是在 HTML 中显示 title="我是DOM属性，而"我是组件属性” 传递给了组件。

在 React 中 CSS 的样式写义在 jsx 中的语法是以 className="xx" 的形式，而在 Vue 的 jsx 中可以直接写成 class="xx"。实际上由于 class 是 js 的保留字，因此在 DOM 中其属性名为 className 而在 HTML 属性中为 class，我们可以在 Vue 中这样写，经过 Babel 转译后得到正确的样式类名：

``` jsx
<div domPropsClassName="mt__xs"></div>
```

注意：如果同时写了 class="xx" domPropsClassName="yy" 那么后者的优先级较高，和位置无关。所以尽量还是采用 class 的写法。

有使用过 Bootstrap 经验的可能会注意到它里面包含了很多 ARIA 属性，这些属性并不属于 DOM，在 jsx 中可以通过 attrsXX 或者直接 aria-xx 的方式来添加：

``` jsx
<label aria-label="title"></label>
<label attrsAria-label="title"></label>
```

但是上面的换成 domPropsAria-label 就没有任何效果。

注意：在 jsx 中所有 DOM 属性(Property)语法为 domPropsXx, HTML 特性(Attribute)语法为 attrsXx。更多的时候建议还是少使用，或者说合理使用。

在 jsx 中还可以使用混用的写法，例如在组件中写了`<Demo title="demo" />`，还可以定义一个属性对象，然后使用{...props}的方式写在一起，这个时候就会出现属性合并的问题，同样的事件多个地方声明事件处理函数，都会触发。

最后需要提及一点的是，在 Vue 中当给一个组件传了很多 props，但是有的并不是组件声明的，也有可能是一些通用的 HTML 或者 DOM 属性，但是在最终编译后的 HTML 中会直接显示这些 props，如果不希望这些属性显示在最终的 HTML 中，可以在组件中设 inheritAttrs: false。虽然不显示了，但是我们依然可以通过 vm.$attrs 获取所有（除 class 和 style）绑定的属性，包括不在 props 中定义的。

#### 关于事件

前面已经把事件相关的知识点都介绍了，这里主要是提及一下关于 jsx 事件绑定语法 onXx 和组件属性（主要是函数prop）以 on 开头的情况如何处理。

虽然在写组件的时候可以避开命名以 on 开头，但是在使用第三库的时候，如果遇到了该如何处理呢？比如Element组件 Upload 很多钩子都是以 on 开头。 下面提供两种解决办法：

1. 使用展开

``` jsx
<el-upload {...{
  props: {
    onPreview: this.handlePreview
  }
}} />
```

2. 使用propsXx

``` jsx
<el-upload propsOnPreview={this.handlePreview} />
```

推荐使用第二种方式，写起来要简单些。

#### 复杂逻辑条件判断

在模板语法中可以使用 v-if、v-else-if 和 v-else 来做条件判断。在 jsx 中可以通过 ?: 三元运算符(Ternary operator)运算符来做 if-else 判断：

``` jsx
const Demo = () => isTrue ? <p>True!</p> : null
```

然后可以利用 && 运算符的特性简写为：

``` jsx
const Demo = () => isTrue && <p>True!</p>
```

对于复杂的条件判断，例如：

``` jsx
const Demo = () => (
  <div>
    {
      flag && flag2 && !flag3
        ? flag4
          ? <p>aa</p>
          : flag5
            ? <p>Meh</p>
            : <p>hErp</p>
        : <p>bb</p>
    }
  </div>
)
```

可以采用几种方式来降低判断识别的复杂度：

- 最好的办法：将判断逻辑转移到子组件
- 可选的 hacky 方法：使用 IIFE (立即执行表达式)
- 使用第三方库解决：`jsx-control-statements`

下面是使用 IIFE 通过内部使用 if-else 返回值来优化上述问题：

``` jsx
const Demo = () => (
  <div>
    {
      (() => {
        if (flag && flag2 &&!flag3) {
          if (flag4) {
            return <p>Blah</p>
          } else if (flag5) {
            return <p>Meh</p>
          } else {
            return <p>Herp</p>
          }
        } else {
          return <p>A</p>
        }
      })()
    }
  </div>
)
```

还可以使用 do 表达式，但是需要插件 @babel/plugin-proposal-do-expressions 的转译来支持

``` jsx
const Demo = () => (
  <div>
    {
      do {
        if (flag1 && flag2 && !flag3) {
          if (flag4) {
            <p>Blah</p>
          } else if (flag5) {
            <p>Meh</p>
          } else {
            <p>Herp</p>
          }
        } else {
          <p>A</p>
        }
      }
    }
  </div>
)
```

再就是一种比较简单的可选办法，如下：

``` jsx
const Demo = () => {
  const basicCondition = flag && flag1 && !flag3;
  if (!basicCondition) return <p>A</p>
  if (flag4) return <p>Blah</p>
  if (flag5) return <p>Meh</p>
  return <p>Herp</p>
}
```

### 组件的传值

在单个 jsx 文件中可以写很多函数式组件来切分更小的粒度，例如之前的文章 Vue 后台管理系统开发日常总结__组件PageHeader，组件的形态有两种，一种是普通标题，另一种是带有选项卡的标题，那么在写的时候就可以这样写：

``` jsx
render() {
  // partial html
  const TabHeader = (
    <div class="page-header page-header--tab"></div>
  )

  // function partial
  const Header = () => (
    <div class="page-header"></div>
  )

  <div class="page-header">
    {this.withTab ? TabHeader : <Header/>}
  </div>
}
```

注意在拆分的时候，如果不需要做任何判断可以纯粹是 HTML 片段赋值给变量，如果需要条件判断就使用函数式组件的方式来写。需要注意的是由于 render 函数会多次被调用，写的时候注意一下对性能的影响，目前能力有限这方面就不作展开了。

既然使用函数式组件，那么同样可以在函数中传递参数了，参数是一个对象中，包含了以下属性：

```
children       # VNode数组，类似于React的children
data           # 绑定的属性
attrs          # Attribute
domProps       # DOM property
on             # 事件
injection      # 注入的对象
listeners:     # 绑定的事件类型
click          # 点击事件
parent         # 父组件
props          # 属性
scopedSlots    # 对象，作用域插槽，使用中发现作用域插槽也挂在这个下面
slots          # 函数，插槽
```

虽然可以在函数式组件中传参数、事件、slot 但是个人觉得不建议这样做，反而搞复杂了。

``` jsx
render() {
  const Demo = props => {
    return (
      <div>
        <h3>Jsx中的内部组件 { props.data.title }</h3>
        { props.children }
        <br />
        { props.scopedSlots.bar() }
      </div>
    )
  }

  return (
    <div>
      <Demo title="test" attrsA="a" domPropsB="b" onClick={this.demo}>
        <h3>我是Children</h3>
        <template slot="bar">
          <p>我是Slot内容</p>
        </template>
      </Demo>
    </div>
  )
}
```

上面的示例最终生成的HTML中会将 `<template>` 的内容转换为 #document-fragment。