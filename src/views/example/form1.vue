<template>
<div class="createPost-container">
    <Form class="form-container" :model="postForm" :rules="rules" ref="postForm" :label-width="80">
        <Sticky :className="'sub-navbar '+postForm.status">
            <template v-if="fetchSuccess">
                <Dropdown trigger="click">
                    <Button>{{!postForm.comment_disabled?'评论已打开':'评论已关闭'}}<Icon type="arrow-down-b"></Icon></Button>
                    <Dropdown-menu class="no-padding no-hover" slot="list">
                        <Dropdown-item>
                            <Radio-group v-model="postForm.comment_disabled">
                                <Radio :label="1">关闭评论</Radio>
                                <Radio :label="0">打开评论</Radio>
                            </Radio-group>
                        </Dropdown-item>
                    </Dropdown-menu>
                </Dropdown>

                <Dropdown trigger="click">
                    <Button>
                        平台<Icon type="arrow-down-b"></Icon>
                    </Button>
                    <Dropdown-menu class="no-border" slot="list">
                        <Checkbox-group v-model="postForm.platforms">
                            <Checkbox v-for="item in platformsOptions" :label="item.key" :key="item.key">
                                {{item.name}}
                            </Checkbox>
                        </Checkbox-group>
                    </Dropdown-menu>
                </Dropdown>

                <Dropdown trigger="click">
                    <Button>
                        外链<Icon type="arrow-down-b"></Icon>
                    </Button>
                    <Dropdown-menu class="no-padding no-border" style="width:300px" slot="list">
                        <Form-item prop="source_uri">
                            <Input placeholder="请输入内容" v-model="postForm.source_uri">
                                <template slot="prepend">填写url</template>
                            </Input>
                        </Form-item>
                    </Dropdown-menu>
                </Dropdown>

                <Button :loading="loading1" style="margin-left: 10px;" type="success" @click="submitForm()">发布</Button>
                <Button :loading="loading2" type="warning" @click="draftForm">草稿</Button>
            </template>
            <template v-else>
                <Tag>发送异常错误,刷新页面,或者联系程序员</Tag>
            </template>
        </Sticky>

        <div class="createPost-main-container">
            <Row>
                <Col :span="21">
                    <Form-item style="margin-bottom: 40px;" prop="title">
                        <MDinput name="name" v-model="postForm.title" required :maxlength="100">
                            标题
                        </MDinput>
                        <span v-show="postForm.title.length>=26" class='title-prompt'>app可能会显示不全</span>
                    </Form-item>

                    <div class="postInfo-container">
                        <Row>
                            <Col :span="8">
                                <Form-item label="作者:" class="postInfo-container-item">
                                    <multiselect v-model="postForm.author" :options="userLIstOptions" @search-change="getRemoteUserList" placeholder="搜索用户" selectLabel="选择" deselectLabel="删除" track-by="key" :internalSearch="false" label="key">
                                        <span slot='noResult'>无结果</span>
                                    </multiselect>
                                </Form-item>
                            </Col>

                            <Col :span="8">
                                <Tooltip class="item" effect="dark" content="将替换作者" placement="top">
                                    <Form-item label="来源:" class="postInfo-container-item">
                                        <Input placeholder="将替换作者" style='min-width:150px;' v-model="postForm.source_name"></Input>
                                    </Form-item>
                                </Tooltip>
                            </Col>

                            <Col :span="8">
                                <Form-item label="发布时间:" class="postInfo-container-item">
                                    <Date-picker v-model="postForm.display_time" type="datetime" format="yyyy-MM-dd HH:mm:ss" placeholder="选择日期时间"></Date-picker>
                                </Form-item>
                            </Col>
                        </Row>
                    </div>
                </Col>
            </Row>

            <Form-item style="margin-bottom: 40px;" label="摘要:">
                <Input type="textarea" class="article-textarea" :rows="1" autosize placeholder="请输入内容" v-model="postForm.content_short"></Input>
                <span class="word-counter" v-show="contentShortLength">{{contentShortLength}}字</span>
            </Form-item>

            <div class="editor-container">
                <Tinymce :height=400 ref="editor" v-model="postForm.content"></Tinymce>
            </div>

            <div style="margin-bottom: 20px;">
                <Upload v-model="postForm.image_uri"></Upload>
            </div>
        </div>
    </Form>
