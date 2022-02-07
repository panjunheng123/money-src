<template>
  <div class="tags">
    <ul class="current">
      <li v-for="tag in tagList" :key="tag.id"
          v-show="tag.type===type"
          :class="{selected:selectedTags.indexOf(tag)>=0}"
          @click="toggle(tag)">
        <Icon :name="tag.iconName" class="myIcon"></Icon>
        <span class="myFont">{{ tag.name }}</span>
      </li>
      <li>
        <router-link to="/labels" class="item" active-class="selected">
          <Icon class="myIcon" name="设置"/>
          <span class="myFont">设置</span>
        </router-link>
      </li>
    </ul>
    <!--        <div class="new">-->
    <!--          <button @click="createTag">新增标签</button>-->
    <!--        </div>-->
  </div>

</template>

<script lang="ts">
import {Component, Prop} from 'vue-property-decorator';
import {mixins} from 'vue-class-component';
import TagHelper from '@/mixins/TagHelper';

@Component
export default class Tags extends mixins(TagHelper) {

  @Prop(String) readonly type!: string;

  selectedTags: string[] = [];

  get tagList() {
    return this.$store.state.tagList;
  }

  created() {
    this.$store.commit('fetchTags');
  }

  toggle(tag: string) {
    const index = this.selectedTags.indexOf(tag);
    if (index >= 0) {
      this.selectedTags.splice(index, 1);
    } else {
      this.selectedTags = [];
      this.selectedTags.push(tag);
    }
    this.$emit('update:value', this.selectedTags);
  }
}
</script>

<style lang="scss" scoped>
@import "./src/assets/style/helper";
.tags {
  //background: white;
  font-size: 14px;
  padding: 16px;
  flex-grow: 1;

  > .current {
    display: flex;
    flex-wrap: wrap;

    > li {
      //$bg: #d9d9d9;
      $bg: rgb(236, 236, 236);
      background: $bg;
      $h: 80px;
      height: $h;
      width: $h;
      border-radius: $h/2;
      padding: 6px 18px;
      margin-left: 8px;
      margin-top: 4px;
      display: flex;
      flex-direction: column;

      &.selected {
        background: darken($bg, 35%);
        color: #fff;
        //border: 1px solid $color-highlight;
      }

      .myIcon {
        color: #333;
        font-size: 46px;
      }

      .myFont {
        margin-left: 8px;
      }
    }
  }

  > .new {
    padding-top: 16px;

    button {
      background: transparent;
      border: none;
      color: #999;
      border-bottom: 1px solid;
      padding: 0 4px;
    }
  }
}
</style>