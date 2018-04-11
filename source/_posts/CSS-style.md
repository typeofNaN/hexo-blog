---
title: CSS样式各属性书写顺序规范
date: 2018-04-11 21:25:45
categories: 技术文章
tags:
    - CSS
    - web前端
---

本文是我创建的第一篇博客，整理一下前端开发三大工具之一------CSS的常用属性及书写顺序规范

## 快速记忆
### 口诀
状定形边字背其，上右下左顺时针。
<!-- more -->
    * 状（状态）
    * 定（定位）
    * 形（形状）
    * 边（边框）
    * 字（字体）
    * 背（背景)
    * 其（其他）

属性越前则优先级越高。

## 规范书写顺序原因
### 目的
减少浏览器reflow（回流），提升浏览器渲染dom的性能

### 原理
浏览器的渲染流程为——
1.解析html构建dom树，解析css构建css树：将html解析成树形的数据结构，将css解析成树形的数据结构；
2.构建render树：DOM树和CSS树合并之后形成的render树；
3.布局render树：有了render树，浏览器已经知道那些网页中有哪些节点，各个节点的css定义和以及它们的从属关系，从而计算出每个节点在屏幕中的位置；
4.绘制render树：按照计算出来的规则，通过显卡把内容画在屏幕上。
css样式解析到显示至浏览器屏幕上就发生在②③④步骤，可见浏览器并不是一获取到css样式就立马开始解析而是根据css样式的书写顺序将之按照dom树的结构分布render样式，完成第②步，然后开始遍历每个树结点的css样式进行解析，此时的css样式的遍历顺序完全是按照之前的书写顺序。在解析过程中，一旦浏览器发现某个元素的定位变化影响布局，则需要倒回去重新渲染正如按照这样的书写书序：
``` css
width: 100px;

height: 100px;

background-color: red ;

position: absolute;
```
当浏览器解析到position的时候突然发现该元素是绝对定位元素需要脱离文档流，而之前却是按照普通元素进行解析的，所以不得不重新渲染，解除该元素在文档中所占位置，然而由于该元素的占位发生变化，其他元素也可能会受到它回流的影响而重新排位。最终导致③步骤花费的时间太久而影响到④步骤的显示，影响了用户体验。

所以规范的的css书写顺序对于文档渲染来说一定是事半功倍的！

## 详细解析
### 状态类
    * display: block | none | inline | compact | marker | inline-table | list-item | run-in | table | table-caption | table-cell | table-column | table-column-group | table-footer-group | table-header-group | table-row | table-row-grou 
    * visibility: inherit | visible | collapse | hidden  
    * float: none | left | right  
    * clear: none | left | right | both 
    * overflow: visible | auto | hidden | scroll 

### 定位类
    * position: static | absolute | fixed | relative  
    * top：auto | length  
    * right: auto | length  
    * bottom: auto | length 
    * left: auto | length  
    * z-index: auto | number 

### 形状类
#### 宽
    * width: visible | auto | hidden | scroll 
    * min-width: visible | auto | hidden | scroll 
    * max-width: visible | auto | hidden | scroll 
#### 高
    * height: auto | length 
    * min-height: auto | length 
    * max-height: auto | length 

### 边框类
#### maigin
    * margin: auto | length 
    * margin-top: auto | length 
    * margin-right: auto | length 
    * margin-bottom: auto | length 
    * margin-left: auto | length 
#### padding
    * padding: auto | length 
    * padding-top: auto | length 
    * padding-right: auto | length 
    * padding-bottom: auto | length 
    * padding-left: auto | length 
#### border
##### 四边
    * border: border-width || border-style || border-color 
    * border-top: border-width || border-style || border-color 
    * border-right: border-width || border-style || border-color 
    * border-bottom: border-width || border-style || border-color 
    * border-left: border-width || border-style || border-color 
##### 边宽
    * border-width: none | hidden | dotted | dashed | solid | double | groove | ridge | inset | outset 
    * border-top-width: medium | thin | thick | length 
    * border-right-width: medium | thin | thick | length 
    * border-bottom-width: medium | thin | thick | length 
    * border-left-width: medium | thin | thick | length 
##### 边样式
    * border-style: none | hidden | dotted | dashed | solid | double | groove | ridge | inset | outset 
    * border-top-style: border-width || border-style || border-color 
    * border-right-style: border-width || border-style || border-color 
    * border-bottom-style: border-width || border-style || border-color 
    * border-left-style: border-width || border-style || border-color 
##### 边颜色
    * border-color: color 
    * border-top-color: color 
    * border-right-color: color 
    * border-bottom-color: color 
    * border-left-color: color 
##### 边圆角
    * border-radius: length 
##### 外边线
    * outline: outline-color || outline-style || outline-width 
    * list-style: list-style-image || list-style-position || list-style-type 
##### 其他边
    * table-layout: auto | fixed 
    * caption-side: top | right | bottom | left 
    * border-collapse: separate | collapse 
    * border-spacing: length 
    * empty-cells: show | hide 

### 字体类
    * font: caption | icon | menu | message-box | small-caption | status-bar 
    * font-family: ncursive | fantasy | monospace | serif | sans-serif 
    * font-size: xx-small | x-small | small | medium | large | x-large | xx-large | larger | smaller | length 
    * line-height: normal | length 
    * font-weight: normal | bold | bolder | lighter | 100 | 200 | 300 | 400 | 500 | 600 | 700 | 800 | 900  
    * text-align: left | right | center | justify 
    * text-indent: length 
    * text-transform: none | capitalize | uppercase | lowercase 
    * text-decoration: none || underline || blink || overline || line-through 
    * letter-spacing: normal | length 
    * word-spacing: normal | length 
    * white-space: normal | pre | nowrap 
    * vertical-align: auto | baseline | sub | super | top | text-top | middle | bottom | text-bottom | length 
    * color: color 

### 背景类
    * background: background-color || background-image || background-repeat || background-attachment || background-position
    * background-color: transparent | color 
    * background-image: none | url ( url ) 
    * background-repeat: repeat | no-repeat | repeat-x | repeat-y
    * background-position: length || length || position 

 
### 其他
    * opacity：0-1
    * cursor: auto | all-scroll | col-resize| crosshair | default | hand | move | help | no-drop | not-allowed | pointer | progress | row-resize | text | vertical-text | wait | *-resize | url ( url ) 
    * content: attr(alt) | counter(name) | counter(name , list-style-type) | counters(name , string) | counters(name , string , list-style-type) | no-close-quote | no-open-quote | close-quote | open-quote | string | url(url)
    * quotes: none | string 
