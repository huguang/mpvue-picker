<template>
  <div class="mpvue-picker">
    <div :class="{'pickerMask':showPicker}" @click="maskClick" catchtouchmove="true"></div>
    <div class="mpvue-picker-content " :class="{'mpvue-picker-view-show':showPicker}">
      <div class="mpvue-picker__hd" catchtouchmove="true">
        <div class="mpvue-picker__action" @click="pickerCancel">取消</div>
        <div class="mpvue-picker__action" :style="{color: themeColor}" @click="pickerConfirm">确定</div>
      </div>
      <!-- 单列 -->
      <picker-view indicator-style="height: 40px;" class="mpvue-picker-view" :value="pickerValue" @change="pickerChange" v-if="mode==='selector' && pickerValueSingleArray.length > 0">
        <block>
          <picker-view-column>
            <div class="picker-item" v-for="(item,index) in pickerValueSingleArray" :key="index">{{item.label}}</div>
          </picker-view-column>
        </block>
      </picker-view>
      <!-- 时间选择器 -->
      <picker-view indicator-style="height: 40px;" class="mpvue-picker-view" :value="pickerValue" @change="pickerChange" v-if="mode==='timeSelector'">
        <block>
          <picker-view-column>
            <div class="picker-item" v-for="(item,index) in pickerValueHour" :key="index">{{item.label}}</div>
          </picker-view-column>
          <picker-view-column>
            <div class="picker-item" v-for="(item,index) in pickerValueMinute" :key="index">{{item.label}}</div>
          </picker-view-column>
        </block>
      </picker-view>
      <!-- 多列选择 -->
      <picker-view indicator-style="height: 40px;" class="mpvue-picker-view" :value="pickerValue" @change="pickerChange" v-if="mode==='multiSelector'">
        <block>
          <picker-view-column v-for="(n,index) in pickerValueMulArray.length" :key="index">
            <div class="picker-item" v-for="(item,index1) in pickerValueMulArray[n]" :key="index1">{{item.label}}</div>
          </picker-view-column>
        </block>
      </picker-view>
      <!-- 联动选择器 -->
      <picker-view indicator-style="height: 40px;" class="mpvue-picker-view" :value="pickerValue" @change="pickerChangeMultiLinkage" v-if="mode==='multiLinkageSelector'">
        <block>
          <picker-view-column v-for="(colum, columnIndex) in pickerValueMulArray" :key="columnIndex">
            <div class="picker-item" v-for="(item, index) in colum" :key="index">{{item.label}}</div>
          </picker-view-column>
        </block>
      </picker-view>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      pickerChangeValue: [],
      pickerValue: [],
      pickerValueArrayChange: true,
      modeChange: false,
      pickerValueSingleArray: [],
      pickerValueHour: [],
      pickerValueMinute: [],
      pickerValueMulArray: [],
      pickerValueMulTwoOne: [],
      pickerValueMulTwoTwo: [],
      pickerValueMulThreeOne: [],
      pickerValueMulThreeTwo: [],
      pickerValueMulThreeThree: []
    };
  },
  props: {
    /* mode */
    mode: {
      type: String,
      default: 'selector'
    },
    /* 是否显示控件 */
    showPicker: {
      type: Boolean,
      default: false
    },
    /* picker 数值 */
    pickerValueArray: {
      type: Array,
      default: []
    },
    /* 默认值 */
    pickerValueDefault: {
      type: Array,
      default: []
    },
    /* 几级联动 */
    deepLength: {
      type: Number,
      default: 2
    },
    /* 主题色 */
    themeColor: String
  },
  watch: {
    pickerValueArray(oldVal, newVal) {
      this.pickerValueArrayChange = true;
    },
    mode(oldVal, newVal) {
      this.modeChange = true;
    }
  },
  methods: {
    initPicker(valueArray) {
      let pickerValueArray = valueArray;
      this.pickerValue = this.pickerValueDefault;
      // 初始化多级联动
      if (this.mode === 'selector') {
        this.pickerValueSingleArray = valueArray;
      } else if (this.mode === 'timeSelector') {
        this.modeChange = false;
        let hourArray = [];
        let minuteArray = [];
        for (let i = 0; i < 24; i++) {
          hourArray.push({value: i, label: i > 9 ? `${i} 时` : `0${i} 时`});
        }
        for (let i = 0; i < 60; i++) {
          minuteArray.push({value: i, label: i > 9 ? `${i} 分` : `0${i} 分`});
        }
        this.pickerValueHour = hourArray;
        this.pickerValueMinute = minuteArray;
      } else if (this.mode === 'multiSelector') {
        this.pickerValueMulArray = valueArray;
      } else if (this.mode === 'multiLinkageSelector') {
        this.pickerValueMulArray = [];
        let roots = pickerValueArray;
        for (let i = 0; i < this.deepLength; i++) {
          const ary = [];
          roots.forEach((item) => {
            ary.push({'value': item.value, 'label': item.label});
          });
          this.pickerValueMulArray.push(ary);
          // 准备下一级的列表
          let defaultValueIndex = this.pickerValueDefault[i];
          if (defaultValueIndex > ary.length) {
            defaultValueIndex = 0;
          }
          const selected = roots[defaultValueIndex];
          if (selected) {
            roots = selected.children || [];
          }
        }
      }
    },
    show() {
      setTimeout(() => {
        if (this.pickerValueArrayChange || this.modeChange) {
          this.initPicker(this.pickerValueArray);
          this.showPicker = true;
          this.pickerValueArrayChange = false;
          this.modeChange = false;
        } else {
          this.showPicker = true;
        }
      }, 0);
    },
    maskClick() {
      this.pickerCancel();
    },
    pickerCancel() {
      this.showPicker = false;
      this._initPickerValue();
      let pickObj = {
        index: this.pickerValue,
        value: this._getPickerLabelAndValue(this.pickerValue, this.mode).value,
        label: this._getPickerLabelAndValue(this.pickerValue, this.mode).label
      };
      this.$emit('onCancel', pickObj);
    },
    pickerConfirm(e) {
      this.showPicker = false;
      this._initPickerValue();
      let pickObj = {
        index: this.pickerValue,
        value: this._getPickerLabelAndValue(this.pickerValue, this.mode).value,
        label: this._getPickerLabelAndValue(this.pickerValue, this.mode).label
      };
      this.$emit('onConfirm', pickObj);
    },
    showPickerView() {
      this.showPicker = true;
    },
    pickerChange(e) {
      this.pickerValue = e.mp.detail.value;
      let pickObj = {
        index: this.pickerValue,
        value: this._getPickerLabelAndValue(this.pickerValue, this.mode).value,
        label: this._getPickerLabelAndValue(this.pickerValue, this.mode).label
      };
      this.$emit('onChange', pickObj);
    },
    pickerChangeMultiLinkage (e) {
      let changeValue = e.mp.detail.value;
      this.pickerValueMulArray = [];
      let roots = this.pickerValueArray;
      for (let i = 0; i < this.deepLength; i++) {
        const columnArray = [];
        roots.forEach((item) => {
          columnArray.push({'value': item.value, 'label': item.label});
        });
        this.pickerValueMulArray.push(columnArray);
        // 准备下一级的列表
        let idx = changeValue[i];
        if (idx >= columnArray.length) {
          idx = 0;
        }
        const selected = roots[idx];
        if (selected) {
          roots = selected.children || [];
        }
      }
      this.pickerValue = changeValue;
      let pickObj = {
        index: this.pickerValue,
        value: this._getPickerLabelAndValue(this.pickerValue, this.mode).value,
        label: this._getPickerLabelAndValue(this.pickerValue, this.mode).label
      };
      this.$emit('onChange', pickObj);
    },
    // 获取 pxikerLabel
    _getPickerLabelAndValue(value, mode) {
      let pickerLable;
      let pickerGetValue = [];
      // selector
      if (mode === 'selector') {
        pickerLable = this.pickerValueSingleArray[value].label;
        pickerGetValue.push(this.pickerValueSingleArray[value].value);
      } else if (mode === 'timeSelector') {
        pickerLable = `${this.pickerValueHour[value[0]].label}-${this.pickerValueMinute[value[1]].label}`;
        pickerGetValue.push(this.pickerValueHour[value[0]].value);
        pickerGetValue.push(this.pickerValueHour[value[1]].value);
      } else if (mode === 'multiSelector') {
        for (let i = 0; i < value.length; i++) {
          if (i > 0) {
            pickerLable += this.pickerValueMulArray[i][value[i]].label + (i === value.length - 1 ? '' : '-');
          } else {
            pickerLable = this.pickerValueMulArray[i][value[i]].label + '-';
          }
          pickerGetValue.push(this.pickerValueMulArray[i][value[i]].value);
        }
      } else if (mode === 'multiLinkageSelector') {
        pickerLable = '';
        let roots = this.pickerValueArray;
        for (let i = 0; i < this.deepLength; i++) {
          let selectedElement = roots[value[i]];
          if (selectedElement) {
            pickerLable += i > 0 ? '-' + selectedElement.label : selectedElement.label;
            pickerGetValue.push(selectedElement.value);
            roots = selectedElement.children || [];
          } else {
            pickerGetValue.push(0);
            roots = [];
          }
        }
      }
      return {
        label: pickerLable,
        value: pickerGetValue
      };
    },
    // 初始化 pickerValue 默认值
    _initPickerValue() {
      if (this.pickerValue.length === 0) {
        if (this.mode === 'selector') {
          this.pickerValue = [0];
        } else if (this.mode === 'multiSelector') {
          this.pickerValue = new Int8Array(this.pickerValueArray.length);
        } else if (this.mode === 'multiLinkageSelector') {
          for (let i = 0; i < this.deepLength; i++) {
            this.pickerValue.push(0);
          }
        }
      }
    }
  }
};
</script>

