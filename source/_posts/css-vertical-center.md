---
title: CSS垂直居中
date: 2018-09-06 10:19:48
categories: 技术文章
tags:
    - CSS
    - Vertical Center
---

# 前言

因为在工作中前端开发布局这方面涉及到的css垂直居中问题比较多，便总结了一下。

<!-- more -->

# 1、Line-height

适用情景：单行文字垂直居中技巧

这个方式应该是最多人知道的了，常见于单行文字的应用，像是按钮这一类对象，或者是下拉框、导航此类元素最常见到的方式了。此方式的原理是在于将单行文字的行高设定后，文字会位于行高的垂直中间位置，利用此原理就能轻松达成垂直居中的需求了。

``` html
<style>
  .content {
    width: 400px;
    background: #ccc;
    line-height:100px;
    margin: auto;
  }
</style>
<div class="content">CSS垂直居中</div>
```

# 2、Line-height + inline-block

适用情景：多对象的垂直居中技巧

既然可以使用第一种方式对行元素达成垂直居中的话，当然没有理由不能做到多行啊~但是你需要将多个元素或多行元素当成一个行元素来看待，所以我们必须要将这些数据多包一层，并将其设定为inline-block，并在该inline-block对象的外层对象使用inline-block来代替height的设置，如此便可以达到垂直居中的目的了，从使你的数据是包含了标题跟内容在内也可以正常的垂直居中了。

``` html
<style>
  h2 {
    text-align: center;
  }
  .box {
    width: 500px;
    border: 1px solid #f00;
    margin: auto;
    line-height: 200px;
    text-align: center;
  }
  .box2 .content {
    display: inline-block;
    height: auto;
    line-height:1;
    width: 400px;
    background: #ccc;
  }
</style>
<div class="box box2">
  <div class="content">
    CSS垂直居中
  </div>
</div>
```

# 3、:before + inline-block

适用情景：多对象的CSS垂直居中技巧

:before 伪类元素搭配 inline-block 属性的写法应该是很传统的垂直居中的技巧了，此方式的好处在于子元素居中可以不需要特别设定高度，我们将利用:before伪类元素设定为100%高的inline-block，再搭配上将需要居中的子元素同样设置成inline-block性质后，就能使用vertical-align:middle来达到垂直居中的目的了，此方式在以往其实是个非常棒的垂直居中解决方案，唯独需要特别处理掉inline-block元素之间的4-5px空间这个小缺陷，但也很实用了。

``` html
<style>
  h2 {
    text-align: center;
  }
  .box {
    width: 500px;
    height: 250px;
    border: 1px solid #f00;
    margin: auto;
    text-align: center;
  }
  .box::before {
    content:'';
    display: inline-block;
    height: 100%;
    width: 0;
    vertical-align: middle;
  }
  .box .content {
    width: 400px;
    background: #ccc;
    display: inline-block;
    vertical-align: middle;
  }
</style>
<h2>3.:before + inline-block</h2>
<div class="box box3">
  <div class="content">
    CSS垂直居中
  </div>
</div>
```

# 4、absolute + margin 负值

适用情景：多行文字的垂直居中技巧

谁说绝对定位要少用？Amos认为没有少用多用的问题，重点在于你是否有妥善运用才是重点，绝对定位在这个例子中会设置top:50%来抓取空间高度的50%，接着在将居中元素的margin-top设定为负一半的高度，这样就能让元素居中了，此方法可是自古以来流传多年的居中方式呢？

``` html
<style>
  h2 {
    text-align: center;
  }
  .box {
    width: 500px;
    height: 250px;
    border: 1px solid #f00;
    margin: auto;
    position: relative;
  }
  .box4 .content {
    width: 400px;
    background: #ccc;
    height: 70px;
    position: absolute;
    top:50%;
    left: 50%;
    margin-left: -200px;
    margin-top: -35px;
  }
</style>
<h2>4.absolute + margin 負值</h2>
<div class="box box4">
  <div class="content">
    CSS垂直居中
  </div>
</div>
```

# 5、absolute + margin auto

适用情景：多行文字的垂直居中技巧

