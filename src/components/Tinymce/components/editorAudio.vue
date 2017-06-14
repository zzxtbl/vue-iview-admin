<template>
    <div class="upload-container">
        <Button @click=" dialogVisible=true" type="primary" :style="{background:color,borderColor:color}">上传音频</Button>
        <Modal v-model="dialogVisible" title="上传音频" @on-ok="handleSubmit">
            <Form ref="form" :model="form" :rules="rules" :label-width="100">
                <Upload class="editor-audio-upload" action="https://upload.qbox.me" :data="dataObj" :show-file-list="true" :file-list="audioList" :on-success="handleAudioScucess" :on-change="handleAudioChange" :before-upload="audioBeforeUpload">
                    <Button size="small" type="primary">上传音频</Button>
                </Upload>
                <Form-item prop="url" label="音频URL">
                    <Input v-model="form.url" placeholder="请输入音频URL" />
                </Form-item>
                <Form-item prop="title" label="音频标题">
                    <Input v-model="form.title" placeholder="请输入音频标题" />
                </Form-item>
                <Form-item label="音频文本">
                    <Input type="textarea" :autosize="{ minRows: 2}" v-model="form.text" />
                </Form-item>
            </Form>
        </Modal>
    </div>
</template>
<script>
    import { getToken } from 'api/qiniu';

    export default {
      name: 'editorAudioUpload',
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
          audioList: [],
          tempAudioUrl: '',
          form: {
            title: '',
            url: '',
            text: ''
          },
          rules: {
            title: [
                        { required: true, trigger: 'blur' }
            ],
            url: [
                        { required: true, trigger: 'blur' }
            ]
          }
        };
      },
      methods: {
        handleSubmit() {
          this.$refs.form.validate(valid => {
            if (valid) {
              this.$emit('successCBK', this.form);
              this.dialogVisible = false;
              this.form = {
                title: '',
                url: '',
                text: ''
              }
            } else {
              this.$message('填写有误');
              return false;
            }
          });
        },
        handleAudioChange(file, fileList) {
          this.audioList = fileList.slice(-1);
        },
        handleAudioScucess() {
          this.form.url = this.tempAudioUrl
        },
        audioBeforeUpload() {
          const _self = this;
          return new Promise((resolve, reject) => {
            getToken().then(response => {
              const key = response.data.qiniu_key;
              const token = response.data.qiniu_token;
              _self._data.dataObj.token = token;
              _self._data.dataObj.key = key;
              this.tempAudioUrl = response.data.qiniu_url;
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
        .editor-audio-upload {
            button {
                float: left;
                margin-left: 30px;
                margin-bottom: 20px;
            }
        }
    }
</style>
