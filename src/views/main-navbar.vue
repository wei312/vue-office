<template>
  <nav class="site-navbar" :class="'site-navbar--' + navbarLayoutType">
    <div class="site-navbar__header">
      <h1 class="site-navbar__brand" @click="$router.push({ name: 'home' })">
        <a class="site-navbar__brand-lg" href="javascript:;">运营后台管理</a>
        <a class="site-navbar__brand-mini" href="javascript:;">后台</a>
      </h1>
    </div>
    <div class="site-navbar__body clearfix" >
      <template v-for="menuItem in sideBarConfig">
      <el-menu
        :key="menuItem.path"
        :mode="mode"
        :theme="theme"
        class="site-navbar__menu">
        
        
        <el-menu-item :key="menuItem.key"  >
        <template>
          <span @click="menuItem.path">{{menuItem.name}}</span>
        </template>              
        </el-menu-item> 
        </el-menu>
        </template>
      
      <el-menu
        class="site-navbar__menu site-navbar__menu--right"
        mode="horizontal">
        <el-menu-item index="1" @click="$router.push({ name: 'theme' })">
          <template slot="title">
            <el-badge value="new">
              <icon-svg name="shezhi" class="el-icon-setting"></icon-svg>
            </el-badge>
          </template>
        </el-menu-item>
       
        <el-menu-item class="site-navbar__avatar" index="1">
          <el-dropdown :show-timeout="0" placement="bottom">
            <span class="el-dropdown-link">
              <img src="~@/assets/img/avatar.png" :alt="userName">{{ userName }}
            </span>
            <el-dropdown-menu slot="dropdown">
              <el-dropdown-item @click.native="updatePasswordHandle()">修改密码</el-dropdown-item>
              <el-dropdown-item @click.native="logoutHandle()">退出</el-dropdown-item>
            </el-dropdown-menu>
          </el-dropdown>
        </el-menu-item>
      </el-menu>
    </div>
    <!-- 弹窗, 修改密码 -->
    <update-password v-if="updatePassowrdVisible" ref="updatePassowrd"></update-password>
  </nav>
</template>
<style>
.site-navbar{
  background:#001529e7;
}
</style>
<script>
  import UpdatePassword from './main-navbar-update-password'
  import { clearLoginInfo } from '@/utils'
  export default {
    name: 'HnisiSideBar',
    props: {
      mode: {
        type: String,
        default: 'inline'
      },
      theme: {
        type: String,
        default: 'light'
      },
      className: {
        type: String,
        default: ''
      }
    },
    data () {
      return {
        updatePassowrdVisible: false,
        defaultOpenKeys: [],
        sideBarConfig:[],
        isSideBarCollapse:false
                
      }
    },
    components: {
      UpdatePassword
    },
    computed: {
      navbarLayoutType: {
        get () { return this.$store.state.common.navbarLayoutType }
      },
      sidebarFold: {
        get () { return this.$store.state.common.sidebarFold },
        set (val) { this.$store.commit('common/updateSidebarFold', val) }
      },
      mainTabs: {
        get () { return this.$store.state.common.mainTabs },
        set (val) { this.$store.commit('common/updateMainTabs', val) }
      },
      userName: {
        get () { return this.$store.state.user.name }
      },
      defaultSelectedKeys() {
        return this.setDefaultSelectedKeys(this.$route) || []
      }
    },
    mounted() {
      this.setSideBarConfig()
    },
    methods: {
      toggleSideBar() {
        //this.$store.dispatch(this.$storeGlobalTypes.TOGGLE_SIDE_BAR)
      },
      setSideBarConfig(){
        const menus = [
            {
              iconClassName: 'appstore',
              name: '个人中心',
              path:'/'
            },
            {
              iconClassName: 'appstore',
              name: '服务管理',
              path:'/'            
            },
            {
              iconClassName: 'appstore',
              name: '实施管理',
              path:'/'      
            },
            {
              iconClassName: 'appstore',
              name: '系统管理',
              path:'/'
              
            },
            {
              iconClassName: 'appstore',
              name: '门户管理',
              path:'/'
              
            },
            {
              iconClassName: 'appstore',
              name: '需求管理',
              path:'/'
              
            }
          ]
          this.sideBarConfig=menus;
        },
      setDefaultSelectedKeys(route) {
        const name = route.path.substring(1)
        if (Object.keys(route.params) && !this.isSideBarCollapse) {
          this.setDefaultOpenKeys(name)
        }
      },
      setDefaultOpenKeys(path) {
        let split = path.split('/')
        // 去掉最后一位待处理 ['a','b','c','d'] => ['a','b','c']
        split = split.slice(0, -1)
        // ['a', 'b', 'c'] => ['a', 'a/b', 'a/b/c']
        const newArr = split.map((item, index, array) =>
          array.slice(0, index + 1).join('/')
        )

        this.$set(this, 'defaultOpenKeys', newArr)
      },
      onItemClick({ item, key, keyPath }) {
        if (this.defaultSelectedKeys[0] === key) {
          return
        }
        // 需要传递出去的事件：选择后触发
        this.$emit('sidebar-selected', item, () => {
          if (this.$router) {
            this.$router.push(`/${key}`)
          }
        })
      },
      // 修改密码
      updatePasswordHandle () {
        this.updatePassowrdVisible = true
        this.$nextTick(() => {
          this.$refs.updatePassowrd.init()
        })
      },
      // 退出
      logoutHandle () {
        this.$confirm(`确定进行[退出]操作?`, '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$http({
            url: this.$http.adornUrl('/sys/logout'),
            method: 'post',
            data: this.$http.adornData()
          }).then(({data}) => {
            if (data && data.code === 0) {
              clearLoginInfo()
              this.$router.push({ name: 'login' })
            }
          })
        }).catch(() => {})
      }
    }
  }
</script>

<style lang="scss">
/*.icon {*/
/*  margin-right: 10px;*/
/*  font-size: 14px;*/
/*}*/
.ant-menu-inline-collapsed /deep/ .sidebarMenuName {
  max-width: 0;
  display: inline-block;
  opacity: 0;
}
.sidebarContainer {
  position: fixed;
  padding-bottom: 50px;
  height: 100%;
  border-right: 1px solid #ccd2d8;
  .ant-menu-inline {
    height: 100%;
    overflow: hidden visible;
    border: 0;
  }
  .ant-menu-inline .ant-menu-item,
  .ant-menu-inline .ant-menu-submenu-title {
    width: 100%;
  }
  .sideToggleButton {
    width: 100%;
    position: absolute;
    bottom: 0;
    padding: 10px;
    i.anticon {
      cursor: pointer;
    }
  }
}
</style>
