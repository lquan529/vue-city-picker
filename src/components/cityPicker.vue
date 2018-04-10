<template>
	<div class="city-picker">
		<div v-for="(item, index) in level" :key="index" :class="['city-picker-item', { hide: inlay[index].isHide }]" @keydown.stop="onKeyDown(index, $event)" @keyup.enter.stop="onEnter(index)">
			<input type="text" :placeholder="selectpattern[index].placeholder" :value="inlay[index].values[0]" readonly unselectable="on" :disabled="inlay[index].isDisabled" @click.stop="toggleList(index)">
			<input type="hidden" :name="[selectpattern[index].field]" :value="storageVal(index)">
			<ul class="city-picker-list">
				<li v-for="(city, cindex) in inlay[index].filterDatas" :key="cindex" :data-index="cindex" :data-id="city.id" :class="['caller', {active: city.isActive}]" @click.stop="choice(city, index)">
						{{ shortName ?  city.shortName : city.name }}
				</li>
			</ul>
		</div>
	</div>
</template>

<script>
import cityData from "../assets/cityData";

export default {
  name: "cityPicker",
  data () {
    return {
      inlay: [
        {
          isHide: true,
          isDisabled: false,
          filterDatas: [],
          values: []
        }, {
          isHide: true,
          isDisabled: true,
          filterDatas: [],
          values: []
        }, {
          isHide: true,
          isDisabled: true,
          filterDatas: [],
          values: []
        }
      ],
      removalCityArray: [],
      itemIndex: 0
    }
  },
  props: {
    cityData: {
      type: Array,
      default () {
        return cityData
      }
    },
    selectpattern: {
      type: Array,
      default () {
        return [
          {
            field: 'userProvinceId',
            placeholder: '请选择省份'
          }, {
            field: 'userCityId',
            placeholder: '请选择城市'
          }, {
            field: 'userDistrictId',
            placeholder: '请选择区县'
          }
        ]
      }
    },
    defaultCity: {
			type: String
		},
    shortName: {
      type: Boolean,
      default: false
    },
    storage: {
      type: Boolean,
      default: true
    },
    disabled: {
      type: Boolean,
      default: false
    },
    level: {
      type: Number,
      default: 3
    }
  },
  computed: {
    newCityData () {
      return JSON.parse(JSON.stringify(this.cityData));
    }
  },
  created () {
    const that = this;

    // 取到省份的数据
    that.filterData('100000', 0);
    // 有默认城市设置默认，没有默认城市且开启ip定位就进行ip定位获取城市位置
    if (that.defaultCity) {
      that.setCity();
    }
    // 根据父是否开启禁止，开启就执行
    if (that.disabled) {
      that.modifyNature(that.inlay, 'isDisabled', true);
    }
    // 点击document判断是否要隐藏全部列表
    document.addEventListener('click', function (e) {
      if (e.target.className !== 'city-picker') {
        that.modifyNature(that.inlay, 'isHide', true);
      }
    });
  },
  methods: {
    toggleList (index) {
      // 设置列表显示/隐藏
      this.$set(this.inlay[index], 'isHide', !this.inlay[index].isHide);
      // 下拉框显示/隐藏触发的回调
      this.$emit('visible-change', this.inlay[index].isHide);
    },
    get (url, data = {}) {
      return new Promise((resolve, reject) => {
          this.$http.get(url, data)
              .then(res => {
                resolve(res.data);
              })
              .catch(err => {
                reject(err);
              });
      });
    },
    filterData (id, index) {
      // 如果当前索引大于级数，就不往下执行
      if (index > this.level - 1) { return false; }
      // 遍历出对应级的城市数据
      for (let list of this.newCityData) {
        if (list.parentId === id) {
          // 设置选中的判断属性
          list['isActive'] = false;
          // 存储每个级的城市数据
          this.inlay[index].filterDatas.push(list);
        }
      }
    },
    setCity (city) {
      // 分隔字符串成数组
      const citys = city || this.defaultCity;
      const filterCityArray = citys.split(/\,\s|\,/g);

      // 找到对应的城市数据
      for (let list of filterCityArray) {
        for (let clist of this.newCityData) {
          // 根据传进来的是Name还是Id
          const isNumber = isNaN(list) ? clist.name.indexOf(list) > -1 : list === clist.id;
          if (isNumber) {
            this.removalCityArray.push(clist);
          }
        }
      }
      // 去重数据
      this.removalCityArray = [...new Set(this.removalCityArray)];
      // 设置默认城市
      this.removalCityArray.map((key, index) => {
        this.choice(key, index);
      });
    },
    clearOriginal (index) {
      // 清空历史数据
      for (let i = index; i < this.level; i++) {
        const inlay = this.inlay[i];
        // 清空城市数据
        inlay.filterDatas = [];
        // 清空选择的值
        inlay.values = [];
        // 添加禁止状态
        inlay.isDisabled = true;
      }
    },
    modifyNature (data, name, keys) {
      // 修改状态
      for (let i = 0; i < data.length; i ++) {
        this.$set(data[i], name, keys);
      }
    },
    storageVal (index) {
      // 存储的是城市ID还是城市名称
      return this.storage ? this.inlay[index].values[1] : this.inlay[index].values[0];
    },
    choice (city, index) {
      const inlay = this.inlay[index];
      // 显示的城市名称是全称还是简写
      const name = this.shortName ? city.shortName : city.name;
      // 下一级的索引
      const nextIndex = index + 1;

      // 还原选择的状态为没有选择
      this.modifyNature(inlay.filterDatas, 'isActive', false);
      // 清空下一级上次选择的数据
      this.clearOriginal(nextIndex);
      // 加载下一级城市的数据
      this.filterData(city.id, nextIndex);
      // 解锁下一级可点击状态
      nextIndex < this.level ? this.$set(this.inlay[nextIndex], 'isDisabled', false) : '';
      // 添加选中状态
      this.$set(city, 'isActive', true);
      // 存储选择的名称和ID
      this.$set(inlay, 'values', [name, city.id]);
      // 选择的回调函数
      this.$emit('choice-caller', [name, city.id]);
      // 隐藏选择级的列表
      this.modifyNature(this.inlay, 'isHide', true);
    },
    onKeyDown (index, e) {
      let $items = this.$el.getElementsByClassName('city-picker-item')[index];
      let $caller = $items.getElementsByClassName('caller');
      let $callerActive = $items.getElementsByClassName('caller active');
      let inlay = this.inlay[index];
      let keyCode = e.keyCode;
      let activeIndex = -1;
      let direction;

      // 按下键盘up/down
      if (keyCode === 38 || keyCode === 40) {
        // 上下方向
        direction = keyCode === 38 ? -1 : 1;
        // 已经选中的索引
        activeIndex = $callerActive[0] ? Number($callerActive[0].getAttribute('data-index')) : activeIndex;
        // 根据方向，然后计算得出索引
        this.countIndex(activeIndex, direction, $caller.length);
        // 清除选中状态
        this.modifyNature(inlay.filterDatas, 'isActive', false);
        // 添加索引选中状态
        this.$set(inlay.filterDatas[this.itemIndex], 'isActive', true);
        this.scroll($items, $caller, this.itemIndex);
        return false;
      }
      e.preventDefault();
    },
    onEnter (index) {
      // 按下回车选中
      this.choice(this.inlay[index].filterDatas[this.itemIndex], index);
      return false;
    },
    countIndex (index, direction, leng) {
        // 判断选中的索引值
        if (this.itemIndex < 0) {
          this.itemIndex = direction > 0 ? -1 : 0;
        } else {
          this.itemIndex = index;
        }
        // 索引与反向相加
        this.itemIndex = this.itemIndex + direction;
        // 循环添加索引
        this.itemIndex = this.itemIndex === leng ? 0 : this.itemIndex < 0 ? leng - 1 : this.itemIndex;
    },
    scroll ($items, $caller, itemIndex) {
      const $listBox = $items.getElementsByClassName('city-picker-list');
      const h = $caller[0].offsetHeight;
      const y =  h * itemIndex;

      $listBox[0].scrollTop = y;
    }
  }
};
</script>

