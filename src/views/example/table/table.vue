<template>
<div class="app-container calendar-list-container">
    <div class="filter-container">
        <Input @keyup.enter.native="handleFilter" style="width: 200px;" class="filter-item" placeholder="标题" v-model="listQuery.title"></Input>

        <Select clearable style="width: 90px" class="filter-item" v-model="listQuery.importance" placeholder="重要性">
            <Option v-for="item in importanceOptions" :key="item" :label="item" :value="item"></Option>
        </Select>

        <Select clearable class="filter-item" style="width: 130px" v-model="listQuery.type" placeholder="类型">
            <Option v-for="item in  calendarTypeOptions" :key="item.key" :label="item.display_name+'('+item.key+')'" :value="item.key">
            </Option>
        </Select>

        <Select @change='handleFilter' style="width: 120px" class="filter-item" v-model="listQuery.sort" placeholder="排序">
            <Option v-for="item in sortOptions" :key="item.key" :label="item.label" :value="item.key">
            </Option>
        </Select>

        <Button class="filter-item" type="primary" v-waves icon="search" @click="handleFilter">搜索</Button>
        <Button class="filter-item" style="margin-left: 10px;" @click="handleCreate" type="primary" icon="edit">添加</Button>
        <Button class="filter-item" type="primary" icon="document" @click="handleDownload">导出</Button>
        <Checkbox class="filter-item" @change='tableKey=tableKey+1' v-model="showAuditor">显示审核人</Checkbox>
    </div>

    <Table :key='tableKey' :columns="columns" :data="list" :listQuery="listQuery" border></Table>
    <div v-show="!listLoading" class="pagination-container">
        <Page :total="total" :current="listQuery.page" :page-size="listQuery.limit" @on-change="changePage" @on-page-size-change="changePageNums" show-total show-sizer show-elevator></Page>
    </div>

    <Modal :title="textMap[dialogStatus]" v-model="dialogFormVisible">
        <Form class="small-space" :model="temp" label-position="left" :label-width="70" style='width: 400px; margin-left:50px;'>
            <Form-item label="类型">
                <Select class="filter-item" v-model="temp.type" placeholder="请选择">
                    <Option v-for="item in  calendarTypeOptions" :key="item.key" :label="item.display_name" :value="item.key">
                    </Option>
                </Select>
            </Form-item>

            <Form-item label="状态">
                <Select class="filter-item" v-model="temp.status" placeholder="请选择">
                    <Option v-for="item in  statusOptions" :key="item" :label="item" :value="item">
                    </Option>
                </Select>
            </Form-item>

            <Form-item label="时间">
                <Date-picker v-model="temp.timestamp" type="datetime" placeholder="选择日期时间">
                </Date-picker>
            </Form-item>

            <Form-item label="标题">
                <Input v-model="temp.title"></Input>
            </Form-item>

            <Form-item label="重要性">
                <Rate style="margin-top:8px;" v-model="temp.importance" :colors="['#99A9BF', '#F7BA2A', '#FF9900']"></Rate>
            </Form-item>

            <Form-item label="点评">
                <Input type="textarea" :autosize="{ minRows: 2, maxRows: 4}" placeholder="请输入内容" v-model="temp.remark">
                </Input>
            </Form-item>
        </Form>
        <div slot="footer" class="dialog-footer">
            <Button @click="dialogFormVisible = false">取 消</Button>
            <Button v-if="dialogStatus=='create'" type="primary" @click="create">确 定</Button>
            <Button v-else type="primary" @click="update">确 定</Button>
        </div>
    </Modal>

    <Modal title="阅读数统计" v-model="dialogPvVisible" size="small">
        <Table :columns="pvColumns" :data="pvData" border style="width: 100%">
        </Table>
        <span slot="footer" class="dialog-footer">
        <Button type="primary" @click="dialogPvVisible = false">确 定</Button>
      </span>
    </Modal>

</div>
</template>

<script>
import { fetchList, fetchPv } from 'api/article_table';
import { parseTime } from 'utils';

const calendarTypeOptions = [{
        key: 'FD',
        display_name: '经济数据'
    },
    {
        key: 'FE',
        display_name: '财经大事'
    },
    {
        key: 'BI',
        display_name: '国债发行'
    },
    {
        key: 'VN',
        display_name: '假期报告'
    }
];

// arr to obj
const calendarTypeKeyValue = calendarTypeOptions.reduce((acc, cur) => {
    acc[cur.key] = cur.display_name;
    return acc
}, {});

