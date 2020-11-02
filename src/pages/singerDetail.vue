<template>
  <transition name="slide">
    <v-music-list :title="title" :bg-image="bgImage" :songs="songs"></v-music-list>
  </transition>
</template>

<script>
import api from '@/api'
import musicList from '@/components/musicList'

export default {
  name: 'singer-detail',
  components: {
    'v-music-list': musicList,
  },
  data() {
    return {
      songs: [],
      bgImage: '',
      title: '',
    }
  },
  created() {
    this._getSingerMusciList()
  },
  methods: {
    // 获取歌手歌单
    _getSingerMusciList() {
      // 如果路由中没有id就返回热门页面，有就获取歌单列表
      if (!this.$route.params.id) {
        this.$router.push('/singer')
        return
      }
      this.$showLoading()
      api.SingerMusciList({ id: this.$route.params.id })
        .then((res) => {
          if (res.code === 200) {
            this.$hideLoading()
            this.songs = res.hotSongs
            this.bgImage = res.artist.img1v1Url
            this.title = res.artist.name
          }
        })
    },
  },
}
</script>

<style scoped lang="scss">
.slide-enter-active,
.slide-leave-active {
  transition: all 0.3s;
}

.slide-enter,
.slide-leave-to {
  transform: translate3d(100%, 0, 0);
}
</style>
