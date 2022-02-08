<template>
  <ul class="tabs" :class="{[classPrefix+'-tabs']:classPrefix}">
    <li v-for="item in dataSource" :key="item.value"
        class="tabs-item"
        :class="{selected: item.value===value,[classPrefix+'-tabs-item']:classPrefix}"
        @click="select(item)">{{ item.text }}
    </li>
  </ul>
</template>

<script lang="ts">
import Vue from 'vue';
import {Component, Prop} from 'vue-property-decorator';

type DataSourceItem = { text: string, value: string }

@Component
export default class Tabs extends Vue {
  @Prop({required: true, type: Array}) dataSource!: DataSourceItem[];
  @Prop(String) readonly value!: string;
  @Prop(String) classPrefix?: string;


  select(item: DataSourceItem) {
    this.$emit('update:value', item.value);
  }
}
</script>

<style lang="scss" scoped>
@import "./src/assets/style/helper";
.tabs {
  //background: #c4c4c4;
  display: flex;
  text-align: center;
  font-size: 26px;
  font-family: $font-kai;

  &-item {
    width: 50%;
    height: 64px;
    display: flex;
    justify-content: center;
    align-items: center;
    position: relative;

    &.selected::after {
      content: '';
      position: absolute;
      bottom: 10px;
      //left: 0;
      width: 25%;
      height: 2px;
      background: #333;
    }
  }

  &-item:nth-child(1) {
    padding-left: 40px;
  }

  &-item:nth-child(2) {
    padding-right: 40px;
  }
}
</style>