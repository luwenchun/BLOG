---
title: CSS3 动画
date: 2017-09-15 
---

 CSS3 可以实现动画，代替原来的 Flash 和 JavaScript 方案。

 css动画分为CSS Transition和 CSS Animation

 ### CSS Transition

 ### 过渡

语法

			transition： CSS属性，花费时间，效果曲线(默认ease)，延迟时间(默认0)
			/*宽度从原始值到制定值的一个过渡，运动曲线ease,运动时间0.5秒，0.2秒后执行过渡*/
			transition：width,.5s,ease,.2s
			/*所有属性从原始值到制定值的一个过渡，运动曲线ease,运动时间0.5秒*/
			transition：all,.5s
 
 上面栗子是简写模式，也可以分开写各个属性（这个在下面就不再重复了）

			transition-property: width;
			transition-duration: 1s;
			transition-timing-function: linear;
			transition-delay: 2s;

### 动画

动画这个平常用的也很多，主要是做一个预设的动画。和一些页面交互的动画效果，结果和过渡应该一样，让页面不会那么生硬！

语法

			animation：动画名称，一个周期花费时间，运动曲线（默认ease），动画延迟（默认0），播放次数（默认1），是否反向播放动画（默认normal），是否暂停动画（默认running）

			/*执行一次logo2-line动画，运动时间2秒，运动曲线为 linear*/
			animation: logo2-line 2s linear;
			/*2秒后开始执行一次logo2-line动画，运动时间2秒，运动曲线为 linear*/
			animation: logo2-line 2s linear 2s;
			/*无限执行logo2-line动画，每次运动时间2秒，运动曲线为 linear，并且执行反向动画*/
			animation: logo2-line 2s linear alternate infinite;
			animation-fill-mode : none | forwards | backwards | both;
			/*none：不改变默认行为。    
			forwards ：当动画完成后，保持最后一个属性值（在最后一个关键帧中定义）。    
			backwards：在 animation-delay 所指定的一段时间内，在动画显示之前，应用开始属性值（在第一个关键帧中定义）。 
			both：向前和向后填充模式都被应用。  */      

### logo展示动画

