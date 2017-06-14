<template>
  <div class="app-container">
    <code>会补动态换肤的教程</code>
    <i-card class="box-card">
      <div slot="header">
        <span style="line-height: 36px;">偏好设置</span>
      </div>

      <div class="box-item">
        <span class="field-label">换肤:</span>
        <i-switch v-model="theme" on-text="" off-text="">
        </i-switch>
      </div>
    </i-card>

    <div class="block">
      <span class="demonstration">Button: </span>
      <span class="wrapper">
        <i-button type="success">成功按钮</i-button>
        <i-button type="warning">警告按钮</i-button>
        <i-button type="danger">危险按钮</i-button>
        <i-button type="info">信息按钮</i-button>
       </span>
    </div>

    <div class="block">
      <i-tag class='tag-item' v-for="tag in tags" :type="tag.type" :key='tag.type'>
        {{tag.name}}
      </i-tag>
    </div>

    <div class="block">
      <i-alert class='alert-item' title="成功提示的文案" type="success">
      </i-alert>
      <i-alert class='alert-item' title="消息提示的文案" type="info">
      </i-alert>
      <i-alert class='alert-item' title="警告提示的文案" type="warning">
      </i-alert>
      <i-alert class='alert-item' title="错误提示的文案" type="error">
      </i-alert>
    </div>

  </div>
</template>


<script>
import { toggleClass } from 'utils';

export default {
  data() {
    return {
      theme: '',
      tags: [
          { name: '标签一', type: '' },
          { name: '标签二', type: 'gray' },
          { name: '标签三', type: 'primary' },
          { name: '标签四', type: 'success' },
          { name: '标签五', type: 'warning' },
          { name: '标签六', type: 'danger' }
      ],
      inputVisible: false,
      inputValue: ''
    }
  },
  watch: {
    theme() {
      toggleClass(document.body, 'custom-theme')
        //   this.$store.dispatch('setTheme', value);
    }
  },
  methods: {

  }
};
</script>

<style scoped>
.box-card{
  width: 400px;
  margin: 20px auto;
}
.block{
  padding: 30px 24px;
}
.alert-item{
  margin-bottom: 10px;
}
.tag-item{
  margin-right: 15px;
}
</style>
