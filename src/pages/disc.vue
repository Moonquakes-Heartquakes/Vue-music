<template>
  <transition name="slide">
    <v-music-list :title="title" :bg-image="bgImage" :songs="songs"></v-music-list>
  </transition>
</template>

<script>
import api from '@/api'
import musicList from '@/components/musicList'

export default {
  name: 'disc',
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
  // 获取歌单详情
  created() {
    this._getSongList()
  },
  methods: {
    // 路由中有id就获取数据，没有就返回热门歌单推荐
    _getSongList() {
      if (!this.$route.params.id) {
        this.$router.push('/recommend')
        return
      }
      this.$showLoading()
      api.SongList({ id: this.$route.params.id })
        .then((res) => {
          if (res.code === 200) {
            this.$hideLoading()
            this.songs = res.playlist.tracks
            this.bgImage = res.playlist.coverImgUrl
            this.title = res.playlist.name
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
