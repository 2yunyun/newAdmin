<template>
  <div>
    <el-aside id="asideNav">
      <div class="logo-name">
        <p v-if="$store.getters.logoShow">Admin</p>
        <p v-else>XXX管理系统</p>
      </div>
      <el-menu :default-active="$route.path" class="el-menu-vertical"
               @select="selectmenu"
               :collapse="$store.getters.isCollapse"
               background-color="#fff"
               text-color="rgba(255,255,255)"
               active-text-color="rgb(49, 180, 141)"
               :router="$store.getters.uniquerouter"
               :unique-opened="$store.getters.uniquerouter"
               :collapse-transition="true"
      >
        <template v-for="(item,index) in $store.getters.routers" v-if="!item.hidden">
          <el-submenu v-if="!item.alone && item.children.length>0" :index="index+''">
            <template slot="title">
              <i :class="item.iconCls?item.iconCls:[fa,fa-server]"></i>
              <span slot="title">{{ $t(`routeName.${item.name}`) }}</span>
            </template>

            <menu-tree :menuData="item.children"></menu-tree>

          </el-submenu>
          <el-menu-item :index="item.path" v-else>
            <i :class="item.iconCls?item.iconCls:[fa,fa-file]"></i>
            <span slot="title">{{ $t(`routeName.${item.name}`) }}</span>
          </el-menu-item>
        </template>

      </el-menu>
    </el-aside>
  </div>
</template>

<script>
  import menuTree from './menuTree'

  export default {
    name: 'asideNav',
    components: {
      menuTree,
    },
    watch: {
      // 监听浏览器直接输入路由，将此路由添加到tabnavBox
      '$route.path': function (val) {
        this.selectmenu(val)
      }
    },
    methods: {
      selectmenu (key) {
        let router = this.$store.getters.routers
        let name = ''
        let navTitle = function (path, routerARR) {
          for (let i = 0; i < routerARR.length; i++) {
            if (routerARR[i].children.length > 0 || routerARR[i].path === path) {
              if (routerARR[i].path === path && routerARR[i].children.length < 1) {
                name = routerARR[i].name
                break
              }
              navTitle(path, routerARR[i].children)
            }
          }
          return name
        }
        this.$store.dispatch('addTab', {
          title: navTitle(key, router),
          path: key
        })
      }
    }
  }
</script>

<style lang="scss">
  $top: top;
  $bottom: bottom;
  $left: left;
  $right: right;
  %w100 {
    width: 100%;
  }

  %h100 {
    height: 100%;
  }

  %cursor {
    cursor: pointer;
  }

  @mixin set-value($side, $value) {
    @each $prop in $leftright {
      #{$side}-#{$prop}: $value;
    }
  }

  #asideNav {
    width: auto !important;
    display: flex;
    flex-direction: column;
    border-right: solid 1px #e6e6e6;
    .logo-name {
      background-color: #f0f2f5 !important;
      @extend %w100;
      font-weight: 600;
      z-index: 999;
      p {
        height: 50px;
        line-height: 50px;
        text-align: center;
        font-size: 16px;
        color: #000;
      }
    }
    .el-menu-vertical:not(.el-menu--collapse) {
      width: 200px;
      @extend %h100;
      overflow-y: scroll;
      overflow-x: hidden;
    }
    .el-menu {
      flex: 1;
      overflow: inherit;
      border-right: none;
      &::-webkit-scrollbar {
        display: none;
      }
      .fa {
        vertical-align: middle;
        margin-right: 5px;
        width: 24px;
        text-align: center;
        font-size: 18px;
      }
      .el-menu-item {
        background-color: #fff !important;
        border-bottom: 1px solid #fff;
        color:#909399 !important;
        &:hover {
          color: rgb(49, 180, 141) !important;
          background-color: #fff !important;
        }
      }
      .el-menu-item.is-active {
        color: rgb(49, 180, 141) !important;
        background-color: #fff!important
      }

      .el-submenu__title span{
        color:#909399;

      }
      

      .is-opened > .el-submenu__title > .el-icon-arrow-down {
        color: #ffffff;
        font-weight: 500;
        font-size: 18px;
      }
    }

  }
</style>
