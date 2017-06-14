<template>
    <Menu mode="vertical" theme="dark" :active-name="$route.path">
        <sidebar-item :routes='permission_routers'></sidebar-item>
    </Menu>
</template>

<script>
    import { mapGetters } from 'vuex';
    import SidebarItem from './SidebarItem';
    export default {
      components: { SidebarItem },
      computed: {
        ...mapGetters([
          'permission_routers'
        ])
      }
  }
</script>
<style rel="stylesheet/scss" lang="scss" scoped>
    .ivu-menu {
        min-height: 100%;
        width: 100% !important;
        background-color: #324157;
    }
</style>
