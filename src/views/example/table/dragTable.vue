<template>
<div class="app-container calendar-list-container">
    <Table :columns="columns" :data="list" border></Table>
    <div class='show-d'>默认顺序 &nbsp; {{ olderList}}</div>
    <div class='show-d'>拖拽后顺序{{newList}}</div>
</div>
</template>

<script>
import { fetchList } from 'api/article_table';
import Sortable from 'sortablejs'

export default {
    name: 'drag-table_demo',
    data() {
        return {
            columns: [{
                    title: '序号',
                    key: 'id'
                },
                {
                    title: '时间',
                    key: 'timestamp',
                    render: (h, params) => {
                         return h('div', this.formatDate(this.list[params.index].timestamp, 'yyyy-MM-dd hh:mm'));
                    }
                },
                {
                    title: '标题',
                    key: 'title'
                },
                {
                    title: '作者',
                    key: 'author'
                },
                {
                    title: '重要性',
                    key: 'importance',
                    render: (h, params) => {
                        return h('Rate', {
                            props: {
                                value: this.list[params.index].importance,
                                count: this.list[params.index].importance,
                                disabled: true
                            }
                        });
                    }
                },
                {
                    title: '阅读数',
                    key: 'pageviews'
                },
                {
                    title: '状态',
                    key: 'status',
                    render: (h, params) => {
                        const row = params.row;
                        const color = row.status === 'deleted' ? 'red' : row.status === 'published' ? 'green' : 'blue';
                        return h('Tag', {
                            props: {
                                type: 'dot',
                                color: color
                            }
                        }, row.status);
                    }
                },
            ],
            list: [],
            total: null,
            listQuery: {
                page: 1,
                limit: 10
            },
            sortable: null,
            olderList: [],
            newList: []
        }
    },
    created() {
        this.getList()
    },
    methods: {
        getList() {
            fetchList(this.listQuery).then(response => {
                this.list = response.data.items;
                this.total = response.data.total;
                this.olderList = this.list.map(v => v.id);
                this.newList = this.olderList.slice();
                this.$nextTick(() => {
                    this.setSort()
                })
            })
        },
        setSort() {
            const el = document.querySelectorAll('.ivu-table-body > table > tbody')[0];
            this.sortable = Sortable.create(el, {
                // handle: '.drag-handler',
                onEnd: evt => {
                    const tempIndex = this.newList.splice(evt.oldIndex, 1)[0];
                    this.newList.splice(evt.newIndex, 0, tempIndex);
                }
            });
        },
        formatDate (date, fmt) {
            date = new Date(date);
            if (/(y+)/.test(fmt)) {
                fmt = fmt.replace(RegExp.$1, (date.getFullYear() + '').substr(4 - RegExp.$1.length));
            }
            let o = {
                'M+': date.getMonth() + 1,
                'd+': date.getDate(),
                'h+': date.getHours(),
                'm+': date.getMinutes(),
                's+': date.getSeconds()
            };
            for (let k in o) {
                if (new RegExp(`(${k})`).test(fmt)) {
                    let str = o[k] + '';
                    fmt = fmt.replace(RegExp.$1, (RegExp.$1.length === 1) ? str : (("00" + str).substr(("" + str).length)));
                }
            }
            return fmt;
        }
    }
}
</script>

<style >
.drag-handler {
    width: 30px;
    height: 30px;
    cursor: pointer;
}

.show-d {
    margin-top: 15px;
}
</style>
