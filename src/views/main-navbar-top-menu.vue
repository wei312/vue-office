<template>
  <div class="sidebarContainer">
    <a-menu
      :mode="mode"
      :theme="theme"
      :default-selected-keys="defaultSelectedKeys"
      :default-open-keys="defaultOpenKeys"
      :class="[className]"
    >
      <template v-for="menuItem in sideBarConfig">
        <a-menu-item
          v-if="!menuItem.children"
          :key="menuItem.path"
          @click="onItemClick"
        >
          <template v-if="menuItem.iconClassName.match('gsd-icon')">
            <i :class="[menuItem.iconClassName]"></i>
          </template>
          <template v-else>
            <a-icon :type="menuItem.iconClassName" />
          </template>
          <span v-if="!isSideBarCollapse" style="margin-left: 10px">{{
            menuItem.name
          }}</span>
        </a-menu-item>
        <a-sub-menu v-else :key="menuItem.path">
          <span slot="title">
            <template v-if="menuItem.iconClassName.match('gsd-icon')">
              <i :class="[menuItem.iconClassName]"></i>
            </template>
            <template v-else>
              <a-icon :type="menuItem.iconClassName" />
            </template>
            <span v-if="!isSideBarCollapse" style="margin-left: 10px">{{
              menuItem.name
            }}</span>
          </span>
          <template v-for="menuSubItem in menuItem.children">
            <a-menu-item
              v-if="!menuSubItem.children"
              :key="menuSubItem.path"
              @click="onItemClick"
            >
              {{ menuSubItem.name }}
            </a-menu-item>
            <a-sub-menu
              v-else
              :key="menuSubItem.path"
              :title="menuSubItem.name"
            >
              <a-menu-item
                v-for="menuSubInnerItem in menuSubItem.children"
                :key="menuSubInnerItem.path"
                @click="onItemClick"
                >{{ menuSubInnerItem.name }}</a-menu-item
              >
            </a-sub-menu>
          </template>
        </a-sub-menu>
      </template>
    </a-menu>
    <div
      class="sideToggleButton"
      :style="{ 'text-align': isSideBarCollapse ? 'center' : 'right' }"
    >
      <a-icon
        :type="isSideBarCollapse ? 'menu-unfold' : 'menu-fold'"
        @click="toggleSideBar"
      ></a-icon>
    </div>
  </div>
</template>
<script>
import { mapState } from 'vuex'
import $ from 'jquery'
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

  data() {
    return {
      // defaultActive: ''
      defaultOpenKeys: []
    }
  },

  computed: {
      isSideBarCollapse: this.$store.isSideBarCollapse,
        sideBarConfig: this.$store.sideBarConfig, 
      defaultSelectedKeys() {
        return this.setDefaultSelectedKeys(this.$route) || []
      }
  },

  watch: {},

  mounted() {
    this.initheight(),
    this.setSideBarConfig()
  },
  methods: {
    init(){

    },
    toggleSideBar() {
      //this.$store.dispatch(this.$storeGlobalTypes.TOGGLE_SIDE_BAR)
    },
    setSideBarConfig(){
    const menus = [
        {
          iconClassName: 'appstore',
          name: '刷新资源',
          children: []
        },
        {
          iconClassName: 'appstore',
          name: '门户内容运营',
          children: [
            {
              path: 'admin/home/index',
              name: '功能入口管理'
            },
            {
              path: 'admin/news/index',
              name: '新闻政策管理'
            },
            {
              name: '联系方式管理',
              path: 'admin/contact'
            },
            {
              name: '帮助中心管理',
              path: 'admin/help'
            }
          ]
        },
        {
          iconClassName: 'appstore',
          name: '资源目录管理',
          children: [
            {
              name: '电子证照目录',
              path: 'cata/elecCert'
            },
            {
              name: '实体资源列表',
              path: 'cata/entity_list'
            },
            {
              name: '目录概览',
              path: 'cata/catalog'
            },
            {
              name: '类别标签管理',
              path: 'cata/cateLab'
            },
            {
              name: '业务事项管理',
              path: 'cata/business_list'
            },
            {
              name: '信息系统管理',
              path: 'cata/imSys'
            },
            {
              name: '信息资源标准',
              path: 'cata/data-source'
            },
            {
              name: '物理资源配置',
              path: 'cata/pdpList'
            },
            {
              name: '数据源配置',
              path: 'cata/dataSrcConf_list'
            }
          ]
        },
        {
          iconClassName: 'appstore',
          name: '个人中心',
          children: [
            {
              name: '其它任务',
              path: 'todo/other'
            },
            {
              name: '我的报障',
              path: 'todo/reporting-obstacles'
            },
            {
              name: '编目挂接任务',
              path: 'todo/hook'
            },
            {
              name: '审核任务',
              path: 'todo/audit'
            }
          ]
        },
        {
          iconClassName: 'appstore',
          name: '服务管理',
          children: [
            {
              name: '接口管理',
              path: 'service/intfc/list'
            },
            {
              name: '第三方接口管理',
              path: 'service/interfaceManagement/list'
            },
            {
              name: '配置管理',
              path: 'service/confManagement/list'
            }
          ]
        },
        {
          iconClassName: 'appstore',
          name: '实施管理',
          children: [
            {
              name: '共享实施',
              path: 'implement/view/list'
            }
          ]
        },
        {
          iconClassName: 'appstore',
          name: '系统管理',
          children: [
            {
              name: '政府用户管理（超级管理员）',
              path: 'system/super_admin'
            },
            {
              name: '政府用户管理（部门管理员）',
              path: 'system/dept_admin'
            },
            {
              name: '政府用户管理（地市超级管理员）',
              path: 'system/city_super_admin'
            },
            {
              name: '政府临时用户账号管理',
              path: 'system/account-admin'
            },
            {
              name: '厂商名录管理',
              path: 'admin/company/index'
            }
          ]
        },
        {
          iconClassName: 'appstore',
          name: '门户管理',
          children: []
        },
        {
          iconClassName: 'appstore',
          name: '需求管理',
          children: [
            {
              name: '需求分析完善',
              path: 'demand/analysis/list'
            },
            {
              name: '需求概览',
              path: 'demand/overview/list'
            }
          ]
        }
      ]
      this.$store.dispatch(this.$store.state.sideBarConfig, menus)
    },
    initheight() {
      // const a = $(window).height()
      // $('#a').css({ 'overflow-y': 'auto', height: a + 'px' })
      $(window).bind('load resize', function() {
        $('#a').css({
          'overflow-y': 'auto',
          height: $(window).height() - 100 + 'px'
        })
        // console.log($(window).height())
      })
    },
    setDefaultSelectedKeys(route) {
      //console.log(route);
      //const name = route.path.substring(1)
      //if (Object.keys(route.params) && !this.isSideBarCollapse) {
     //   this.setDefaultOpenKeys(name)
     // }
      // const length = split.length
      // const name = split[length - 1]
     // this.$store.commit('common/updateMainTabs', name)
     // return [name]
    },

    /**
     * 处理子菜单打开的key array
     *  输出示例：
     *    入参：a/b/c/d
     *    返回：['a', 'a/b', 'a/b/c']
     * @param path
     */
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











