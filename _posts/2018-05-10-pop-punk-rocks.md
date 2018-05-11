---
layout: post
title: Ai-笔记：开发pop-punk.rocks
---

<div class="message">
为了准备即将去参加的Galaxy Camp，第一个pop punk音乐节，我开发了一个简单的歌词网站<a href="www.pop-punk.rocks">www.pop-punk.rocks</a>，这是开发过程中遇到和解决的困难。

</div>

#### 0. 一个主机，多个域名

刚开始的解决方案很天真，在index.php里写了一个switch，让不同的域名跳转到不同的文件夹。
index.php
{% highlight php %}
<?php
switch ($_SERVER["HTTP_HOST"])
{
case "www.pop-punk.rocks":
case "pop-punk.rocks":
header("location:poppunkrocks/");
break;
case "www.wolfiethedog.de":
case "wolfiethedog.de":
header("location:wolfiethedog/");
break;
case "www.notagenius.cn":
header("location:notagenius/");
break;
}
?>
{% endhighlight %}

不过，登录就发现问题了，因为跳转文件夹，就变成二级域名了。跳转过后变成`www.pop-punk.rocks/poppunkrocks/`，这不是我想要的，得知Apache下可以用vitual hosts在路径`/etc/httpd/conf`下修改httpd.conf
{% highlight bash %}
Listen 80

<VirtualHost *:80>
    DocumentRoot "/var/www/html/poppunkrocks/"
    ServerName www.pop-punk.rocks

    # Other directives here
</VirtualHost>

<VirtualHost *:80>
    DocumentRoot "/var/www/html/wolfiethedog/"
    ServerName wolfiethedog.de

    # Other directives here
</VirtualHost>
{% endhighlight %}

问题解决了。

所以，一个主机想用安排多个域名，用虚拟主机。

#### 1. Reveal.js嵌入视效
说现在的艺术性前端，一言以概之，Usefulness不足，Pointlessness不少，可是观赏性就是爆表。问我喜不喜欢，我会说非常非常喜欢，因为就是创造力在canvas上的展现，还带这么点Coding的艺术，而且我相信它的功能性会丰富起来的。
我想要做一个艺术友好的页面。p5.js, three.js, d3.js都可以。哪个方便用哪个。
用d3.js是因为已经有现成的reveal.js的干净的plugin，three.js找到一个demo，但是代码不清晰。

![placeholder](/image/2018-05-10-black-bg.png "black.png")
![placeholder](/image/2018-05-10-white-bg.png "white.png")

d3_js.html
{% highlight html %}
<body>
<script src="//d3js.org/d3.v3.min.js"></script>
<script>

var width = Math.max(innerWidth),
    height = Math.max(innerHeight);

var x1 = width,
    y1 = -height/3,
    x0 = 0,
    y0 = height + height/3,
    i = 0,
    r = Math.max(400,Math.max(width,height)+height/3),
    τ = 2 * Math.PI;

var canvas = d3.select("body").append("canvas")
    .attr("width", width)
    .attr("height", height)
    .on("ontouchstart" in document ? "touchmove" : "mousemove", move);

var context = canvas.node().getContext("2d");
context.globalCompositeOperation = "lighter";
context.lineWidth = 0.7;

d3.timer(function() {
  context.clearRect(0, 0, width, height);

  var z = d3.hsl(++i % 180-180, 1, 0.5).rgb(),
      c = "rgba(" + z.r + "," + z.g + "," + z.b + ",",
      x = x0 += (x1 - x0) * .1,
      y = y0 += (y1 - y0) * .1;

  d3.select({}).transition()
      .duration(30000)
      .ease(Math.sqrt)
      .tween("circle", function() {
        return function(t) {
          context.strokeStyle = c + (1 - t) + ")";
          context.beginPath();
          context.arc(x, y, r*t, 0, τ);
          context.stroke();
        };
      });
});
function move() {
}

</script>

</body>
{% endhighlight %}

主页最终效果：
![placeholder](/image/2018-05-10-black-text-bg.png "black-text.png")


#### 2. Vue.js遍历Json

