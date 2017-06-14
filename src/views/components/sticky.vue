<template>
<div>
    <Sticky className="sub-navbar">
        <Dropdown trigger="click">
            <Button>
                平台
                <Icon type="arrow-down-b"></Icon>
            </Button>
            <Dropdown-menu class="no-border" slot="list">
                <Checkbox-group v-model="platforms" style="padding: 5px 15px;">
                    <Checkbox v-for="item in platformsOptions" :label="item.key" :key="item.key">
                        {{item.name}}
                    </Checkbox>
                </Checkbox-group>
            </Dropdown-menu>
        </Dropdown>

        <Dropdown trigger="click">
            <Button>
                外链
                <Icon type="arrow-down-b"></Icon>
            </Button>
            <Dropdown-menu class="no-padding no-border" style="width:300px" slot="list">
                <Input placeholder="请输入内容" v-model="url">
                    <template slot="prepend">填写url</template>
                </Input>
            </Dropdown-menu>
        </Dropdown>

        <div class="time-container">
            <Date-picker type="datetime" placeholder="发布时间" style="width: 200px"></Date-picker>
        </div>

        <Button style="margin-left: 10px;" type="success">发布</Button>
    </Sticky>

    <div class="components-container">
        <code>Sticky header 当页面滚动到预设的位置会吸附在顶部</code>
        <div>我是占位</div>
        <div>我是占位</div>
        <div>我是占位</div>
        <div>我是占位</div>
        <div>我是占位</div>
        <div>我是占位</div>
        <div>我是占位</div>
        <div>我是占位</div>
        <div>我是占位</div>
        <div>我是占位</div>
        <div>我是占位</div>
        <div>我是占位</div>
        <div>我是占位</div>
        <div>我是占位</div>
        <div>我是占位</div>
        <div>我是占位</div>
        <div>我是占位</div>
        <div>我是占位</div>
        <div>我是占位</div>
        <div>我是占位</div>
        <div>我是占位</div>
        <div>我是占位</div>
        <div>我是占位</div>
        <div>我是占位</div>
        <div>我是占位</div>
        <div>我是占位</div>
        <div>我是占位</div>
        <div>我是占位</div>
        <div>我是占位</div>
        <div>我是占位</div>
        <div>我是占位</div>
        <div>我是占位</div>
        <div>我是占位</div>
        <div>我是占位</div>
        <div>我是占位</div>
        <div>我是占位</div>
        <div>我是占位</div>
        <div>我是占位</div>
        <div>我是占位</div>
        <div>我是占位</div>
        <div>我是占位</div>
        <div>我是占位</div>
        <div>我是占位</div>
        <div>我是占位</div>
        <div>我是占位</div>
        <div>我是占位</div>
        <div>我是占位</div>
        <div>我是占位</div>
        <div>我是占位</div>
        <div>我是占位</div>
        <div>我是占位</div>
        <div>我是占位</div>
    </div>
</div>
</template>
<script>
import Sticky from 'components/Sticky';

export default {
    components: {
        Sticky
    },
    data() {
        return {
            time: '',
            url: '',
            platforms: ['a-platform'],
            platformsOptions: [{
                    key: 'a-platform',
                    name: '平台A'
                },
                {
                    key: 'b-platform',
                    name: '平台B'
                },
                {
                    key: 'c-platform',
                    name: '平台C'
                }
            ],
            pickerOptions: {
                disabledDate(time) {
                    return time.getTime() > Date.now();
                }
            }
        }
    }

};
</script>
<style scoped>
.time-container {
    display: inline-block;
}
</style>