<style lang="scss" scoped>
.city-picker {
  font-size: 0;
}

.city-picker-item {
  display: inline-block;
  vertical-align: top;
  width: 150px;
  margin-left: 10px;
  position: relative;
  &:first-child {
    margin-left: 0;
  }
  &:after {
    content: "";
    border-right: 6px solid transparent;
    border-left: 6px solid transparent;
    border-bottom: 6px solid #ccc;
    display: block;
    width: 0;
    height: 0;
    position: absolute;
    top: 15px;
    right: 5px;
    transition-duration: 400ms;
  }
  &.hide {
    .city-picker-list {
      transform: translateY(-2%);
      opacity: 0;
      visibility: hidden;
    }
    &:after {
      -webkit-transform: rotate(180deg);
      transform: rotate(180deg);
    }
  }
  input[type="text"] {
    border: 1px solid #ccc;
    border-radius: 5px;
    background: #fff;
    color: #333;
    cursor: pointer;
    font-size: 14px;
    display: inline-block;
    width: 100%;
    height: 35px;
    text-indent: 5px;
    box-sizing: border-box;
    text-decoration: none;
    &:focus {
      outline: none;
    }
    &:hover {
      background-color: #fbfbfb;
    }
    &:disabled {
      background-color: #eef1f6;
      cursor: not-allowed;
      color: #666;
    }
  }
}

.city-picker-list {
  border: 1px solid #ccc;
  border-radius: 5px;
  background: #fff;
  box-shadow: 0 0 2px #f3f3f3;
  max-height: 150px;
  overflow-x: hidden;
  overflow-y: auto;
  text-align: left;
  margin: 0;
  padding: 0;
  transition-duration: 400ms;
  transform: translateY(0);
  opacity: 1;
  visibility: visible;
  position: absolute;
  top: 120%;
  left: 0;
  right: 0;
  .caller {
    cursor: pointer;
    font-size: 14px;
    list-style: none;
    padding: 8px;
    transition-duration: 400ms;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
    &:hover {
      background-color: #f9f9f9;
    }
    &.active {
      position: relative;
      text-indent: 10px;
      &:after {
        content: "";
        background: #67bfdc;
        border-radius: 50%;
        width: 8px;
        height: 8px;
        display: block;
        position: absolute;
        top: 50%;
        right: 5px;
        bottom: 10px;
        margin-top: -4px;
      }
    }
  }
}
</style>