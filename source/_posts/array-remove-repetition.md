---
title: 数组去重
date: 2019-10-19 14:03:50
categories: 技术文章
tags:
    - JavaScript
    - Array
    - Array Unique
---

此文介绍数组去重方法。

<!-- more -->

## 利用ES6 Set去重（ES6中最常用）

``` js
function unique(arr) {
  return Array.from(new Set(arr))
}

const arr = [1, 1, 'true', 'true', true, true, 15, 15, false, false, undefined, undefined, null, null, NaN, NaN, 'NaN', 'NaN', 0, 0, 'a', 'a', {}, {}]

console.log(unique(arr))

// [1, 'true', true, 15, false, undefined, null, NaN, 'NaN', 0, 'a', {}, {}]
// 不考虑兼容性，这种去重的方法代码最少。这种方法还无法去掉“{}”空对象，后面的高阶方法会添加去掉重复“{}”的方法。
```

## 利用for嵌套for，然后splice去重（ES5中最常用）

``` js
function unique(arr) {
  for (let i = 0; i < arr.length; i++) {
    for (let j = i + 1; j < arr.length; j++) {
      if (arr[i] === arr[j]) {    // 第一个等同于第二个，splice方法删除第二个
        arr.splice(j, 1)
        j--
      }
    }
  }

  return arr
}

const arr = [1, 1, 'true', 'true', true, true, 15, 15, false, false, undefined, undefined, null, null, NaN, NaN, 'NaN', 'NaN', 0, 0, 'a', 'a', {}, {}]

console.log(unique(arr))

// [1, 'true', 15, false, undefined, NaN, NaN, 'NaN', 'a', {…}, {…}]
// NaN和{}没有去重，两个null直接消失了
// 双层循环，外层循环元素，内层循环时比较值。值相同时，则删去这个值。
```

## 利用indexOf去重

``` js
function unique(arr) {
  if (!Array.isArray(arr)) {
    console.log('type error!')
    return
  }

  const array = []

  for (let i = 0; i < arr.length; i++) {
    if (array.indexOf(arr[i]) === -1) {
      array.push(arr[i])
    }
  }

  return array
}

const arr = [1, 1, 'true', 'true', true, true, 15, 15, false, false, undefined, undefined, null, null, NaN, NaN, 'NaN', 'NaN', 0, 0, 'a', 'a', {}, {}]

console.log(unique(arr))

// [1, 'true', true, 15, false, undefined, null, NaN, NaN, 'NaN', 0, 'a', {…}, {…}]
// NaN、{}没有去重
// 新建一个空的结果数组，for 循环原数组，判断结果数组是否存在当前元素，如果有相同的值则跳过，不相同则push进数组。
```

## 利用sort()

``` js
function unique(arr) {
  if (!Array.isArray(arr)) {
    console.log('type error!')
    return
  }

  arr = arr.sort()
  const array = [arr[0]]

  for (let i = 1; i < arr.length; i++) {
    if (arr[i] !== arr[i - 1]) {
      array.push(arr[i])
    }
  }

  return array
}

const arr = [1, 1, 'true', 'true', true, true, 15, 15, false, false, undefined, undefined, null, null, NaN, NaN, 'NaN', 'NaN', 0, 0, 'a', 'a', {}, {}]

console.log(unique(arr))

// [0, 1, 15, 'NaN', NaN, NaN, {…}, {…}, 'a', false, null, true, 'true', undefined]
// NaN、{}没有去重
// 利用sort()排序方法，然后根据排序后的结果进行遍历及相邻元素比对。
```

## 利用对象的属性不能相同的特点进行去重（这种数组去重的方法有问题，不建议用，有待改进）

``` js
function unique(arr) {
  if (!Array.isArray(arr)) {
    console.log('type error!')
    return
  }

  const array = []
  const obj = {}

  for (let i = 0; i < arr.length; i++) {
    if (!obj[arr[i]]) {
      array.push(arr[i])
      obj[arr[i]] = 1
    } else {
      obj[arr[i]]++
    }
  }

  return array
}

const arr = [1, 1, 'true', 'true', true, true, 15, 15, false, false, undefined, undefined, null, null, NaN, NaN, 'NaN', 'NaN', 0, 0, 'a', 'a', {}, {}]

console.log(unique(arr))

// [1, 'true', 15, false, undefined, null, NaN, 0, 'a', {…}]
// 两个true直接去掉了，NaN和{}去重
```

## 利用includes