</div>
</template>

<script>
import Tinymce from 'components/Tinymce'
import Upload from 'components/Upload/singleImage3'
import MDinput from 'components/MDinput';
import { validateURL } from 'utils/validate';
import { getArticle } from 'api/article';
import { userSearch } from 'api/remoteSearch';

export default {
    name: 'articleDetail',
    components: {
        Tinymce,
        MDinput,
        Upload
    },
    data() {
        const validateRequire = (rule, value, callback) => {
            if (value === '') {
                this.$Message.error( rule.field + '为必传项' );
                callback(null)
            } else {
                callback()
            }
        };
        const validateSourceUri = (rule, value, callback) => {
            if (value) {
                if (validateURL(value)) {
                    callback()
                } else {
                    this.$Message.error( '外链url填写不正确' );
                    callback(null)
                }
            } else {
                callback()
            }
        };
        return {
            postForm: {
                title: '', // 文章题目
                content: '', // 文章内容
                content_short: '', // 文章摘要
                source_uri: '', // 文章外链
                image_uri: '', // 文章图片
                source_name: '', // 文章外部作者
                display_time: undefined, // 前台展示时间
                id: undefined,
                platforms: ['a-platform'],
                comment_disabled: 0
            },
            fetchSuccess: true,
            loading1: false,
            loading2: false,
            userLIstOptions: [],
            platformsOptions: [{
                    key: 'a-platform',
                    name: 'a-platform'
                },
                {
                    key: 'b-platform',
                    name: 'b-platform'
                },
                {
                    key: 'c-platform',
                    name: 'c-platform'
                }
            ],
            rules: {
                image_uri: [{
                    validator: validateRequire
                }],
                title: [{
                    validator: validateRequire
                }],
                content: [{
                    validator: validateRequire
                }],
                source_uri: [{
                    validator: validateSourceUri,
                    trigger: 'blur'
                }]
            }
        }
    },
    computed: {
        contentShortLength() {
            return this.postForm.content_short.length
        }
    },
    created() {
        this.fetchData();
    },
    methods: {
        fetchData() {
            getArticle().then(response => {
                this.postForm = response.data;
            }).catch(err => {
                this.fetchSuccess = false;
                console.log(err);
            });
        },
        submitForm() {
            this.postForm.display_time = parseInt(this.display_time / 1000);
            console.log(this.postForm)
            this.$refs.postForm.validate(valid => {
                if (valid) {
                    this.loading1 = true;
                    this.$notify({
                        title: '成功',
                        message: '发布文章成功',
                        type: 'success',
                        duration: 2000
                    });
                    this.postForm.status = 'published';
                    this.loading1 = false;
                } else {
                    console.log('error submit!!');
                    return false;
                }
            });
        },
        draftForm() {
            this.loading2 = true;
            if (this.postForm.content.length === 0 || this.postForm.title.length === 0) {
                this.$Message.warning('请填写必要的标题和内容');
                this.loading2 = false;
                return;
            }
            this.$Message.success({
                content: '保存成功',
                duration: 1000,
                closable: true
            });
            this.postForm.status = 'draft';
        },
        getRemoteUserList(query) {
            userSearch(query).then(response => {
                if (!response.data.items) return;
                console.log(response)
                this.userLIstOptions = response.data.items.map(v => ({
                    key: v.name
                }));
            })
        }
    }
}
</script>
<style rel="stylesheet/scss" lang="scss" scoped>
@import "src/styles/mixin.scss";
.title-prompt {
    position: absolute;
    right: 0;
    font-size: 12px;
    top: 10px;
    color: #ff4949;
}
.createPost-container {
    position: relative;
    .createPost-main-container {
        padding: 40px 45px 20px 50px;
        .postInfo-container {
            position: relative;
            @include clearfix;
            margin-bottom: 10px;
            .postInfo-container-item {
                float: left;
            }
        }
        .editor-container {
            min-height: 500px;
            margin: 0 0 30px;
            .editor-upload-btn-container {
                text-align: right;
                margin-right: 10px;
                .editor-upload-btn {
                    display: inline-block;
                }
            }
        }
    }
    .word-counter {
        width: 40px;
        position: absolute;
        right: -10px;
        top: 0;
    }
}
</style>
