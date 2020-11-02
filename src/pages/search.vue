<template>
  <div class="search">
<!--    搜索输入框组件-->
    <div class="search-box-wrapper">
      <v-search-box ref="searchBox" @query="onQueryChange"></v-search-box>
    </div>
    <div ref="shortcutWrapper" class="shortcut-wrapper" v-show="!query">
<!--      搜索滑动区组件-->
      <v-scroll :refreshDelay="refreshDelay" ref="shortcut" class="shortcut" :data="shortcut">
        <div>
<!--          热搜关键词-->
          <div class="hot-key">
            <h1 class="title">热门搜索</h1>
            <ul>
              <li
                @click="addQuery(item.first)"
                class="item"
                v-for="(item, index) in hotKey"
                :key="index"
              >
                <span>{{item.first}}</span>
              </li>
            </ul>
          </div>
          <div class="search-history" v-show="searchHistory.length">
            <h1 class="title">
              <span class="text">搜索历史</span>
              <span @click="showConfirm" class="clear">
                <i class="icon">&#xe612;</i>
              </span>
            </h1>
<!--            热搜索关键词搜索组件 -->
            <v-search-list @delete="deleteSearchHistory" @select="addQuery" :searches="searchHistory"></v-search-list>
          </div>
        </div>
      </v-scroll>
    </div>
    <div class="search-result" v-show="query" ref="searchResult">
<!--      搜索到的歌曲 -->
      <v-suggest @listScroll="blurInput" @select="saveSearch" ref="suggest" :query="query"></v-suggest>
    </div>
<!--    清除搜索记录组件-->
    <v-confirm ref="confirm" @confirm="clearSearchHistory" text="是否清空所有搜索历史" confirmBtnText="清空"></v-confirm>
    <router-view></router-view>
  </div>
</template>

<script>
/*导入搜索相关组件*/
import searchBox from '@/components/searchBox'
import scroll from '@/components/scroll'
import searchList from '@/components/searchList'
import suggest from '@/components/suggest'
import confirm from '@/components/confirm'
import { playlistMixin, searchMixin } from 'common/js/mixin'
import api from '@/api'

import { mapActions, mapGetters } from 'vuex'


export default {
  name: 'search',
  components: {
    'v-scroll': scroll,
    'v-search-box': searchBox,
    'v-search-list': searchList,
    'v-suggest': suggest,
    'v-confirm': confirm
  },
  mixins: [playlistMixin, searchMixin],
  data() {
    return {
      hotKey: [],
    }
  },
  // 获取全局热搜关键词
  computed: {
    ...mapGetters([
      'searchHistory'
    ]),
    shortcut() {
      return this.hotKey.concat(this.searchHistory)
    }
  },
  // 页面时获取热搜关键词
  created() {
    this.limit = 10
    this._getHotKey()
  },
  methods: {
    // 播放方法
    handlePlaylist(playlist) {
      const bottom = playlist.length > 0 ? '1.5rem' : ''

      this.$refs.searchResult.style.bottom = bottom
      this.$refs.suggest.refresh()

      this.$refs.shortcutWrapper.style.bottom = bottom
      this.$refs.shortcut.refresh()
    },
    showConfirm() {
      this.$refs.confirm.show()
    },
    // 获取热搜关键词
    _getHotKey() {
      this.$showLoading()
      api.HotSearchKey().then((res) => {
        if (res.code === 200) {
          this.$hideLoading()
          this.hotKey = res.result.hots.slice(0, 10)
        }
      })
    },
    // 清除全局变量中的历史搜索
    ...mapActions([
      'clearSearchHistory'
    ])
  },
  // 监听输入的关键字
  watch: {
    query(newQuery) {
      if (!newQuery) {
        setTimeout(() => {
          this.$refs.shortcut.refresh()
        }, 20)
      }
    }
  }
}
</script>

<style lang="scss" scoped>
@import "../assets/css/function.scss";
.search {
  overflow: hidden;
  background: #fff;
  .search-box-wrapper {
    margin: px2rem(40px);
  }
  .shortcut-wrapper {
    position: fixed;
    top: px2rem(360px);
    bottom: 0;
    width: 100%;
    background: #fff;
    .shortcut {
      height: 100%;
      overflow: hidden;
      .hot-key {
        margin: 0 px2rem(40px) px2rem(40px) px2rem(40px);
        .title {
          margin-bottom: px2rem(40px);
          font-size: 14px;
          color: hsla(0,0%,100%,.5);
        }
        .item {
          display: inline-block;
          padding: px2rem(10px) px2rem(20px);
          margin: 0 px2rem(20px) px2rem(20px) 0;
          border-radius: 6px;
          background: #e7e7e7a1;
          font-size: 14px;
          color: #111;
        }
      }
      .search-history {
        position: relative;
        margin: 0 px2rem(40px);
        .title {
          display: flex;
          align-items: center;
          height: px2rem(80px);
          font-size: 14px;
          color: hsla(0,0%,100%,.5);
          .text {
            flex: 1;
          }
          .clear {
            // extend-click()
            .icon {
              font-size: 18px;
              color: hsla(0,0%,100%,.3);
            }
          }
        }
      }
    }
  }
  .search-result {
    position: fixed;
    width: 100%;
    top: px2rem(360px);
    bottom: 0;
  }
}
</style>
