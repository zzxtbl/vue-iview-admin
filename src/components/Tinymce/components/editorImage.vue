<template>
    <div class="upload-container">
        <Button icon='upload' @click=" dialogVisible=true" type="primary" :style="{background:color,borderColor:color}">上传图片</Button>
        <Modal v-model="dialogVisible" title="上传图片" @on-ok="handleSubmit">
            <Upload
                    class="editor-audio-upload"
                    action="https://httpbin.org/post"
                    multiple
                    type="drag"
                    :data="dataObj"
                    :file-list="fileList"
                    :show-upload-list="true"
                    list-type="picture-card"
                    :on-remove="handleRemove"
                    :on-success="handleImageScucess">
                <div style="padding: 20px 0">
                    <Icon type="ios-cloud-upload" size="52" style="color: #3399ff"></Icon>
                    <p>点击或将文件拖拽到这里上传</p>
                </div>
            </Upload>
        </Modal>
    </div>
</template>
<script>
    import { getToken } from 'api/qiniu';

    export default {
      name: 'editorSlideUpload',
      props: {
        color: {
          type: String,
          default: '#20a0ff'
        }
      },
      data() {
        return {
          dialogVisible: false,
          dataObj: { token: '', key: '' },
          list: [],
          fileList: []
        };
      },
      methods: {
        handleSubmit() {
          const arr = this.list.map(v => v.url);
          this.$emit('successCBK', arr);
          this.list = [];
          this.fileList = [];
          this.dialogVisible = false;
        },
        handleRemove(file) {
          const key = file.response.key;
          for (let i = 0, len = this.list.length; i < len; i++) {
            if (this.list[i].key === key) {
              this.list.splice(i, 1);
              return
            }
          }
        },
        handleImageScucess(file) {
          this.list.push({ url: file.files.file });
        },
        beforeUpload() {
          const _self = this;
          return new Promise((resolve, reject) => {
            getToken().then(response => {
              const key = response.data.qiniu_key;
              const token = response.data.qiniu_token;
              _self._data.dataObj.token = token;
              _self._data.dataObj.key = key;
              this.list.push({ key, url: response.data.qiniu_url });
              resolve(true);
            }).catch(err => {
              console.log(err);
              reject(false)
            });
          });
        }
      }
    };
</script>

<style rel="stylesheet/scss" lang="scss" scoped>
    .upload-container {
        .editor-slide-upload {
            margin-bottom: 20px;
        }
    }
</style>
