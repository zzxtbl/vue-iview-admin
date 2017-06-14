<template>
<div class="app-container calendar-list-container">
    <Table :columns="columns" :data="list" border></Table>
</div>
</template>

<script>
import { fetchList } from 'api/article_table';

export default {
    name: 'inline_edit-table_demo',
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
                {
                    title: '标题',
                    key: 'title',
                    render: (h, params) => {
                        const row = params.row;
                        const self = this;
                        return h('div', [
                            h('Input', {
                                props: {
                                    value: row.title
                                },
                                on: {
                                    input: function (event) {
                                        row.title = event;
                                    }
                                },
                                style: {
                                    display: row.edit ? 'block' : 'none'
                                }
                            }),
                            h('span', {
                                style: {
                                    display: row.edit ? 'none' : 'block'
                                }
                            }, row.title)
                        ]);
                    }
                },
                {
                    title: '操作',
                    key: 'action',
                    render: (h, params) => {
                        const row = params.row;
                        return h('div', [
                            h('Button', {
                                props: {
                                    type: 'primary',
                                    size: 'small'
                                },
                                on: {
                                    click: () => {
                                        row.edit = true;
                                    }
                                }
                            }, '编辑'),
                            h('Button', {
                                props: {
                                    type: 'success',
                                    size: 'small'
                                },
                                on: {
                                    click: () => {
                                        row.edit = false;
                                    }
                                }
                            }, '完成')
                        ]);
                    }
                }
            ],
            list: [],
            listLoading: true,
            listQuery: {
                page: 1,
                limit: 10
            }
        }
    },
    created() {
        this.getList()
    },
    methods: {
        getList() {
            this.listLoading = true;
            fetchList(this.listQuery).then(response => {
                this.list = response.data.items.map(v => {
                    v.edit = false;
                    return v
                });
                this.listLoading = false;
            })
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
