---
layout: post
title: 多列选择器：mode = multiSelector
category: PHP
keywords: 
tags: 小程序 PHP
description: 
---

#### 多列选择器：mode = multiSelector
PHP后台    
```
$regionList = SpaceRegion::field('id,region_name')->select();
foreach ($regionList as $k=>&$value) {
	$spaceList[$k]=ParkingModel::where(['region_id'=>$value['id']])->column('parkno');
}       
```
wxml
```
<view class="section_title">多列选择器</view>
<picker mode="multiSelector" bindchange="bindMultiPickerChange" bindcolumnchange="bindMultiPickerColumnChange" 
value="{{multiIndex}}" range="{{multiArray}}">
<view class="picker">
  当前选择：{{multiArray[0][multiIndex[0]]}}，{{multiArray[1][multiIndex[1]]}}
</view>
</picker>
```
js
```
const App = getApp();
Page({
  data: {
    multidata: [],
    multiArray: [],
    multiIndex: [0, 0],
  },
  onLoad: function () {
    var _this = this;
    let token = wx.getStorageSync('token');
    let regionlist = [];
    let spacelist = [];
    App._post_form('user/getprofile', {
      token: token,
    }, function (result) {
      for (var i in result.data.regionList) {
        regionlist.push(result.data.regionList[i].region_name);
      }
      _this.setData({
        multidata: result.data,
        multiArray: [regionlist, result.data.spaceList[0]]
      });
    }, function () {
      wx.hideLoading();
    });
  },
  bindMultiPickerChange: function (e) {
    console.log('picker发送选择改变，携带值为', e.detail.value)
    this.setData({
      multiIndex: e.detail.value
    })
  },
  bindMultiPickerColumnChange: function (e) {
    let _this=this;
    let regionList =_this.data.multidata;
    console.log('修改的列为', e.detail.column, '，值为', e.detail.value);
    var data = {
      multiArray: _this.data.multiArray,
      multiIndex: _this.data.multiIndex
    };
    data.multiIndex[e.detail.column] = e.detail.value;
    switch (e.detail.column) {
      case 0: 
        data.multiArray[1] = _this.data.multidata['spaceList'][e.detail.value];
    }
    _this.setData(data);
  },
})
```