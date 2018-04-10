<template>
  <div id="app">
    <div class="center">
      <div>
        <h2>Citypicker</h2>
        <img src="./assets/images/logo.png" class="logo" alt="">
      </div>
      <div class="list-cont">
        <h3>一级城市</h3>
        <div class="list-main">
          <city-picker :level="1" @choice-caller="choiceCallers"></city-picker>
        </div>
        <p>设置<strong>:level</strong>属性来定义城市的级数</p>
        <p>调用<strong>@choice-caller</strong>回调，作用是选择选项后触发的事件</p>
      </div>
      <div class="list-cont">
        <h3>二级城市</h3>
        <div class="list-main">
          <city-picker :default-city="defaultCity" :level="2"></city-picker>
        </div>
        <p>设置<strong>:default-city</strong>属性来定义默认城市</p>
        <p>设置<strong>:level</strong>属性来定义城市的级数</p>
      </div>
      <div class="list-cont">
        <h3>三级城市</h3>
        <div class="list-main">
          <city-picker :default-city="defaultCity" @visible-change="visibleChange"></city-picker>
        </div>
        <p class="careful">注意：如果没有设置<strong>:level</strong>属性，默认是三级城市</p>
        <p>设置<strong>:default-city</strong>属性来定义默认城市</p>
        <p>调用<strong>@visible-change</strong>回调，作用是显示/隐藏下拉框触发的事件</p>
      </div>
      <div class="list-cont">
        <h3>禁止选择城市</h3>
        <div class="list-main">
          <city-picker :default-city="defaultCity" :short-name="true" :disabled="true"></city-picker>
        </div>
        <p>设置<strong>:default-city</strong>属性来定义默认城市</p>
        <p>设置<strong>:short-name</strong>属性来显示城市名称、全称/简写</p>
        <p>设置<strong>:disabled</strong>属性禁止城市选择</p>
      </div>
      <div class="table">
        <h3>Citypicker Attributes</h3>
        <table>
          <tr>
            <th>参数</th>
            <th>说明</th>
            <th>类型</th>
            <th>默认值</th>
          </tr>
          <tr v-for="(item, index) in tableData.length" :key="index">
            <td>{{tableData[index].parameter}}</td>
            <td>{{tableData[index].explain}}</td>
            <td>{{tableData[index].type}}</td>
            <td>{{tableData[index].defaults}}</td>
          </tr>
        </table>
      </div>
      <div class="table">
        <h3>Citypicker Events</h3>
        <table>
          <tr>
            <th>事件名称</th>
            <th>说明</th>
            <th>回调参数</th>
          </tr>
          <tr v-for="(item, index) in eventData.length" :key="index">
            <td>{{eventData[index].parameter}}</td>
            <td>{{eventData[index].explain}}</td>
            <td>{{eventData[index].opt}}</td>
          </tr>
        </table>
      </div>
    </div>
  </div>
</template>

<script>
import cityPicker from './components/cityPicker'

export default {
  name: 'App',
  data () {
    return {
      defaultCity: '北京市, 北京市, 东城区',
      storage: true,
      tableData: [
        {
          parameter: 'city-data',
          explain: '城市数据源',
          type: 'Array',
          defaults: '-'
        }, {
          parameter: 'default-city',
          explain: '默认城市设置',
          type: 'String',
          defaults: '-'
        }, {
          parameter: 'selectpattern',
          explain: '配置城市字段名及默认提示文字',
          type: 'Array',
          defaults: "[{field: 'userProvinceId', placeholder: '请选择省份'}, {field: 'userCityId', placeholder: '请选择城市'}, {field: 'userDistrictId', placeholder: '请选择区县'}]"
        }, {
          parameter: 'short-name',
          explain: '显示城市名称。简写/全称',
          type: 'Boolean',
          defaults: 'false'
        }, {
          parameter: 'storage',
          explain: '存储选中值的类型，Name/Id',
          type: 'Boolean',
          defaults: 'true'
        }, {
          parameter: 'level',
          explain: '显示城市的级数',
          type: 'Number',
          defaults: '3'
        }, {
          parameter: 'disabled',
          explain: '禁用',
          type: 'Boolean',
          defaults: 'false'
        }
      ],
      eventData: [
        {
          parameter: 'choice-caller',
          explain: '选择选项触发的事件回调',
          opt: 'values：[name, id]'
        }, {
          parameter: 'visible-change',
          explain: '城市下拉框出现/隐藏时触发',
          opt: 'visible：出现则为 false，隐藏则为 true'
        }
      ]
    }
  },
  components: {
    cityPicker
  },
  methods: {
    visibleChange (visible) {
      console.log(visible ? '隐藏下拉框' : '显示下拉框')
    },
    choiceCallers (values) {
      console.log('选择的城市：'+ values)
    }
  }
}
</script>

<style lang="scss">
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 3%;
}
.logo {
  width: 50px;
}
.center {
  display: inline-block;
  max-width: 1150px;
}
.list-cont {
  border: 1px solid #ebeef5;
  border-radius: 4px;
  box-shadow: 0 2px 12px 0 rgba(0,0,0,.1);
  width: 500px;
  display: inline-block;
  vertical-align: top;
  margin: 3%;
  .list-main {
    border-top: 1px solid #ebeef5;
    border-bottom: 1px solid #ebeef5;
    padding: 20px 0;
  }
  p {
    font-size: 14px;
    color: #5e6d82;
    text-align: left;
    padding: 0 3%;
    strong {
      color: #5e6d82;
      background-color: #e6effb;
      margin: 0 4px;
      display: inline-block;
      padding: 1px 5px;
      font-size: 12px;
      border-radius: 3px;
      height: 18px;
      line-height: 18px;
    }
    &.careful {
      color: #ac3797;
      strong {
        background-color: #f9dff8;
        color: #ac3797;
      }
    }
  }
}
.table {
  margin-bottom: 3%;
  h3 {
    text-align: left;
  }
  table {
    width: 100%;
  }
  th,
  td {
    border-bottom: 1px solid #ebeef5;
    padding: 15px;
    text-align: left;
    max-width: 250px;
  }
  th {
    color: #666;
  }
  td {
    color: #333;
  }
}
</style>
