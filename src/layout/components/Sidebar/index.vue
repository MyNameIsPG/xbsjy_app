<template>
  <div class="sidebar-container" :style="{ width: sidebarWidth }">
    <logo />
    <el-scrollbar
      wrap-class="scrollbar-wrapper"
      v-if="permissionRoutes && permissionRoutes.length > 0"
    >
      <el-menu
        :router="router"
        :mode="mode"
        :collapse="isCollapse"
        :default-active="$route.path"
        :unique-opened="uniqueOpened"
        :background-color="backgroundColor"
        :text-color="textColor"
        :active-text-color="activeTextColor"
        :collapse-transition="collapseTransition"
      >
        <sidebar-item
          v-for="route in permissionRoutes"
          :key="route.menuId"
          :index="route.menuId"
          :item="route"
          :isNest="sidebar.opened"
        />
      </el-menu>
    </el-scrollbar>
  </div>
</template>

<script>
import Logo from "./Logo";
import SidebarItem from "./SidebarItem";
import { resBaseMenuQueryLoginUserMenuTree } from "../../../api";
import { IS_OK } from "../../../api/path";
export default {
  components: {
    Logo,
    SidebarItem
  },
  props: {
    sidebar: {
      type: Object
    }
  },
  data() {
    return {
      router: true,
      mode: "vertical",
      backgroundColor: "#344058",
      textColor: "#fff",
      activeTextColor: "#1e9fff",
      uniqueOpened: false,
      collapseTransition: false,
      permissionRoutes: []
    };
  },
  created() {
    this.getMenus();
  },
  computed: {
    isCollapse() {
      return this.sidebar.opened;
    },
    sidebarWidth() {
      return this.sidebar.opened ? "62px" : "220px";
    }
  },
  methods: {
    async getMenus() {
      const res = await resBaseMenuQueryLoginUserMenuTree();
      if (res.data.code === IS_OK) {
        this.permissionRoutes = res.data.data;
        localStorage.setItem("menu", JSON.stringify(this.permissionRoutes));
      }
    }
  }
};
</script>

<style lang="stylus" scoped>
.sidebar-container {
  -webkit-transition: width 0.28s;
  transition: width 0.28s;
  background-color: #304156;
  height: 100%;
  position: fixed;
  font-size: 0;
  top: 0;
  bottom: 0;
  left: 0;
  z-index: 2;
  overflow: hidden;

  > .el-scrollbar {
    height: calc(100% - 50px);
  }
}
</style>