export default {
    name: 'table_demo',
    data() {
        return {
            list: [],
            pvColumns: [{
                    title: '渠道',
                    key: 'key'
                },
                {
                    title: 'PV',
                    key: 'pv'
                }
            ],
            total: null,
            listLoading: true,
            listQuery: {
                page: 1,
                limit: 20,
                importance: undefined,
                title: undefined,
                type: undefined,
                sort: '+id'
            },
            temp: {
                id: undefined,
                importance: 0,
                remark: '',
                timestamp: 0,
                title: '',
                type: '',
                status: 'published'
            },
            importanceOptions: [1, 2, 3],
            calendarTypeOptions,
            sortOptions: [{
                label: '按ID升序列',
                key: '+id'
            }, {
                label: '按ID降序',
                key: '-id'
            }],
            statusOptions: ['published', 'draft', 'deleted'],
            dialogFormVisible: false,
            dialogStatus: '',
            textMap: {
                update: '编辑',
                create: '创建'
            },
            dialogPvVisible: false,
            pvData: [],
            showAuditor: false,
            tableKey: 0
        }
    },
    created() {
        this.getList()
    },
    computed: {
        columns() {
            let column = [
                {
                    title: '序号',
                    key: 'id',
                    width: 65
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
                    key: 'title',
                    render: (h, params) => {
                        const row = params.row;
                        return h('div', [
                            h('span', {
                                on: {
                                    click: () => {
                                        this.handleUpdate(row);
                                    }
                                }
                            }, row.title),
                            h('Tag', {

                            }, this.typeFilter(row.type))
                        ])
                    }
                },
                {
                    title: '作者',
                    key: 'author'
                }];
            if(this.showAuditor) {
                column.push({
                    title: '审核人',
                    key: 'auditor'
                });
            }
            column.push({
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
            });
            column.push({
                title: '阅读数',
                key: 'pageviews',
                width: 95,
                render: (h, params) => {
                    const row = params.row;
                    return h('span', {
                        on: {
                            click: () => {
                                this.handleFetchPv(row.pageviews);
                            }
                        }
                    }, row.pageviews)
                }
            });
            column.push({
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
            });
            column.push({
                title: '操作',
                key: 'action',
                render: (h, params) => {
                    const row = params.row;
                    let f, d, p;
                    if(row.status!='published') {
                        p = h('Button', {
                            props: {
                                type: 'success',
                                size: 'small'
                            },
                            on: {
                                click: () => {
                                    this.handleModifyStatus(row, 'published')
                                }
                            }
                        }, '发布');
                    }
                    if(row.status!='draft') {
                        f = h('Button', {
                            props: {
                                type: 'ghost',
                                size: 'small'
                            },
                            on: {
                                click: () => {
                                    this.handleModifyStatus(row,'draft')
                                }
                            }
                        }, '草稿');
                    }
                    if(row.status!='deleted') {
                        d = h('Button', {
                            props: {
                                type: 'error',
                                size: 'small'
                            },
                            on: {
                                click: () => {
                                    this.handleModifyStatus(row,'deleted')
                                }
                            }
                        }, '删除');
                    }
                    return h('div', [
                        p, f, d
                    ]);
                }
            });

            return column;
        }
    },
    methods: {
        getList() {
            this.listLoading = true;
            fetchList(this.listQuery).then(response => {
                this.list = response.data.items;
                this.total = response.data.total;
                this.listLoading = false;
            })
        },
        changePage(val) {
            this.listQuery.page = val;
            this.getList();
        },
        changePageNums(val) {
            this.listQuery.limit = val;
            this.getList();
        },
        handleFilter() {
            this.getList();
        },
        typeFilter(type) {
            return calendarTypeKeyValue[type]
        },
        timeFilter(time) {
            if (!time[0]) {
                this.listQuery.start = undefined;
                this.listQuery.end = undefined;
                return;
            }
            this.listQuery.start = parseInt(+time[0] / 1000);
            this.listQuery.end = parseInt((+time[1] + 3600 * 1000 * 24) / 1000);
        },
        handleModifyStatus(row, status) {
            this.$Message.success('操作成功');
            row.status = status;
        },
        handleCreate() {
            this.resetTemp();
            this.dialogStatus = 'create';
            this.dialogFormVisible = true;
        },
        handleUpdate(row) {
            this.temp = Object.assign({}, row);
            this.dialogStatus = 'update';
            this.dialogFormVisible = true;
        },
        handleDelete(row) {
            this.$Notice.success({
                title: '成功',
                desc: '删除成功',
                duration: 2000
            });
            const index = this.list.indexOf(row);
            this.list.splice(index, 1);
        },
        create() {
            this.temp.id = parseInt(Math.random() * 100) + 1024;
            this.temp.timestamp = +new Date();
            this.temp.author = '原创作者';
            this.list.unshift(this.temp);
            this.dialogFormVisible = false;
            this.$Notice.success({
                title: '成功',
                desc: '创建成功',
                duration: 2000
            });
        },
        update() {
            this.temp.timestamp = +this.temp.timestamp;
            for (const v of this.list) {
                if (v.id === this.temp.id) {
                    const index = this.list.indexOf(v);
                    this.list.splice(index, 1, this.temp);
                    break;
                }
            }
            this.dialogFormVisible = false;
            this.$Notice.success({
                title: '成功',
                desc: '更新成功',
                duration: 2000
            });
        },
        resetTemp() {
            this.temp = {
                id: undefined,
                importance: 0,
                remark: '',
                timestamp: 0,
                title: '',
                status: 'published',
                type: ''
            };
        },
        handleFetchPv(pv) {
            fetchPv(pv).then(response => {
                this.pvData = response.data.pvData;
                this.dialogPvVisible = true;
                return this.pvData;
            })
        },
        handleDownload() {
            require.ensure([], () => {
                const { export_json_to_excel } = require('vendor/Export2Excel');
                const tHeader = ['时间', '地区', '类型', '标题', '重要性'];
                const filterVal = ['timestamp', 'province', 'type', 'title', 'importance'];
                const data = this.formatJson(filterVal, this.list);
                export_json_to_excel(tHeader, data, 'table数据');
            })
        },
        formatJson(filterVal, jsonData) {
            return jsonData.map(v => filterVal.map(j => {
                if (j === 'timestamp') {
                    return parseTime(v[j])
                } else {
                    return v[j]
                }
            }))
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
