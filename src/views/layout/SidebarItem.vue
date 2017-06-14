<template>
    <div>
        <template v-for="item in routes">
            <router-link v-if="!item.hidden&&item.noDropdown&&item.children.length>0" :to="item.path+'/'+item.children[0].path">
                <Menu-item :name="item.path+'/'+item.children[0].path">
                    <wscn-icon-svg v-if='item.icon' :icon-class="item.icon" /> {{item.children[0].name}}
                </Menu-item>
            </router-link>
            <Submenu :name="item.name" v-if="!item.noDropdown&&!item.hidden">
                <template slot="title">
                    <wscn-icon-svg v-if='item.icon' :icon-class="item.icon" /> {{item.name}}
                </template>
                <template v-for="child in item.children" v-if='!child.hidden'>
                    <sidebar-item class='menu-indent' v-if='child.children&&child.children.length>0' :routes='[child]'> </sidebar-item>
                    <router-link v-else class="menu-indent" :to="item.path+'/'+child.path">
                        <Menu-item :name="item.path+'/'+child.path">
                            {{child.name}}
                        </Menu-item>
                    </router-link>
                </template>
            </Submenu>
        </template>
    </div>
</template>

<script>

    export default {
      name: 'SidebarItem',
      props: {
        routes: {
          type: Array
        }
      }
    }
</script>
<style rel="stylesheet/scss" lang="scss">
    .wscn-icon {
        margin-right: 10px;
    }
    .hideSidebar .menu-indent{
        display: block;
        text-indent: 10px;
    }
    .ivu-menu-dark.ivu-menu-vertical .ivu-menu-item,
    .ivu-menu-dark.ivu-menu-vertical .ivu-menu-submenu-title {
        color: #BFCBD9;
    }
    .ivu-menu-submenu-title span>i, .ivu-menu-submenu-title>i {
        margin-right: 0;
    }
    .ivu-menu-dark.ivu-menu-vertical .ivu-menu-opened,
    .ivu-menu-dark.ivu-menu-vertical .ivu-menu-submenu .ivu-menu-item-active,
    .ivu-menu-dark.ivu-menu-vertical .ivu-menu-submenu .ivu-menu-item-active:hover {
        background: #1f2d3d!important;
    }
    .ivu-menu-dark.ivu-menu-vertical .ivu-menu-item-active:not(.ivu-menu-submenu),
    .ivu-menu-dark.ivu-menu-vertical .ivu-menu-submenu-title-active:not(.ivu-menu-submenu) {
        color: #2d8cf0;
        border-right: 2px solid #2d8cf0;
    }
    .ivu-menu-item-active .ivu-menu {
        display: block!important;
    }
</style>
