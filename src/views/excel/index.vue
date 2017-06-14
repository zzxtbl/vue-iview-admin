<template>
    <div class="app-container">
        <Table :columns="columns" :data="list" border></Table>
        <br>
        <Button type="primary" icon="document" @click="handleDownload">导出excel</Button>
    </div>
</template>
<script>
import { getList } from 'api/article';
export default {
    data() {
        return {
            columns: [
                {
                    title: 'ID',
                    key: 'id'
                },
                {
                    title: '文章标题',
                    key: 'title'
                },
                {
                    title: '作者',
                    key: 'author'
                },
                {
                    title: '阅读数',
                    key: 'pageviews'
                },
                {
                    title: '发布时间',
                    key: 'display_time'
                },
            ],
            list: this.fetchData()
        }
    },
    methods: {
        fetchData() {
            getList(this.listQuery).then(response => {
                this.list = response.data;
                return response.data;
            })
        },
        handleDownload() {
            require.ensure([], () => {
                const { export_json_to_excel } = require('vendor/Export2Excel');
                const tHeader = ['序号', '文章标题', '作者', '阅读数', '发布时间'];
                const filterVal = ['id', 'title', 'author', 'pageviews', 'display_time'];
                const list = this.list;
                const data = this.formatJson(filterVal, list);
                export_json_to_excel(tHeader, data, '列表excel');
            })
        },
        formatJson(filterVal, jsonData) {
            return jsonData.map(v => filterVal.map(j => v[j]))
        }
    }
};
</script>