又一个绝对定位的垂直居中的方案，这个方式比较特别一点，当元素设置为绝对定位后，假设它是抓不到整体可运用的空间范围，所以margin:auto会失效，但当你设置了top:0;bottom:0;时，绝对定位元素就抓到了可运用的空间了，这时你的margin:auto就生效了（神奇吧），如果你的绝对定位元素需要水平居中于父层，那你同样可以设定left:0;right:0;来让绝对定位元素取得空间可运用范围，再让margin-left与margin-right设定为auto即可居中。但此方式的缺点是你的定位元素必须有固定的宽高（百分比也算）才能正常居中。

``` html
<style>
  h2 {
    text-align: center;
  }
  .box {
    width: 500px;
    height: 250px;
    border: 1px solid #f00;
    margin: auto;
    position: relative;
  }
  .content {
    width: 400px;
    background: #ccc;
    height: 70px;
    position: absolute;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    margin: auto;
  }
</style>
<h2>5.absolute + translate(-50%, -50%)</h2>
<div class="box box5">
  <div class="content">
    CSS垂直居中
  </div>
</div>
```

# 6、absolute + translate

适用情景：多行文字的垂直居中技巧

在一个绝对定位居中的方式，此方式应该算是最方便的了，因为此居中的定位元素不需要固定的宽高，我们利用绝对定位时的top 与right设置元素的上方跟左方各为50%，再利用translate(-50%,-50%)位移居中元素自身宽与高的50%就能达成居中的目的了。（css3好棒）

``` html
<style>
  h2 {
    text-align: center;
  }
  .box {
    width: 500px;
    height: 250px;
    border: 1px solid #f00;
    margin: auto;
    position: relative;
  }
  .box5 .content {
    width: 400px;
    background: #ccc;
    position: absolute;
    top:50%;
    left: 50%;
    transform: translate(-50%, -50%);
  }
</style>
<h2>6.absolute + margin: auto</h2>
<div class="box box6">
  <div class="content">
    CSS垂直居中
  </div>
</div>
```

# 7、Flex + align-items

适用情景：多行文字的垂直居中技巧

Flex！前端的毒品！后端的宝物！这东西自从面世之后就不断的考验网页开发者的良心，到底要不要抛弃float拥抱flex，我想这答案人人心中自由一把尺，但先碰Flex再碰Float可谓先甜后苦，这顺序到底要倒吃甘蔗还是正吃甘蔗是实在难说，自从有了Flex之后，小孩考试一百分，设计网页不跑版，客户网页都RWD，老板赚钱好开心，我也加薪（加班）好甘心，不由的说Flex真的是一个神物，我们只要设定父层display:flex以及设定次轴(cross axis)属性align-items:center 就好了（说那么多结果重点就一行字是哪招啦），这个方式的优点是此层不需要设定高度即可自动居中，且原始代码干净无比，真的是用一次就让你升天啦。

``` html
<style>
  h2 {
    text-align: center;
  }
  .box {
    width: 500px;
    height: 250px;
    border: 1px solid #f00;
    margin: auto;
    display: flex;
    justify-content: center;
    align-items: center; 
  }
  .content {
    width: 400px;
    background: #ccc;
  }
</style>
<h2>7.Flex + align-items</h2>
<div class="box box7">
  <div class="content">
    CSS垂直居中
  </div>
</div>
```

# 8、Flex + :before + flex-grow

适用情景：多行文字的垂直居中技巧

Flex有多种方式可以让你把数据居中，适用Flex-grow的延展特性来达成，这个例子中Amos适用了flex-direction:column直式排法，搭配:before伪元素适用flex-grow伸展值能够取得剩下所有空间的特性，把它设定成一半的剩余空间就能做到把内容数据准确的推到垂直中间位置，算是个传统技法的延伸方式。这样的话上面第七个方式不是比较快？

``` html
<style>
  h2 {
    text-align: center;
  }
  .box {
    width: 500px;
    height: 250px;
    border: 1px solid #f00;
    margin: auto;
    display: flex;
    flex-direction: column;
    align-items: center;
  }
  .box:before {
    content: '';
    flex-grow: .5;
  }
  .content {
    width: 400px;
    background: #ccc;
  }
</style>
<h2>8.Flex + before + flex-grow</h2>
<div class="box box8">
  <div class="content">
    CSS垂直居中
  </div>
</div>
```

# 9、Flex + margin

适用情景：多行文字的垂直居中技巧

继续用Flex来居中，由于Flex元素对空间解读的特殊性，我们只要在父层元素设定display:flex，接着在需要垂直居中的元素上设定margin:auto，即可自动居中