``` js
function unique(arr) {
  if (!Array.isArray(arr)) {
    console.log('type error!')
    return
  }

  const array =[]

  for (let i = 0; i < arr.length; i++) {
    if ( !array.includes( arr[i]) ) {    // includes 检测数组是否有某个值
      array.push(arr[i])
    }
  }

  return array
}

const arr = [1, 1, 'true', 'true', true, true, 15, 15, false, false, undefined, undefined, null, null, NaN, NaN, 'NaN', 'NaN', 0, 0, 'a', 'a', {}, {}]

console.log(unique(arr))

// [1, 'true', true, 15, false, undefined, null, NaN, 'NaN', 0, 'a', {…}, {…}]
// {}没有去重
```

## 利用hasOwnProperty

``` js
function unique(arr) {
  const obj = {}
  return arr.filter(function (item, index, arr) {
    return obj.hasOwnProperty(typeof item + item) ? false : (obj[typeof item + item] = true)
  })
}

const arr = [1, 1, 'true', 'true', true, true, 15, 15, false, false, undefined, undefined, null, null, NaN, NaN, 'NaN', 'NaN', 0, 0, 'a', 'a', {}, {}]

console.log(unique(arr))

// [1, 'true', true, 15, false, undefined, null, NaN, 'NaN', 0, 'a', {…}]
// 所有的都去重了
// 利用hasOwnProperty 判断是否存在对象属性
```

## 利用filter

``` js
function unique(arr) {
  return arr.filter(function (item, index, arr) {
    // 当前元素，在原始数组中的第一个索引 == 当前索引值，否则返回当前元素
    return arr.indexOf(item, 0) === index
  })
}

const arr = [1, 1, 'true', 'true', true, true, 15, 15, false, false, undefined, undefined, null, null, NaN, NaN, 'NaN', 'NaN', 0, 0, 'a', 'a', {}, {}]

console.log(unique(arr))

// [1, 'true', true, 15, false, undefined, null, 'NaN', 0, 'a', {…}, {…}]
```

## 利用递归去重

``` js
function unique(arr) {
  const array = arr
  const len = array.length

  array.sort(function (a, b) {    // 排序后更加方便去重
    return a - b
  })

  function loop(index) {
    if (index >= 1) {
      if (array[index] === array[index - 1]) {
        array.splice(index, 1)
      }

      loop(index - 1)    // 递归loop，然后数组去重
    }
  }

  loop(len - 1)

  return array
}

const arr = [1, 1, 'true', 'true', true, true, 15, 15, false, false, undefined, undefined, null, null, NaN, NaN, 'NaN', 'NaN', 0, 0, 'a', 'a', {}, {}]

console.log(unique(arr))

// [1, 'a', 'true', true, 15, false, 1, {…}, null, NaN, NaN, 'NaN', 0, 'a', {…}, undefined]
```

## 利用Map数据结构去重

``` js
function unique(arr) {
  const map = new Map()
  const array = []    // 数组用于返回结果

  for (let i = 0; i < arr.length; i++) {
    if (map.has(arr[i])) {    // 如果有该key值
      map.set(arr[i], true)
    } else {
      map.set(arr[i], false)    // 如果没有该key值
      array.push(arr[i])
    }
  }

  return array
}

const arr = [1, 1, 'true', 'true', true, true, 15, 15, false, false, undefined, undefined, null, null, NaN, NaN, 'NaN', 'NaN', 0, 0, 'a', 'a', {}, {}]

console.log(unique(arr))

// [1, 'a', 'true', true, 15, false, 1, {…}, null, NaN, NaN, 'NaN', 0, 'a', {…}, undefined]
// 创建一个空Map数据结构，遍历需要去重的数组，把数组的每一个元素作为key存到Map中。由于Map中不会出现相同的key值，所以最终得到的就是去重后的结果。
```

## 利用reduce+includes

``` js
function unique(arr) {
  return arr.reduce((prev,cur) => prev.includes(cur) ? prev : [...prev, cur], [])
}

const arr = [1, 1, 'true', 'true', true, true, 15, 15, false, false, undefined, undefined, null, null, NaN, NaN, 'NaN', 'NaN', 0, 0, 'a', 'a', {}, {}]

console.log(unique(arr))

// [1, 'true', true, 15, false, undefined, null, NaN, 'NaN', 0, 'a', {…}, {…}]
```

## […new Set(arr)]

``` js
[...new Set(arr)]

// 代码就是这么少----（其实，严格来说并不算是一种，相对于第一种方法来说只是简化了代码）
```

方法多种多样！