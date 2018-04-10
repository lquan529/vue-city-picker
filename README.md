# Citypicker

> 基于vue开发的城市模拟下拉组件

## Build Setup

``` bash
# 下载依赖包
npm install

# 运行开发环境
npm run dev

# 打包生成
npm run build
```

## Attributes

| 参数 | 说明 | 类型 | 默认值 |
|---|---|---|---|
| city-data | 城市数据源 | Array | - |
| default-city | 默认城市设置 | String | - |
| selectpattern | 配置城市字段名及默认提示文字 | Array |[{field: 'userProvinceId', placeholder: '请选择省份'}, {field: 'userCityId', placeholder: '请选择城市'}, {field: 'userDistrictId', placeholder: '请选择区县'}] |
| short-name | 显示城市名称。简写/全称 | Boolean | false |
| storage | 存储选中值的类型，Name/Id | Boolean | true |
| level | 显示城市的级数 | Number | 3 |
| disabled | 禁用 | Boolean | false |

## Events

| 事件名称 | 说明 | 回调参数 |
|---|---|---|
| choice-caller | 选择选项触发的事件回调 | values：[name, id] |
| visible-change | 城市下拉框出现/隐藏时触发 | visible：出现则为 false，隐藏则为 true |

## Invoking

``` html
定义默认城市，二级城市

<city-picker :default-city="北京市, 北京市, 东城区" :level="2"></city-picker>

开启简写城市，三级城市

<city-picker :short-name="true"></city-picker>

禁止城市，三级城市

<city-picker :disabled="true"></city-picker>
```

## Demo

[点击预览](http://vuejs-templates.github.io/webpack/)