``` html
<style>
  h2 {
    text-align: center;
  }
  .box {
    width: 500px;
    height: 250px;
    border: 1px solid #f00;
    margin: auto;
    display: flex;
  }
  .content {
    width: 400px;
    background: #ccc;
    margin: auto;
  }
</style>
<h2>9.Flex + margin</h2>
<div class="box box9">
  <div class="content">
    CSS垂直居中
  </div>
</div>
```

# 10、Flex + align-self

适用情景：多行文字的垂直居中技巧

align-self 应该大家都不陌生，基本上就是对flex次轴cross axis 的个别对齐方式只要对单一子层元素设定align-self:center就能达成垂直居中的目的了。

``` html
<style>
  h2 {
    text-align: center;
  }
  .box {
    width: 500px;
    height: 250px;
    border: 1px solid #f00;
    margin: auto;
    display: flex;
    justify-content: center;
  }
  .content {
    width: 400px;
    background: #ccc;
    align-self: center
  }
</style>
<h2>10.Flex + align-self</h2>
<div class="box box10">
  <div class="content">
    CSS垂直居中
  </div>
</div>
```

# 11、Flex + align-content

适用情景：多行文字的垂直居中技巧

在正常的状况下，align-content 仅能对次轴多行flex item做居中，但是当我今天子层元素不确定有多少个时，且有时可能会有单个的情况出现时，此技巧就能用到了（当然你也能有其他解法），既然是多行子元素才能用，那我们就为单个子组件多加两个兄弟吧，使用:before以及:after 来让子元素增加到多个，这样就能使用flex的align-content属性来居中

``` html
<style>
  h2 {
    text-align: center;
  }
  .box {
    width: 500px;
    height: 250px;
    border: 1px solid #f00;
    margin: auto;
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    align-content: center;
  }
  .content {
    width: 400px;
    background: #ccc;
  }
  .box11:before,
  .box11:after {
    content: '';
    display: block;
    width:100%;
  }
</style>
<h2>11.Flex + align-content</h2>
<div class="box box11">
  <div class="content">
    CSS垂直居中
  </div>
</div>
```

# 12、Grid + template

适用情景：多行文字的垂直居中技巧

CSS Grid最令人惊讶的就是这个template的功能了，简直就是把块元素当画布在使用，我们仅需要把模板设置成三列，就能搞定垂直居中了

``` html
<style>
  h2 {
    text-align: center;
  }
  .box {
    width: 500px;
    height: 250px;
    border: 1px solid #f00;
    margin: auto;
    display: grid;
    grid-template-rows: 1fr auto 1fr;
    grid-template-columns: 1fr auto 1fr;
    grid-template-areas: 
      '. . .'
      '. amos .'
      '. . .';
  }
  .content {
    width: 400px;
    background: #ccc;
    grid-area: amos;
  }
</style>
<h2>12.Grid + template</h2>
<div class="box box12">
  <div class="content">
    CSS垂直居中
  </div>
</div>
```

# 13、Grid + align-items

适用情景：多行文字的垂直居中技巧

align-items不仅是Flex可用，连CSS Grid也拥有此属性可使用，但在Flex中align-items是针对次轴cross axis作对齐，而在CSS Grid中则是针对Y轴做对齐，你可以把它想象成是表格中储存单元格的vertical-align属性看待，就可以很好理解了

``` html
<style>
  h2 {
    text-align: center;
  }
  .box {
    width: 500px;
    height: 250px;
    border: 1px solid #f00;
    margin: auto;
    display: grid;
    justify-content: center;
    align-items: center; 
  }
  .content {
    width: 400px;
    background: #ccc;
  }
</style>
<h2>13.Grid + align-items</h2>
<div class="box box13">
  <div class="content">
    CSS垂直居中
  </div>
</div>
```

# 14、Grid + align-content

适用情景：杜航文字的垂直居中技巧

CSS Grid的align-content跟Flex的align-content有点差异，CSS Grid对于空间的解释会跟Flex有一些些的落差，所以导致align-content在Flex中仅能针对多行元素起作用，但在Grid中就没这个问题，所以我们可以很开心的使用align-content来对子元素做垂直居中，丝毫不费力气

