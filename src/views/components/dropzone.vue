<template>
  <div class="components-container">
    <code>https://github.com/rowanwins/vue-dropzone
      由于我司业务有特殊需求，而且要传七牛 所以没用第三方 选择了自己封装
    </code>
    <div class="editor-container">
     <Dropzone  v-on:dropzone-removedFile="dropzoneR" v-on:dropzone-success="dropzoneS" id="myVueDropzone"
            url="https://httpbin.org/post"></Dropzone>
    </div>
  </div>
</template>
<script>
    import Dropzone from 'components/Dropzone';

    export default {
      components: { Dropzone },
      methods: {
        dropzoneS(file) {
          console.log(file)
          this.$Message.success('上传成功');
        },
        dropzoneR(file) {
          console.log(file)
          this.$Message.success('删除成功');
        }
      }
    };
</script>
