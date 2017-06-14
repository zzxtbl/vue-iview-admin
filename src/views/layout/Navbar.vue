<template>
    <Menu class="navbar" mode="horizontal">
        <Hamburger class="hamburger-container" :toggleClick="toggleSideBar" :isActive="sidebar.opened"></Hamburger>
        <levelbar></levelbar>
        <ErrLog v-if="log.length>0" class="errLog-container" :logsList="log"></ErrLog>
        <Dropdown class="avatar-container" trigger="click" placement="bottom-end">
            <div class="avatar-wrapper">
                <img class="user-avatar" :src="avatar+'?imageView2/1/w/80/h/80'">
                <Icon type="arrow-down-b"/>
            </div>
            <Dropdown-menu class="user-dropdown" slot="list">
                <router-link  class='inlineBlock' to="/">
                    <Dropdown-item>首页</Dropdown-item>
                </router-link>
                <router-link  class='inlineBlock' to="/admin/profile">
                    <Dropdown-item>设置</Dropdown-item>
                </router-link>
                <Dropdown-item divided><span @click="logout" style="display:block;">退出登录</span></Dropdown-item>
            </Dropdown-menu>
        </Dropdown>
    </Menu>
</template>

<script>
    import { mapGetters } from 'vuex';
    import Levelbar from './Levelbar';
    import Hamburger from 'components/Hamburger';
    import ErrLog from 'components/ErrLog';
    import errLogStore from 'store/errLog';

    export default {
      components: {
        Levelbar,
        Hamburger,
        ErrLog
      },
      data() {
        return {
          log: errLogStore.state.errLog
        }
      },
      computed: {
        ...mapGetters([
          'sidebar',
          'name',
          'avatar'
        ])
      },
      methods: {
        toggleSideBar() {
          this.$store.dispatch('ToggleSideBar')
        },
        logout() {
          this.$store.dispatch('LogOut').then(() => {
            location.reload();// 为了重新实例化vue-router对象 避免bug
          });
        }
      }
    }
</script>
<style rel="stylesheet/scss" lang="scss" scoped>
    .navbar {
        height: 50px;
        line-height: 50px;
        border-radius: 0px !important;
        background: #eef1f6;
        .hamburger-container {
            line-height: 58px;
            height: 50px;
            float: left;
            padding: 0 10px;
        }
        .errLog-container {
            display: inline-block;
            position: absolute;
            right: 150px;
        }
        .avatar-container {
            height: 50px;
            display: inline-block;
            position: absolute;
            right: 35px;
            top: 0;
            .avatar-wrapper {
                cursor: pointer;
                margin-top:5px;
                position: relative;
                height: 40px;
                .user-avatar {
                    width: 40px;
                    height: 40px;
                    border-radius: 10px;
                }
                .ivu-icon-arrow-down-b {
                    position: absolute;
                    right: -15px;
                    top: 25px;
                }
            }
        }
    }
</style>



