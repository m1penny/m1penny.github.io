---
title: JSPatch简单使用
date: 2016-06-03 14:40:21
tags:
---
JSPatch，只需在项目中引入极小的引擎，就可以使用JavaScript调用任何Objective-C的原生接口，获得脚本语言的能力：动态更新APP，替换项目原生代码修复bug。

1.
```ios
pod 'JSPatch'
```
2.本地测试用：
```ios
[JPEngine startEngine];
NSString *sourcePath = [[NSBundle mainBundle] pathForResource:@"sample" ofType:@"js"];
NSString *script = [NSString stringWithContentsOfFile:sourcePath encoding:NSUTF8StringEncoding error:nil];
[JPEngine evaluateScript:script];
```
<!-- more -->
网络获取用：
```ios
[JPEngine startEngine];
[NSURLConnection sendAsynchronousRequest:[NSURLRequest requestWithURL:[NSURL URLWithString:@"http://cnbang.net/test.js"]] queue:[NSOperationQueue mainQueue] completionHandler:^(NSURLResponse *response, NSData *data, NSError *connectionError) {
    NSString *script = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
    [JPEngine evaluateScript:script];
 }];

```
3.sample.js
```ios
require('UIColor,UILabel');
defineClass('ViewController', {
            viewDidLoad: function() {
            self.super().viewDidLoad();
            self.MidView().setBackgroundColor(UIColor.blueColor());
            var label = UILabel.alloc().initWithFrame({x:20, y: 20, width: 100, height: 100});
            label.setText("代文乐");
            self.view().addSubview(label);
            self.method()
            // Do any additional setup after loading the view, typically from a nib.
            },
            method: function() {
//            NSLog("test");
            console.log("methodTest")
//            self.MidView().setBackgroundColor(UIColor.blackColor());
            },
            });
console.log("test")
```
4.使用工具

都由bang大神制作的

一、JSPatchX
在Alcatraz下搜索下载

二、JSPatchConvertor
将OC转为JS的实用工具，地址：<http://bang590.github.io/JSPatchConvertor/>
并不是百分百准确，但仍旧很牛逼
源代码地址：<https://github.com/bang590/JSPatch>

![](http://o7awmhryr.bkt.clouddn.com/0FAB6C46-AEE4-4216-BD61-DB5B9848E379.png)