因为这次歌词本比以往多很多，一共有8个乐队，将近90首歌，手动写html是最糟糕的主意，设计json条目，发现我需要band.title, song.title, youtube.link, song.lyrics
因为json还是要手写，本来以为genius api可以给我返回歌词，但是后来发现并没有，歌词是有版权保护的。所以，我们给每个乐队安排了一个json。手动写json。
vue-for在html里直接可以用，非常好用。
所以保持了页面body很清晰。因为body主体就是一个json文件的2次遍历。

{% highlight html %}
<section data-transition="convex" data-background="#2B2B2B" id="statechamps">
	<section class="scrollable">
	<h2>State Champs</h2>
		<template v-for="(item,index) in items">
		<a v-bind:href="'#/1/'+ ++index">
		<h3 style="color:orange">{{item.song}}</h3>
		</a>
		</template>
	</section>
		<template v-for="item in item1s">
		<section class="scrollable" data-scrolling>
			<iframe :data-src="item.Youtube"></iframe>
			<h2 style="color:orange">{{item.song}}</h2>
			<p v-html="item.lyrics"></p>
		</section>
		</template>
</section>
{% endhighlight %}

vue加载本地json出了点问题，简单的import怎么都失败，最后的解决方法是模拟成网络请求，我还是被这些异步加载块给弄糊涂了。

{% highlight javascript %}
<script>
	(async () => {
	const statechampsResponse = await fetch('./json/statechamps.json');
	const statechamps_json = await statechampsResponse.json();
	new Vue({
		el: '#statechamps',
		data() {
		return {
				items: statechamps_json
				}
				}
		})
	})();
</script>
{% endhighlight %}

#### 3. Reveal.js变竖屏和滚动条和Menu

因为在做不是reveal.js设计出来要做的事情，所以需要改！
首先，歌词一定超过屏幕高度，我需要滚动条，
customized.css

{% highlight css %}
.scrollable {
overflow-y: auto !important;
overflow-x: hidden !important;
height: 1248px !important;
}
{% endhighlight %}

另外变竖排和为了在用户触摸屏幕的时候触发滚动条而不是走向不同的页面，我还需要禁用touch

{% highlight javascript %}
Reveal.configure({ touch: false });
Reveal.configure({ width: 910, height: 1248 });
{% endhighlight %}

910, 1248是这个主流手机屏幕比例下的，因为导航键的存在，不可以全屏。在iphone X这种高度下，是会[浪费](/image/2018-05-10-iphoneX.png "space waster")的。


Menu是现成的Plugin，因为很多hardcoded的地方，所以就进源码改了，用阿狗icon的想法也没有落实，最后页面内icon还是fontawesome里的。

最后menu的效果：
![placeholder](/image/2018-05-10-menu.png "menu.png")


#### 4. 设计

color block式幼稚的设计

![placeholder](/image/2018-05-10-color-1.png "color-1.png")
![placeholder](/image/2018-05-10-color-2.png "color-2.png")
![placeholder](/image/2018-05-10-color-3.png "color-3.png")
![placeholder](/image/2018-05-10-color-4.png "color-4.png")

最后的效果：
![placeholder](/image/2018-05-10-green.png "green.png")
![placeholder](/image/2018-05-10-red.png "red.png")
![placeholder](/image/2018-05-10-grey.png "grey.png")
![placeholder](/image/2018-05-10-pink.png "pink.png")

#### 5. 问题和结语

没有解决的问题是在firefox下转页时的背景颜色会丢失，firefox在2页之后，就无法改变背景颜色了，当内容是inline的时，没有问题，当内容是在加载json后，firefox就出问题。测试了firefox 59 和 60, 都失败，而safari，chrome却没有这个问题。

解决方案是，我写了一条字，“如果没有颜色，看不清歌词，请去themeless的子页面”的白痴方案。

产品体验是，我本来计划是进一个页面，就开始播歌，在PC上这个实现了，但是在手机上，因为有流量问题，我本还担心是不是要改一下设计，后来发现，即使autoplay设置成true，手机方面还是会拦截自动播放，就没有去处理它。

自我评价的话，这还是一个拼凑现成工具的网站，并没有很多原创性。
但是功能性上来说，听歌和背歌词都很方便。

「完」
