<template>
  <div class="numberPad">
    <div class="output">{{ output }}</div>
    <form>
      <div class="buttons">
        <button @click="inputContent">7</button>
        <button @click="inputContent">8</button>
        <button @click="inputContent">9</button>
        <input v-once id="datePicker" @change="updateDate($event.target.value)" :value="defaultDate" class="datePicker"
               type="date"/>
        <button @click="inputContent">4</button>
        <button @click="inputContent">5</button>
        <button @click="inputContent">6</button>
        <button @click="remove">删除</button>
        <!--      <button @click="clear">清空</button>-->
        <button @click="inputContent">1</button>
        <button @click="inputContent">2</button>
        <button @click="inputContent">3</button>
        <button class="ok" @click="ok">完成</button>
        <button @click="inputContent" class="zero">0</button>
        <button @click="inputContent">.</button>
      </div>
    </form>
  </div>
</template>

<script lang="ts">
import Vue from 'vue';
import {Component, Prop} from 'vue-property-decorator';
import dayjs from 'dayjs';

@Component
export default class NumberPad extends Vue {
  @Prop() readonly value!: number;
  output = '0';
  createdDate = '';
  defaultDate = dayjs().format('YYYY-MM-DD');


  updateDate(value: string) {
    this.createdDate = dayjs(value).toISOString();
  }

  inputContent(event: MouseEvent) {
    const button = (event.target as HTMLButtonElement);
    const input = button.textContent!;
    if (this.output.length === 10) {return;}
    if (this.output === '0') {
      if ('0123456789'.indexOf(input) >= 0) {
        this.output = input;
      } else {
        this.output += input;
      }
      return;
    }
    if (this.output.indexOf('.') >= 0 && input === '.') {return;}
    this.output += input;
  }

  remove() {
    if (this.output.length === 1) {
      this.output = '0';
    } else {
      this.output = this.output.slice(0, -1);
    }
  }

  clear() {
    this.output = '0';
  }

  ok() {
    const number = parseFloat(this.output);
    if (number === 0) {
      window.alert('请输入金额！');
      return;
    }
    this.$emit('update:value', this.output);
    this.$emit('submit', this.createdDate);
    this.clear();
  }

}
</script>

<style lang="scss" scoped>
@import "./src/assets/style/helper";

.numberPad {
  .output {
    @extend %clearFix;
    //@extend %innerShadow;
    font-size: 36px;
    font-family: Consolas, monospace;
    padding: 0 16px;
    text-align: right;
    height: 56px;
    line-height: 56px;
  }

  .buttons {
    @extend %clearFix;
    background-color: rgb(243, 243, 243);

    > .datePicker {
      padding-top: 6px;
      padding-left: 6px;
      font-size: 14px;
      width: 25%;
      height: 64px;
      background-color: rgb(243, 243, 243);
      border: 1px solid rgb(201, 201, 201);
      display: flex;
      flex-direction: column-reverse;
    }

    > button {
      width: 25%;
      height: 64px;
      float: left;
      background: transparent;
      border: 1px solid rgb(201, 201, 201);
      font-size: 20px;

      &.ok {
        height: 64*2px;
        float: right;
      }


      &.zero {
        width: 50%;
      }

      $bg: #f2f2f2;
      //
      //&:nth-child(1) {
      //  background: $bg;
      //}
      //
      //&:nth-child(2), &:nth-child(5) {
      //  background: darken($bg, 4%);
      //}
      //
      //&:nth-child(3), &:nth-child(6), &:nth-child(9) {
      //  background: darken($bg, 4*2%);
      //}
      //
      //&:nth-child(4), &:nth-child(7), &:nth-child(10) {
      //  background: darken($bg, 4*3%);
      //}
      //
      //&:nth-child(8), &:nth-child(11), &:nth-child(13) {
      //  background: darken($bg, 4*4%);
      //}
      //
      //&:nth-child(14) {
      //  background: darken($bg, 4*5%);
      //}
      //
      //&:nth-child(12) {
      //  background: darken($bg, 4*6%);
      //}
    }
  }
}
</style>