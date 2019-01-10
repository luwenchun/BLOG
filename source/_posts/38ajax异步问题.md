---
title: ajaix异步问题
date: 2017-11-16
---



### Promise 封装ajax想顺序执行ajax

            function $myAjax(url, method, data, callback) {
            let p = new Promise(function(resolve, reject) {
                $Ajax.request({
                    url: url,
                    method: method,
                    data: data,
                    success: function(resp) {
                        callback(resp);
                        resolve();
                    },
                    failure: function(xhr) {
                        //todo
                        reject();
                    }
                });
            });
            return p;
            }
            let $docs = document;
            $docs.getElementById('xxx').onclick = function() {
            $myAjax('https://mhd.uzai.com/api/CommonActive/GetPrizeGivingByUserid', 'get', { 'memberid': 1920740, 'activeid': 1 }, function(resp) {
                console.log(resp);
                console.log(1);
            }).then(function() {
                return
                $myAjax('https://mhd.uzai.com/api/CommonActive/GetPrizeGivingByUserid', 'get', { 'memberid': 1920740, 'activeid': 1 }, function(resp) {
                console.log(resp);
                console.log(2);
            })
            }).then(function() {
                 return
                $myAjax('https://mhd.uzai.com/api/CommonActive/GetPrizeGivingByUserid', 'get', { 'memberid': 1920740, 'activeid': 1 }, function(resp) {
                console.log(resp);
                console.log(3);
            })
            });
            };


    打出的顺序经常是1，3，2;由于每个回调函数必须return，后面两个then是基于第一个promise的异步，所以不是顺序执行的

    













最直观的办法是用异步嵌套，但代码看起来很蠢。比较好的解决办法是es6的async await





### es6的async await

$docs.getElementById('xxx').onclick = async function() {
  let resp1 = await $myAjax('https://mhd.uzai.com/api/CommonActive/GetPrizeGivingByUserid')
  let resp2 = await $myAjax('https://mhd.uzai.com/api/CommonActive/GetPrizeGivingByUserid')
  }