``` html
<style>
  h2 {
    text-align: center;
  }
  .box {
    width: 500px;
    height: 250px;
    border: 1px solid #f00;
    margin: auto;
    display: grid;
    justify-content: center;
    align-content: center; 
  }
  .content {
    width: 400px;
    background: #ccc;
  }
</style>
<h2>14.Grid + align-content</h2>
<div class="box box14">
  <div class="content">
    CSS垂直居中
  </div>
</div>
```

# 15、Grid + align-self

适用情景：多行文字的垂直居中技巧

align-self 应该大家都不陌生，基本上就是对grid Y轴的个别对齐方式，只要对单一子层元素设置为align-self:center就能达成垂直居中的目的了

``` html
<style>
  h2 {
    text-align: center;
  }
  .box {
    width: 500px;
    height: 250px;
    border: 1px solid #f00;
    margin: auto;
    display: grid;
    justify-content: center;
  }
  .content {
    width: 400px;
    background: #ccc;
    align-self: center;
  }
</style>
<h2>15.Grid + align-self</h2>
<div class="box box15">
  <div class="content">
    CSS垂直居中
  </div>
</div>
```

# 16、Grid + place-items

适用情景：多行文字的垂直居中技巧

place-items这属性不知道有多少人用过，此属性是align-items与justify-items的缩写，简单的说就是水平与垂直的对齐方式，想当然的，设定center就能居中

``` html
<style>
  h2 {
    text-align: center;
  }
  .box {
    width: 500px;
    height: 250px;
    border: 1px solid #f00;
    margin: auto;
    display: grid;
    height: 150px;
    margin: 0 auto;
    place-items: center;
  }
  .content {
    width: 400px;
    background: #ccc;
  }
</style>
<h2>16.Grid + place-items</h2>
<div class="box box16">
  <div class="content">
    CSS垂直居中
  </div>
</div>
```

# 17、Grid + place-content

适用情景：多行文字的垂直居中技巧

place-content这属性有多少人用过，此属性是align-content与justify-content的缩写，简单的说就是水平与垂直的对齐方式，想当然的，设置center就能居中了

``` html
<style>
  h2 {
    text-align: center;
  }
  .box {
    width: 500px;
    height: 250px;
    border: 1px solid #f00;
    margin: auto;
    display: grid;
    height: 150px;
    margin: 0 auto;
    place-content: center;
  }
  .content {
    width: 400px;
    background: #ccc;
  }
</style>
<h2>17.Grid + place-content</h2>
<div class="box box17">
  <div class="content">
    CSS垂直居中
  </div>
</div>
```

# 18、Grid + margin

适用情景：多行文字的垂直居中技巧

继续用Grid来居中，由于Grid元素对空间解读的特殊性，我们只要在父层元素设定display:grid，接着在需要垂直居中的元素上设置margin:auto即可自动居中。怎么这描述似曾相识。

``` html
<style>
  h2 {
    text-align: center;
  }
  .box {
    width: 500px;
    height: 250px;
    border: 1px solid #f00;
    margin: auto;
    display: grid;
  }
  .content {
    width: 400px;
    background: #ccc;
    margin:auto;
  }
</style>
<h2>18.Grid + margin</h2>
<div class="box box18">
  <div class="content">
    CSS垂直居中
  </div>
</div>
```

# 19、Display：table-cell

适用情景：多行文字的垂直居中技巧

这一招我想有点年纪的开发者应该都有看过，当然像我这么嫩的开发者当然是第一次看到啦，这一招的原理在于使用 CSS display属性将div设置成表格的单元格，这样就能利用支持存储单元格对齐的vertical-align属性来将信息垂直居中

``` html
<style>
  h2 {
    text-align: center;
  }
  .box {
    width: 500px;
    height: 250px;
    border: 1px solid #f00;
    margin: auto;
    text-align: center;
    display: table-cell;
    vertical-align: middle;
  }
  .content {
    width: 400px;
    background: #ccc;
    margin: auto;
  }
</style>
<h2>19.display: table-cell</h2>
<div class="box box19">
  <div class="content">
    CSS垂直居中
  </div>
</div>
```

# 20、calc

适用情景：多行文字的垂直居中技巧