<style>
.pickerMask {
  position: fixed;
  z-index: 1000;
  top: 0;
  right: 0;
  left: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.6);
}
.mpvue-picker-content {
  position: fixed;
  bottom: 0;
  left: 0;
  width: 100%;
  transition: all 0.3s ease;
  transform: translateY(100%);
  z-index: 3000;
}
.mpvue-picker-view-show {
  transform: translateY(0);
}
.mpvue-picker__hd {
  display: flex;
  padding: 9px 15px;
  background-color: #fff;
  position: relative;
  text-align: center;
  font-size: 17px;
}
.mpvue-picker__hd:after {
  content: ' ';
  position: absolute;
  left: 0;
  bottom: 0;
  right: 0;
  height: 1px;
  border-bottom: 1px solid #e5e5e5;
  color: #e5e5e5;
  transform-origin: 0 100%;
  transform: scaleY(0.5);
}
.mpvue-picker__action {
  display: block;
  flex: 1;
  color: #1aad19;
}
.mpvue-picker__action:first-child {
  text-align: left;
  color: #888;
}
.mpvue-picker__action:last-child {
  text-align: right;
}
.picker-item {
  text-align: center;
  line-height: 40px;
  font-size: 16px;
}
.mpvue-picker-view {
  position: relative;
  bottom: 0;
  left: 0;
  width: 100%;
  height: 238px;
  background-color: rgba(255, 255, 255, 1);
}
</style>
