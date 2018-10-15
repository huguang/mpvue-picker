<template>
  <div class="mvpue-picker">
    <div class="page-hd">
      <div class="page-title">mvpue-picker 示例</div>
      <div class="page__desc">选中的值:</div>
      <div class="picker-text">{{pickerText}}</div>
    </div>
    <div class="page-bd">
      <button type="default" @click="showSinglePicker">单列选择</button>
      <button type="default" @click="showTimePicker">时间选择</button>
      <button type="default" @click="showMulPicker">多列选择</button>
      <button type="default" @click="showMulLinkageTwoPicker">二级联动选择</button>
      <button type="default" @click="showMulLinkageThreePicker">三级联动选择</button>
      <button type="default" @click="showMulLinkageRegionPicker">地域联动选择</button>
    </div>
    <mpvue-picker ref="mpvuePicker" :mode="mode" :deepLength="deepLength"
                  :pickerValueDefault="pickerValueDefault" :pickerValueArray="pickerValueArray"
                  @onChange="onChange" @onConfirm="onConfirm" @onCancel="onCancel"
                  themeColor="#5773F9"></mpvue-picker>
  </div>
</template>

<script>
import mpvuePicker from '@/mpvue-picker/mpvuePicker.vue';
const single = require('@/dicts/single.json');
const multi = require('@/dicts/multi.json');
const linkageTwo = require('@/dicts/linkage-two.json');
const linkageThree = require('@/dicts/linkage-three.json');
const regions = require('@/dicts/regions.json');
export default {
  components: {
    mpvuePicker
  },
  data() {
    return {
      mode: 'selector',
      deepLength: 0, // 几级联动
      pickerValueDefault: [], // 初始化值
      pickerValueArray: [], // picker 数组值
      pickerText: '',
      pickerSingleArray: single,
      pickerMulArray: multi,
      mulLinkageTwoPicker: linkageTwo,
      mulLinkageThreePicker: linkageThree,
      mulLinkageRegionPicker: regions
    };
  },
  methods: {
    onChange(e) {
      console.log('index onChange')
      console.log(e);
    },
    onCancel(e) {
      console.log('index onCancel')
      console.log(e);
    },
    onConfirm(e) {
      console.log('index onConfirm')
      console.log(e);
      if (this.mode === 'selector') {
        this.pickerText = e.label;
      } else if (this.mode === 'timeSelector') {
        this.pickerText = e.label;
      } else if (this.mode === 'multiSelector') {
        this.pickerText = e.label;
      } else if (this.mode === 'multiLinkageSelector') {
        this.pickerText = e.label;
      }
    },
    // 单列
    showSinglePicker() {
      this.pickerValueArray = this.pickerSingleArray;
      this.mode = 'selector';
      this.pickerValueDefault = [];
      this.$refs.mpvuePicker.show();
    },
    // 时间选择
    showTimePicker() {
      this.mode = 'timeSelector';
      this.pickerValueDefault = [1, 2];
      this.$refs.mpvuePicker.show();
    },
    // 多列选择
    showMulPicker() {
      this.pickerValueArray = this.pickerMulArray;
      this.mode = 'multiSelector';
      this.pickerValueDefault = [1, 1, 1];
      this.$refs.mpvuePicker.show();
    },
    // 二级联动选择
    showMulLinkageTwoPicker() {
      this.pickerValueArray = this.mulLinkageTwoPicker;
      this.mode = 'multiLinkageSelector';
      this.deepLength = 2;
      this.pickerValueDefault = [1, 0];
      this.$refs.mpvuePicker.show();
    },
    // 三级联动选择
    showMulLinkageThreePicker() {
      this.pickerValueArray = this.mulLinkageThreePicker;
      this.mode = 'multiLinkageSelector';
      this.deepLength = 3;
      this.pickerValueDefault = [1, 1, 1];
      this.$refs.mpvuePicker.show();
    },
    // 地域联动选择
    showMulLinkageRegionPicker() {
      this.pickerValueArray = this.mulLinkageRegionPicker;
      this.mode = 'multiLinkageSelector';
      this.deepLength = 3;
      this.pickerValueDefault = [0, 0, 0];
      this.$refs.mpvuePicker.show();
    },
    showPickerView() {
      this.$refs.mpvuePicker.show();
    }
  }
};
</script>

<style>
.page-hd {
  padding: 40px;
}
.page-title {
  font-size: 20px;
  font-weight: 400px;
}
.page-bd {
  padding: 15px;
}
.picker-text,
.page__desc {
  margin-top: 10px;
}
button {
  margin-top: 15px;
}
</style>