Calc是计算机英文单词calculator的缩写，这个由微软提出的css 方法，真的是网页开发者的一个福音。我们竟然可以在网页中直接做计算，这真是太猛了，从此我们再也不用在那边绞尽脑汁的数学计算了，或是想办法用js来动态计算，我们可以很轻松的利用calc()这个方法，来将百分比及时且动态的计算出实际要的是什么高度，真可谓是划时代的一个方法啊，但这个方法需要注意的是大量使用的话，网页性能会是比较差的，所以请谨慎使用。

``` html
<style>
  h2 {
    text-align: center;
  }
  .box {
    width: 500px;
    height: 250px;
    border: 1px solid #f00;
    margin: auto;
  }
  .content {
    width: 400px;
    background: #ccc;
    position: relative;
    top:calc((100% - 70px) / 2);
    margin:auto;
    height: 70px;
  }
</style>
<h2>20.calc</h2>
<div class="box box20">
  <div class="content">
    CSS垂直居中
  </div>
</div>
```

# 21、Relative + translateY

适用情景：多行文字的垂直居中技巧

这个技巧是利用了top:50%的招式，让你的元素上方能产生固定百分比的距离，接着让要居中的元素本身使用translateY的百分比来达成垂直居中的需求，translate是一个很棒的属性，由于translate的百分比单位是利用元素自身的尺寸作为100%，这样让我们要利用元素自身宽高做事变得方便很多。

``` html
<style>
  h2 {
    text-align: center;
  }
  .box {
    width: 500px;
    height: 250px;
    border: 1px solid #f00;
    margin: auto;
  }
  .content {
    width: 400px;
    background: #ccc;
    position: relative;
    top: 50%;
    transform: translateY(-50%);
    margin: auto;
  }
</style>
<h2>21.relative + translateY(-50%)</h2>
<div class="box box21">
  <div class="content">
    CSS垂直居中
  </div>
</div>
```

# 22、padding

适用情景：多行文字的垂直居中技巧

什么！这也算垂直居中技巧，连我奶奶都知道这方式吧

对的，这的确也算是一种垂直居中的方式，不可讳言的这方式真的是简单过头了，以至于有些开发者认为这种方式都不能算是一种垂直居中的技巧，但同样的你无法反驳的是，我的数据的确垂直居中啦，好啦，就当我硬凹吧，你说的对，好吧

``` html
<style>
  h2 {
    text-align: center;
  }
  .box {
    width: 500px;
    border: 1px solid #f00;
    margin: auto;
    height: auto;
    padding: 50px 0;
  }
  .content {
    width: 400px;
    background: #ccc;
    margin: auto;
  }
</style>
<h2>22.padding</h2>
<div class="box box22">
  <div class="content">
    CSS垂直居中
  </div>
</div>
```

# 23、Write-mode

适用情景：多行文字的垂直剧种技巧

这个方式应该是比较少见到的有人使用的了，这个想法是被老友Paul所激发的，write-mode这个css属性的功能基本上跟垂直居中是八竿子打不着，它的用途是改变文字书写的方向从横变竖，且支持度从很早期的IE5就有支持了，但当时Amos很少使用，一来是网页多是横书较多，另外当时除了IE浏览器意外，其他浏览器的支持度都不是很好，也就很少使用了。

使用write-mode将一整个文字容器变成直书，接着将此容器利用text-align:center来达到垂直居中的目的，白话一点的解说就是，你把原本横排的文字变成竖排，所以原本横排用到的水平对齐方式，就变成了控制直排的中间了，原理就是这么简单。但要特别注意的是浏览器对此语法的支持度来说，需要拆开写法才行，不然某些浏览器的语法不同，可能会让你的网页在某些浏览器上看起来无效，这会是最需要注意到的

``` html
<style>
  h2 {
    text-align: center;
  }
  .box {
    width: 500px;
    height: 250px;
    border: 1px solid #f00;
    margin: auto;
    writing-mode: tb-lr; /* for ie11 */
    writing-mode: vertical-lr;
    text-align: center;
    margin:0 auto;
  }
  .content {
    width: 400px;
    background: #ccc;
    display: inline-block; /* for ie & edge */
    width: 100%;
    writing-mode: lr-tb;
    margin: auto; 
    text-align: left;
  }
  .box .txt {
    width: 80%;
    margin: auto;
  }
</style>
<h2>23.writing-mode</h2>立马来看Amos实际完成的
<div class="box box23">
  <div class="content">
    <div class="txt">
      CSS垂直居中
    </div>
  </div>
</div>
```
