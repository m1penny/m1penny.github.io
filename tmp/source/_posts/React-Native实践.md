---
title: React_Native实践
date: 2016-06-07 14:45:41
tags:
---
React Native 结合了 Web 应用和 Native 应用的优势，可以使用 JavaScript 来开发 iOS 和 Android 原生应用。

1.安装

（1）安装Node
```ios
brew install node
```
（2）使用npm安装React Native CLI工具
```ios
npm install -g react-native-cli
```
若不行（被墙），需要用到淘宝镜像
```ios
npm install -g cnpm --registry=https://registry.npm.taobao.org
cnpm install -g react-native-cli
```
（3）构建项目
```ios
react-native init '项目名'
```
接着就是到项目文件夹运行ios下的xcodeproj，运行是欢迎页~

<!-- more -->

2.本次实践

前提，导入
```ios
import React, { Component } from 'react';
import {
  AppRegistry,
  StyleSheet,
  Text,
  View,
  NavigatorIOS,
  TouchableHighlight,
  ListView,
  Image,
  ScrollView,
} from 'react-native';
```
我们需要用到的都需先导入，如：NavigatorIOS,Image,ListView等。

（1）组成部分

![](http://o7awmhryr.bkt.clouddn.com/2016-06-07%2015_43_46.gif)

导航控制器、列表页、详情页

导航控制器：
```ios
var MainClass = React.createClass({

  render: function() {
    //component这里设置的是这个组件启动的时候显示的第一个子组件
    return (
      <NavigatorIOS ref='nav'
                    style= {styles.container}
                    initialRoute= {{
                      component: HomeScene,
                      title: 'home',
                      // rightButtonTitle: '',
                      // onRightButtonPress: this.onRightButtonPress,
                    }}>

      </NavigatorIOS>
    );
  },
});
```
导航作为主页，要指定initialRoute的component，这边指定为HomeScene。
注意：要将导航设置为启动组件，如下：
```ios
AppRegistry.registerComponent('HelloWorld', () => MainClass);
```

列表页：
```ios
/*--  首页页面组件 --*/
var HomeScene = React.createClass({

  getInitialState: function() {
    return {
      dataSource: new ListView.DataSource({
        rowHasChanged: (row1, row2) => row1 !== row2,
      }),
      loaded: false,
    };
  },
  componentDidMount: function() {
    this.fetchData();
  },
  fetchData: function() {
    fetch(REQUEST_URL)
      .then((response) => response.json())
      .then((responseData) => {
        this.setState({
          dataSource: this.state.dataSource.cloneWithRows(responseData.movies),
          loaded: true,
        });
      })
      .done();
  },
  renderLoadingView: function() {
    return (
      <View style={styles.container}>
        <Text>
          Loading movies...
        </Text>
      </View>
    );
  },
  selectMovie: function(movie: Object) {
      this.props.navigator.push({
        title: movie.title,
        component: MovieScreen,
        passProps: {movie},
      });
  },
  renderRow: function(movie: Object
    ) {
    var criticsScore = movie.ratings.critics_score;
    var TouchableElement = TouchableHighlight;

    return (
      <View>
        <TouchableElement
          onPress={() => this.selectMovie(movie)}
          onShowUnderlay={movie.onHighlight}
          onHideUnderlay={movie.onUnhighlight}>
          <View style={styles.row}>
            <Image
              source={getImageSource(movie, 'det')}
              style={styles.cellImage}
            />
            <View style={styles.textContainer}>
              <Text style={styles.movieTitle} numberOfLines={2}>
                {movie.title}
              </Text>
              <Text style={styles.movieYear} numberOfLines={1}>
                {movie.year}
                {' '}&bull;{' '}
                <Text style={getStyleFromScore(criticsScore)}>
                  Critics {getTextFromScore(criticsScore)}
                </Text>
              </Text>
            </View>
          </View>
        </TouchableElement>
      </View>
    );
  },
  render: function() {

    if (!this.state.loaded) {
      return this.renderLoadingView();
    }

    return (
      <ListView
        dataSource={this.state.dataSource}
        renderRow={this.renderRow}
        style={styles.listView}
      />
    );
  },
  onPress: function() {
    this.props.navigator.push({
      title: 'touch View',
      component: SecondScene,
      passProps: { myProp: 'Axiba001' },
    });
  }
});
```
获取数据->dataSource->填充listView，并加入了每一行的点击事件，将跳转到MovieScreen组件，传递数据到该组件。
注意点：renderRow: function(movie: Object)方法不能少，用于展示每一行数据，如同原生cell。

详情页：
```ios
var MovieScreen = React.createClass({
  render: function() {
    return (
      <ScrollView contentContainerStyle={styles.contentContainer}>
        <View style={styles.mainSection}>
          <Image
            source={getImageSource(this.props.movie, 'det')}
            style={styles.detailsImage}
          />
          <View style={styles.rightPane}>
            <Text style={styles.movieTitle}>{this.props.movie.title}</Text>
            <Text>{this.props.movie.year}</Text>
            <View style={styles.mpaaWrapper}>
              <Text style={styles.mpaaText}>
                {this.props.movie.mpaa_rating}
              </Text>
            </View>
            <Ratings ratings={this.props.movie.ratings} />
          </View>
        </View>
        <View style={styles.separator} />
        <Text>
          {this.props.movie.synopsis}
        </Text>
        <View style={styles.separator} />
        <Cast actors={this.props.movie.abridged_cast} />
      </ScrollView>
    );
  },
});
```
简单的数据展示。

就这么实现一个简单的导航，列表，详情，样式代码没有贴出，可下载查看。
不足：都挤在同一个js中，可分离开。
Demo地址：<https://github.com/m1penny/React_Native_Test/>
