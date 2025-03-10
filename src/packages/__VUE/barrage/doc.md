# Barrage 弹幕

### 介绍

用于话语和词组的轮播展示，适用于视频中或其他类似需求中。

### 安装

``` javascript
import { createApp } from 'vue';
//vue
import { Barrage } from '@nutui/nutui';
//taro
import { Barrage } from '@nutui/nutui-taro';

const app = createApp();
app.use(Barrage);

```

## 代码演示

### 基础用法1

`Icon` 的 `name` 属性支持传入图标名称或图片链接。

```html
<nut-barrage ref="danmu" :danmu="list"></nut-barrage>
```
``` javascript
  setup() {
    const inputVal = ref<any>('');
    const danmu = ref<any>(null);
    let list = ref(["画美不看", "不明觉厉", "喜大普奔", "男默女泪", "累觉不爱", "爷青结"]); 
    function addDanmu() {
      danmu.value.add(inputVal.value);
    }
    return {
      inputVal,
      danmu,
      list,
      addDanmu
    };
  }
```



## API

### Props

| 参数         | 说明                             | 类型   | 默认值           |
|--------------|----------------------------------|--------|------------------|
| danmu         | 弹幕列表数据               | Array | []              |
| frequency        | 可视区域内每个弹幕出现的时间间隔                         | Number | 200               |
| speeds         | 每个弹幕的滚动时间 | Number |  2000               |
| rows  | 弹幕行数，分几行展示     | Number | 1 |
| top  | 弹幕垂直距离    | Number | 10 |
| loop  | 是否循环播放     | Boolean | true |

### Events

| 事件名 | 说明           | 回调参数     |
|--------|----------------|--------------|
| add  | 添加数据 | - |