![你看](https://user-gold-cdn.xitu.io/2017/11/15/15fbf406b0855a99?imageslim)


			<!DOCTYPE html>
			<html lang="en">
			<head>
				<meta charset="UTF-8">
				<title>Title</title>
				<link rel="stylesheet" href="reset.css">
			</head>
			<style>
			.logo-box{
				width: 600px;
				margin: 100px auto;
				font-size: 0;
				position: relative;
			}
			.logo-box div{
				display: inline-block;
			}
			.logo-box .logo-text{
				margin-left: 10px;
			}
			.logo-box .logo1{
				animation: logo1 1s ease-in 2s;
				animation-fill-mode:backwards;
			}
			.logo-box .logo-text{
				animation: logoText 1s ease-in 3s;
				animation-fill-mode:backwards;
			}
			.logo-box .logo2{
				position: absolute;
				top: 20px;
				left: 20px;
				animation: logo2-middle 2s ease-in;
			}
			.logo-box .logo2 img{
				animation: logo2-line 2s linear;
			}
			@keyframes logo1 {
				0%{
					transform:rotate(180deg);
					opacity: 0;
				}
				100%{
					transform:rotate(0deg);
					opacity: 1;
				}
			}
			@keyframes logoText {
				0%{
					transform:translateX(30px);
					opacity: 0;
				}
				100%{
					transform:translateX(0);
					opacity: 1;
				}
			}
			@keyframes logo2-line {
				0% { transform: translateX(200px)}
				25% { transform: translateX(150px)}
				50% { transform: translateX(100px)}
				75% { transform: translateX(50px)}
				100% { transform: translateX(0); }
			}

			@keyframes logo2-middle {
				0% { transform: translateY(0);     }
				25% { transform: translateY(-100px);     }
				50% { transform: translateY(0);     }
				75% { transform: translateY(-50px);     }
				100% { transform: translateY(0); }
			}
			</style>
			<body>
			<div class="logo-box">
			<div class="logo1"><img src="logo1.jpg"/></div>
			<div class="logo2"><img src="logo2.jpg"/></div>
			<div class="logo-text"><img src="logo3.jpg"/></div>
			</div>

			<div class="wraper"><div class="item"></div></div>

			</body>
			</html>

下面让大家看一个专业级别的
![你看](https://user-gold-cdn.xitu.io/2017/11/15/15fbf406a3af6c0a?imageslim)

			<!DOCTYPE html>
			<html lang="en">
			<head>
				<meta charset="UTF-8">
				<title>Title</title>
			</head>
			<style>
				body {
					font-family: Arial,"Helvetica Neue",Helvetica,sans-serif;
					overflow: hidden;
					background: #fff;
				}

				.center {
					margin: 80px auto;
				}

				.so {
					display: block;
					width: 500px;
					height: 156px;
					background: #ffffff;
				}
				.so .inner {
					width: 500px;
					height: 156px;
					position: absolute;
				}
				.so .inner * {
					position: absolute;
					animation-iteration-count: infinite;
					animation-duration: 3.5s;
				}
				.so .inner .name {
					position: absolute;
					font-size: 54px;
					left: 130px;
					top: 95px;
				}
				.so .inner .name .b {
					font-weight: bold;
				}
				.so .inner .stack-box {
					top: 100px;
					width: 115px;
					height: 56px;
				}
				.so .inner .box {
					width: 115px;
					height: 56px;
					left: 0px;
				}
				.so .inner .box div {
					background: #BCBBBB;
				}
				.so .inner .box .bottom {
					bottom: 0px;
					left: 0px;
					width: 115px;
					height: 12px;
				}
				.so .inner .box .left {
					bottom: 11px;
					left: 0px;
					width: 12px;
					height: 34px;
				}
				.so .inner .box .right {
					bottom: 11px;
					left: 103px;
					width: 12px;
					height: 34px;
				}
				.so .inner .box .top {
					top: 0px;
					left: 0px;
					width: 0;
					height: 12px;
				}
				.so .inner .stack {
					left: 22px;
					top: 22px;
				}
				.so .inner .stack .inner-item {
					background: #F48024;
					width: 71px;
					height: 12px;
				}
				.so .inner .stack .item {
					transition: transform 0.3s;
					width: 291px;
				}
				.so .inner .stack div:nth-child(1) {
					transform: rotate(0deg);
				}
				.so .inner .stack div:nth-child(2) {
					transform: rotate(12deg);
				}
				.so .inner .stack div:nth-child(3) {
					transform: rotate(24deg);
				}
				.so .inner .stack div:nth-child(4) {
					transform: rotate(36deg);
				}
				.so .inner .stack div:nth-child(5) {
					transform: rotate(48deg);
				}
				.so .inner .box {
					animation-name: box;
				}
				.so .inner .box .top {
					animation-name: box-top;
				}
				.so .inner .box .left {
					animation-name: box-left;
				}
				.so .inner .box .right {
					animation-name: box-right;
				}
				.so .inner .box .bottom {
					animation-name: box-bottom;
				}
				.so .inner .stack-box {
					animation-name: stack-box;
				}
				.so .inner .stack {
					animation-name: stack;
				}
				.so .inner .stack .inner-item {
					animation-name: stack-items;
				}
				.so .inner .stack .item:nth-child(1) {
					animation-name: stack-item-1;
				}
				.so .inner .stack .item:nth-child(2) {
					animation-name: stack-item-2;
				}
				.so .inner .stack .item:nth-child(3) {
					animation-name: stack-item-3;
				}
				.so .inner .stack .item:nth-child(4) {
					animation-name: stack-item-4;
				}
				.so .inner .stack .item:nth-child(5) {
					animation-name: stack-item-5;
				}
				@keyframes stack {
					0% {
						left: 22px;
					}
					15% {
						left: 22px;
					}
					30% {
						left: 52px;
					}
					50% {
						left: 52px;
					}
					80% {
						left: 22px;
					}
				}
				@keyframes stack-item-1 {
					0% {
						transform: rotate(12deg * 0);
					}
					10% {
						transform: rotate(0deg);
					}
					50% {
						transform: rotate(0deg);
					}
					54% {
						transform: rotate(0deg);
					}
					92% {
						transform: rotate(12deg * 0);
					}
				}
				@keyframes stack-item-2 {
					0% {
						transform: rotate(12deg * 1);
					}
					10% {
						transform: rotate(0deg);
					}
					50% {
						transform: rotate(0deg);
					}
					54% {
						transform: rotate(0deg);
					}
					92% {
						transform: rotate(12deg * 1);
					}
				}
				@keyframes stack-item-3 {
					0% {
						transform: rotate(12deg * 2);
					}
					10% {
						transform: rotate(0deg);
					}
					50% {
						transform: rotate(0deg);
					}
					54% {
						transform: rotate(0deg);
					}
					92% {
						transform: rotate(12deg * 2);
					}
				}
				@keyframes stack-item-4 {
					0% {
						transform: rotate(12deg * 3);
					}
					10% {
						transform: rotate(0deg);
					}
					50% {
						transform: rotate(0deg);
					}
					54% {
						transform: rotate(0deg);
					}
					92% {
						transform: rotate(12deg * 3);
					}
				}
				@keyframes stack-item-5 {
					0% {
						transform: rotate(12deg * 4);
					}
					10% {
						transform: rotate(0deg);
					}
					50% {
						transform: rotate(0deg);
					}
					54% {
						transform: rotate(0deg);
					}
					92% {
						transform: rotate(12deg * 4);
					}
				}
				@keyframes stack-items {
					0% {
						width: 71px;
					}
					15% {
						width: 71px;
					}
					30% {
						width: 12px;
					}
					50% {
						width: 12px;
					}
					80% {
						width: 71px;
					}
				}
				@keyframes box {
					0% {
						left: 0;
					}
					15% {
						left: 0;
					}
					30% {
						left: 30px;
					}
					50% {
						left: 30px;
					}
					80% {
						left: 0;
					}
				}
				@keyframes box-top {
					0% {
						width: 0;
					}
					6% {
						width: 0;
					}
					15% {
						width: 115px;
					}
					30% {
						width: 56px;
					}
					50% {
						width: 56px;
					}
					59% {
						width: 0;
					}
				}
				@keyframes box-bottom {
					0% {
						width: 115px;
					}
					15% {
						width: 115px;
					}
					30% {
						width: 56px;
					}
					50% {
						width: 56px;
					}
					80% {
						width: 115px;
					}
				}
				@keyframes box-right {
					15% {
						left: 103px;
					}
					30% {
						left: 44px;
					}
					50% {
						left: 44px;
					}
					80% {
						left: 103px;
					}
				}
				@keyframes stack-box {
					0% {
						transform: rotate(0deg);
					}
					30% {
						transform: rotate(0deg);
					}
					40% {
						transform: rotate(135deg);
					}
					50% {
						transform: rotate(135deg);
					}
					83% {
						transform: rotate(360deg);
					}
					100% {
						transform: rotate(360deg);
					}
				}
			</style>
			<body>
			<div class="so center">
				<div class="inner">
					<div class="stack-box">
						<div class="stack">
							<div class="item">
								<div class="inner-item"></div>
							</div>
							<div class="item">
								<div class="inner-item"></div>
							</div>
							<div class="item">
								<div class="inner-item"></div>
							</div>
							<div class="item">
								<div class="inner-item"></div>
							</div>
							<div class="item">
								<div class="inner-item"></div>
							</div>
						</div>
						<div class="box">
							<div class="bottom"></div>
							<div class="left"></div>
							<div class="right"></div>
							<div class="top"></div>
						</div>
					</div>
					<div class="name">
						stack<span class="b">overflow</span>
					</div>
				</div>
			</div>
			</body>
			</html>




 #### 基本用法

在CSS 3引入Transition（过渡）这个概念之前，CSS是没有时间轴的。也就是说，所有的状态变化，都是即时完成。

		img{
		    height:15px;
		    width:15px;
		}

		img:hover{
		    height: 450px;
		    width: 450px;
		}

transition的作用在于，指定状态变化所需要的时间。


		img{
			transition: 1s;
		}

上面代码指定，图片放大的过程需要1秒，效果如下。

我们还可以指定transition适用的属性，比如只适用于height。

		img{
			transition: 1s height;
		}

这样一来，只有height的变化需要1秒实现，其他变化（主要是width）依然瞬间实现，效果如下。

#### transition-delay

在同一行transition语句中，可以分别指定多个属性。

		img{
			transition: 1s height, 1s width;
		}

但是，这样一来，height和width的变化是同时进行的，跟不指定它们没有差别，效果如下。

我们希望，让height先发生变化，等结束以后，再让width发生变化。实现这一点很容易，就是为width指定一个delay参数。


		img{
			transition: 1s height, 1s 1s width;
		}

上面代码指定，width在1秒之后，再开始变化，也就是延迟（delay）1秒，效果如下。

delay的真正意义在于，它指定了动画发生的顺序，使得多个不同的transition可以连在一起，形成复杂效果.

#### transition-timing-function

transition的状态变化速度（又称timing function），默认不是匀速的，而是逐渐放慢，这叫做ease。

		img{
			transition: 1s ease;
		}

除了ease以外，其他模式还包括

		（1）linear：匀速

		（2）ease-in：加速

		（3）ease-out：减速

		（4）cubic-bezier函数：自定义速度模式

最后那个cubic-bezier，可以使用[工具网站](http://cubic-bezier.com/#.67,.21,.83,.67)来定制。


		img{
			transition: 1s height cubic-bezier(.83,.97,.05,1.44);
		}

上面的代码会产生一个最后阶段放大过度、然后回缩的效果。


#### transition的各项属性

transition的完整写法如下。


		img{
			transition: 1s 1s height ease;
		}

这其实是一个简写形式，可以单独定义成各个属性。

		img{
			transition-property: height;
			transition-duration: 1s;
			transition-delay: 1s;
			transition-timing-function: ease;
		}

#### transition的使用注意

（1）目前，各大浏览器（包括IE 10）都已经支持无前缀的transition，所以transition已经可以很安全地不加浏览器前缀。

（2）不是所有的CSS属性都支持transition，完整的列表查看这里，以及具体的效果。

（3）transition需要明确知道，开始状态和结束状态的具体数值，才能计算出中间状态。比如，height从0px变化到100px，transition可以算出中间状态。但是，transition没法算出0px到auto的中间状态，也就是说，如果开始或结束的设置是height: auto，那么就不会产生动画效果。类似的情况还有，display: none到block，background: url(foo.jpg)到url(bar.jpg)等等。

#### transition的局限

transition的优点在于简单易用，但是它有几个很大的局限。

（1）transition需要事件触发，所以没法在网页加载时自动发生。

（2）transition是一次性的，不能重复发生，除非一再触发。

（3）transition只能定义开始状态和结束状态，不能定义中间状态，也就是说只有两个状态。

（4）一条transition规则，只能定义一个属性的变化，不能涉及多个属性。CSS Animation就是为了解决这些问题而提出的。


### CSS Animation

#### 基本用法

首先，CSS Animation需要指定动画一个周期持续的时间，以及动画效果的名称。

		div:hover {
			animation: 1s rainbow;
		}

上面代码表示，当鼠标悬停在div元素上时，会产生名为rainbow的动画效果，持续时间为1秒。为此，我们还需要用keyframes关键字，定义rainbow效果。

		@keyframes rainbow {
			0% { background: #c00; }
			50% { background: orange; }
			100% { background: yellowgreen; }
		}

上面代码表示，rainbow效果一共有三个状态，分别为起始（0%）、中点（50%）和结束（100%）。如果有需要，完全可以插入更多状态。效果如下。

默认情况下，动画只播放一次。加入infinite关键字，可以让动画无限次播放。

		div:hover {
			animation: 1s rainbow infinite;
		}

也可以指定动画具体播放的次数，比如3次。

		div:hover {
			animation: 1s rainbow 3;
		}


#### animation-fill-mode

动画结束以后，会立即从结束状态跳回到起始状态。如果想让动画保持在结束状态，需要使用animation-fill-mode属性。

		div:hover {
			animation: 1s rainbow forwards;
		}

forwards表示让动画停留在结束状态。

animation-fill-mode还可以使用下列值。

		（1）none：默认值，回到动画没开始时的状态。

		（2）backwards：让动画回到第一帧的状态。

		（3）both: 根据animation-direction（见后）轮流应用forwards和backwards规则。

#### animation-direction

动画循环播放时，每次都是从结束状态跳回到起始状态，再开始播放。animation-direction属性，可以改变这种行为。

下面看一个例子，来说明如何使用animation-direction。假定有一个动画是这样定义的。

		@keyframes rainbow {
			0% { background-color: yellow; }
			100% { background: blue; }
		}

默认情况是，animation-direction等于normal。

		div:hover {
			animation: 1s rainbow 3 normal;
		}

此外，还可以等于取alternate、reverse、alternate-reverse等值。它们的含义见下图

简单说，animation-direction指定了动画播放的方向，最常用的值是normal和reverse。浏览器对其他值的支持情况不佳，应该慎用。

#### animation的各项属性

同transition一样，animation也是一个简写形式。

		div:hover {
			animation: 1s 1s rainbow linear 3 forwards normal;
		}

这是一个简写形式，可以分解成各个单独的属性。

		div:hover {
			animation-name: rainbow;
			animation-duration: 1s;
			animation-timing-function: linear;
			animation-delay: 1s;
			animation-fill-mode:forwards;
			animation-direction: normal;
			animation-iteration-count: 3;
		}

**animation-name**对应到动画名称，**animation-duration**是动画时长，还有其他属性：

		1. animation-timing-function：规定动画的速度曲线。默认是ease。
		2. animation-delay：规定动画何时开始。默认是 0。
		3. animation-iteration-count：规定动画被播放的次数。默认是 1。
		4. animation-direction：规定动画是否在下一周期逆向地播放。默认是normal。
		5. animation-play-state ：规定动画是否正在运行或暂停。默认是running。
		6. animation-fill-mode：规定动画执行之前和之后如何给动画的目标应用，默认是none，保留在最后一帧可以用forwards。

#### keyframes的写法

keyframes关键字用来定义动画的各个状态，它的写法相当自由。

		@keyframes rainbow {
			0% { background: #c00 }
			50% { background: orange }
			100% { background: yellowgreen }
		}

0%可以用from代表，100%可以用to代表，因此上面的代码等同于下面的形式。

		@keyframes rainbow {
			from { background: #c00 }
			50% { background: orange }
			to { background: yellowgreen }
		}

如果省略某个状态，浏览器会自动推算中间状态，所以下面都是合法的写法。

		@keyframes rainbow {
			50% { background: orange }
			to { background: yellowgreen }
		}

		@keyframes rainbow {
			to { background: yellowgreen }
		}

甚至，可以把多个状态写在一行。


		@keyframes pound {
			from，to { transform: none; }
			50% { transform: scale(1.2); }
		}

另外一点需要注意的是，浏览器从一个状态向另一个状态过渡，是平滑过渡。steps函数可以实现分步过渡

		div:hover {
			animation: 1s rainbow infinite steps(10);
		}

#### animation-play-state

有时，动画播放过程中，会突然停止。这时，默认行为是跳回到动画的开始状态

上面动画中，如果鼠标移走，色块立刻回到动画开始状态。

如果想让动画保持突然终止时的状态，就要使用animation-play-state属性。

		div {
			animation: spin 1s linear infinite;
			animation-play-state: paused;
		}

		div:hover {
			animation-play-state: running;
		}

上面的代码指定，没有鼠标没有悬停时，动画状态是暂停；一旦悬停，动画状态改为继续播放。效果如下


#### 浏览器前缀

目前，IE 10和Firefox（>= 16）支持没有前缀的animation，而chrome不支持，所以必须使用webkit前缀。

也就是说，实际运用中，代码必须写成下面的样子。

		div:hover {
			-webkit-animation: 1s rainbow;
			animation: 1s rainbow;  
		}

		@-webkit-keyframes rainbow {
			0% { background: #c00; }
			50% { background: orange; }
			100% { background: yellowgreen; }
		}

		@keyframes rainbow {
			0% { background: #c00; }
			50% { background: orange; }
			100% { background: yellowgreen; }
		}


## CSS 的transition和animation有何区别？

首先transition和animation都可以做动效，从语义上来理解，transition是过渡，由一个状态过渡到另一个状态，比如高度100px过渡到200px；而animation是动画，即更专业做动效的，animation有帧的概念，可以设置关键帧keyframe，一个动画可以由多个关键帧多个状态过渡组成，另外animation也包含上面提到的多个属性。

