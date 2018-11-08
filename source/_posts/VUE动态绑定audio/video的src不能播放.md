---
title: VUE动态绑定audio/video的src不能播放
date: 2018-08-08
---

前几天写个项目，要求要本地上传音视频至服务器，再回显可播放。心想这简单啊，直接vue双向绑定不就轻松解决，没想到遇到个大坑~

            <audio controls>
            <source :src="audio_url">
            您的浏览器不支持 audio 元素。
            </audio>
            this.audio_url = res.data.audio_url

结果音频并不能播放，于是就找度娘，很多说赋值完成后要重新用js控制播放器播放，像这样。。

            let dom = document.getElementById('audio');
            dom.play();

亲自尝试，并没有效果

最终想到了VUE的$refs特性，不了解的同学自己去VUE看文档

给audio绑定个ref值

            <audio ref='audio' controls>
                您的浏览器不支持 audio 元素。
            </audio>

在需要动态绑定的方法里用$refs动态设置src

            this.$refs.audio.src = res.data.audio_url

