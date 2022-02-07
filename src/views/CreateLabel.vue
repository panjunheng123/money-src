<template>
  <Layout>
    <div class="navBar">
      <Icon class="leftIcon" name="left" @click="goBack"></Icon>
      <span class="title">新增自定义标签</span>
      <span class="rightIcon"></span>
    </div>
    <div class="form-wrapper">
      <FormItem :value.sync="name" field-name="标签名" placeholder="请输入标签名"></FormItem>
    </div>
    <div class="chooseIcon">
      <div class="text">选择一个图标</div>
      <div class="icon-wrapper">
        <li v-for="iconName in iconNames" :key="iconName"
            @click="toggle(iconName)"
            :class="{selected:iconName===currentIconName}"
        >
          <Icon :name="iconName"></Icon>
        </li>
      </div>
    </div>
    <div class="button-wrapper">
      <Button @click="save">保存</Button>
    </div>
  </Layout>
</template>

<script lang="ts">
import Vue from 'vue';
import {Component} from 'vue-property-decorator';
import FormItem from '@/components/Money/FormItem.vue';
import Button from '@/components/Button.vue';

@Component({
  components: {Button, FormItem: FormItem},
})
export default class EditLabel extends Vue {
  name = '';
  iconNames = ['笑脸', '手柄', '乒乓球', '音乐', '盾牌保卫', '视频', '木马', '水瓶', '特色', '品牌'];
  currentIconName = '';

  save() {
    if (this.name === '') {
      return window.alert('请输入标签名');
    }
    if (this.currentIconName === '') {
      return window.alert('请选择一个图标');
    }
    this.$store.commit('createTag', {name: this.name, type: this.$route.params.type, iconName: this.currentIconName});
    if (this.$store.state.createTagError) {
      if (this.$store.state.createTagError.message === 'tag name duplicated') {
        return window.alert('标签名重复了');
      }
    }
    return window.alert('添加成功');
  }

  toggle(value: string) {
    this.currentIconName = value;
  }

  goBack() {
    this.$router.back();
  }
}
</script>

<style lang="scss" scoped>
.navBar {
  text-align: center;
  font-size: 16px;
  padding: 12px 16px;
  background: rgb(236, 236, 236);
  display: flex;
  align-items: center;
  justify-content: space-between;

  > .title {
  }

  > .leftIcon {
    width: 24px;
    height: 24px;
  }

  > .rightIcon {
    width: 24px;
    height: 24px;
  }
}

.form-wrapper {
  background: rgb(236, 236, 236);
  margin-top: 8px;
}

.button-wrapper {
  text-align: center;
  padding: 16px;
  margin-top: 44-16px;
}

.chooseIcon {
  padding-top: 24px;

  > .text {
    font-size: 24px;
    background: transparent;
    color: #333;
    padding: 0 4px;
  }

  > .icon-wrapper {
    display: flex;
    flex-wrap: wrap;

    li {
      list-style: none;
      width: 80px;
      height: 80px;
      margin: 8px;
      border: 1px solid transparent;
      $bg: rgb(236, 236, 236);
      background: $bg;
      border-radius: 50%;
      padding: 12px;

      &.selected {
        background: darken($bg, 35%);
      }

      svg {
        width: 100%;
        height: 100%;
      }
    }
  }
}
</style>