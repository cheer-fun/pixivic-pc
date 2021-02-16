<template>
  <div id="app">
    <el-container
      class="page-container"
      style="overflow: hidden;"
    >
      <!-- 左边栏开始 -->
      <el-aside
        style="background-color: rgb(238, 241, 246)"
        width="65px"
      >
        <left-side />
      </el-aside>
      <!-- 左边栏结束 -->
      <el-container style="overflow: hidden;">
        <!-- 标题栏开始 -->
        <el-header>
          <header-bar />
        </el-header>
        <!-- 标题栏结束 -->
        <!-- 主要页面开始 -->
        <el-main class="window-view">
          <vue-page-stack>
            <router-view
              :key="key"
              style="max-height: calc(~'100vh - 60px');"
            />
          </vue-page-stack>
        </el-main>
        <!-- 主要页面结束 -->
      </el-container>
    </el-container>
    <Login />
    <CollectPicture />
    <CheckPhone />
  </div>
</template>

<script>
import cookie from 'js-cookie';
import { mapGetters } from 'vuex';
import CheckPhone from 'components/CheckPhone';
import HeaderBar from './components/PublicComponents/HeaderBar.vue';
import LeftSide from './components/PublicComponents/LeftSide.vue';
import Login from './components/PublicComponents/Login/index.vue';
import CollectPicture from './components/Collections/CollectPicture';

function checkWebpFeature(feature, callback) {
  const kTestImages = {
    lossy: 'UklGRiIAAABXRUJQVlA4IBYAAAAwAQCdASoBAAEADsD+JaQAA3AAAAAA',
    lossless: 'UklGRhoAAABXRUJQVlA4TA0AAAAvAAAAEAcQERGIiP4HAA==',
    alpha: 'UklGRkoAAABXRUJQVlA4WAoAAAAQAAAAAAAAAAAAQUxQSAwAAAARBxAR/Q9ERP8DAABWUDggGAAAABQBAJ0BKgEAAQAAAP4AAA3AAP7mtQAAAA==',
    animation: 'UklGRlIAAABXRUJQVlA4WAoAAAASAAAAAAAAAAAAQU5JTQYAAAD/////AABBTk1GJgAAAAAAAAAAAAAAAAAAAGQAAABWUDhMDQAAAC8AAAAQBxAREYiI/gcA',
  };
  const img = new Image();
  img.onload = function () {
    const result = (img.width > 0) && (img.height > 0);
    callback(feature, result);
  };
  img.onerror = function () {
    callback(feature, false);
  };
  img.src = `data:image/webp;base64,${kTestImages[feature]}`;
}

export default {
  name: 'App',
  components: {
    HeaderBar,
    LeftSide,
    Login,
    CollectPicture,
    CheckPhone,
  },
  data() {
    return {

    };
  },
  computed: {
    key() {
      return this.$route.fullPath;
    },
    ...mapGetters(['user']),
  },
  mounted() {
    if (!cookie.get('alert')) {
      this.$notify({
        title: this.$tc('wechatImg'),
        duration: 0,
        dangerouslyUseHTMLString: true,
        message: '<img src="https://cdn.jsdelivr.net/gh/OysterQAQ/Blog-Image/wechat.jpg" style="height:200px;width:200px;"/>',
      });
      cookie.set('alert', true, {
        expires: 365,
      });
    }
    this.$i18n.locale = cookie.get('lang') || 'zh';
    checkWebpFeature('lossless', (f, e) => {
      sessionStorage.setItem('supportWebp', e);
    });
    this.getUsersInfo();
  },

  methods: {
    async getUsersInfo() {
      if (!this.user.id) { return; }
      const res = await this.$api.user.getUsers(this.user.id);
      this.$store.dispatch('setUser', res.data.data);
      // this.getProxyList(data);
    },
  },

};
</script>

<style lang="less">
#app {
  .page-container {
    .el-main {
      padding: 0px;
    }
  }
}
.window-view {
  height: calc(~"100vh - 60px");
  width: calc(~"100vw - 65px");
  overflow: auto;
  background: #fff;
}
</style>
