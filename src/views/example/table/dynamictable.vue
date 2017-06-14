<template>
    <div class="app-container">
        <div class="filter-container">
            <Checkbox-group v-model="formThead" @on-change="changeTableColumns">
                <Checkbox label="apple">apple</Checkbox>
                <Checkbox label="banana">banana</Checkbox>
                <Checkbox label="orange">orange</Checkbox>
            </Checkbox-group>
        </div>
        <Table :columns="columns" :data="tableData" style="width: 100%"></Table>
    </div>
</template>
<script>
export default {
    data() {
        return {
            columns: [],
            tableData: [{
                name: '水果',
                apple: 10,
                banana: 20,
                orange: 20
            }, {
                name: '水果',
                apple: 40,
                banana: 50,
                orange: 60
            }],
            formThead: ['apple', 'banana']
        }
    },
    methods: {
        getTableColumns () {
            const tableColumnList = {
                name: {
                    title: '名称',
                    key: 'name'
                },
                apple: {
                    title: 'apple',
                    key: 'apple',
                    sortable: true
                },
                banana: {
                    title: 'banana',
                    key: 'banana',
                    sortable: true
                },
                orange: {
                    title: 'orange',
                    key: 'orange',
                    sortable: true
                }
            };
            let data = [tableColumnList.name];
            this.formThead.forEach(col => data.push(tableColumnList[col]));
            return data;
        },
        changeTableColumns () {
            this.columns = this.getTableColumns();
        },
        toggleFav (index) {
            this.tableData[index].fav = this.tableData[index].fav === 0 ? 1 : 0;
        }
    },
    mounted () {
        this.changeTableColumns();
    }
};
</script>
