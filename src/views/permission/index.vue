<template>
    <div class="app-container">
        <div style='margin-bottom:15px;'>你的权限： {{roles}}</div>
        切换权限：
        <Radio-group v-model="role" type="button">
            <Radio label="editor"></Radio>
        </Radio-group>
    </div>
</template>

<script>
    import { mapGetters } from 'vuex';
    export default{
      data() {
        return {
          role: ''
        }
      },
      computed: {
        ...mapGetters([
          'roles'
        ])
      },
      watch: {
        role(val) {
          this.$store.dispatch('ChangeRole', val).then(() => {
            this.$router.push({ path: '/permission/index?' + +new Date() });
          })
        }
      }
    }
</script>
