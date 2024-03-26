---
title: Observer API
date: 2021-11-23 11:56:23
categories: 技术文章
tags:
    - Observer API
---

Observer API（观察者API）释放了Web隐藏的超能力，以创建真正的响应式体验，从懒加载关键内容到非侵入式性能监控。
在检测变化方面非常方便，可以用来创建响应式应用。

<!-- more -->

有四种不同类型的观察者可以观察不同的东西——从DOM到浏览器性能。

## MutationObserver

MutationObserver观察DOM树，监听DOM的变化。

``` js
// 选择要观察突变的节点
const targetNode = document.getElementById('element')

// 观察者的选项（观察哪些突变）
const config = {
  attributes: true,
  childList: true,
  subtree: true,
}

// 创建一个观察者实例，链接到一个回调，以便在观察到突变时执行。
const observer = new MutationObserver((mutations, observer) => {
  mutations.forEach(mutation => {
    if (mutation.type === 'childList') {
      console.log('A child node has been added or removed.')
    } else if (mutation.type === 'attributes') {
      console.log(`The ${mutation.attributeName} attribute was modified.`)
    }
  })
})

// 开始观察目标节点的配置突变情况
observer.observe(targetNode, config)

// 之后，你可以停止观察
observer.disconnect()
```

当一个元素的属性、文本或内容发生变化时，我们会得到通知，同时也会监控子节点是否被添加或删除。

这对于调整DOM中元素的大小以及重置DOM值特别有用。

## IntersectionObserver

IntersectionObserver观察一个DOM元素的可见性，监听其位置的变化。

``` js
// 选择要观察突变的节点
const targetNode = document.getElementById('element')

// 观察者的选项（观察哪些突变）
const config = {
  rootMargin: '-100% 0px 0px 0px',
}

// 创建一个观察者实例，链接到一个回调，以便在观察到突变时执行。
const intersectionObserver = new IntersectionObserver((entries, observer) => {
  entries.forEach(entry => {
    if (entry.isIntersecting) {
      console.log('Observing.')

      // 之后，你可以停止观察
      observer.unobserve(entry.target)
    }
  })
})

// 开始观察
intersectionObserver.observe(targetNode, config)
```

这在基于目标元素的可见性和位置的懒惰加载和动画内容方面非常有用。

## ResizeObserver

ResizeObserver观察元素的内容或边框，监听元素及其子元素的变化。

``` js
// 选择要观察突变的节点
const targetNode = document.getElementById('element')

const resizeObserver = new ResizeObserver((entries, observer) => {
  entries.forEach(entry => {
    console.log(`Element size: ${entry.width}px ${entry.height}px`)
    console.log(`Element padding: ${entry.top}px ${entry.left}px`)

    // 之后，你可以停止观察
    observer.unobserve(entry.target)
  })
})

// 开始观察
resizeObserver.observe(targetNode)
```

创建基于输入或触发器包装的动态内容时，此观察者非常重要。

## PerformanceObserver

PerformanceObserver观察性能测量事件，监听新的性能条目。

``` js
// 观察者的选项（观察哪些突变）
const config = {
  entryTypes: ['resource', 'mark', 'measure']
};

const observer = new PerformanceObserver(list => {
  list.getEntries().forEach(entry => {
    // 在控制台上显示每个报告的测量
    console.log(
      `Name: ${entry.name}`,
      `Type: ${entry.entryType}`,
      `Start: ${entry.startTime}`,
      `Duration: ${entry.duration}`,
    )
  })
})

// 开始观察
observer.observe(config)
performance.mark('registered-observer')
```

这对于接收性能通知很有用，可以在空闲时间运行，而不与关键的渲染工作竞争。