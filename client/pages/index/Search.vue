<template>
  <div>
    <div class="search-panel">
      <div class="search-input">
        <span class="search-icon"></span>
        <input v-model="keyword" placeholder="歌手/歌名/拼音" @keydown.enter="search">
      </div>
      <a href="javascript:;" @click="search" class="search-btn">搜索</a>
    </div>

    <div class="search-list" v-show="togglePanel">
      <div class="search-list-title">最近热门</div>
      <mt-cell v-for="(item,index) in hotList" :title="item.keyword" @click.native="replaceInput(index)"
               :key="index"></mt-cell>
    </div>

    <div class="songs-list" v-show="!togglePanel">
      <div class="search-total">
        共有{{total}}条搜索结果
      </div>
      <mt-cell v-for="(item,index) in songList" :title="item.filename" @click.native="playAudio(index)" :key="index">
        <img src="../../assets/images/download_icon.png" width="20" height="20">
      </mt-cell>
    </div>
  </div>
</template>

<script>
  import { PLAY_AUDIO } from '~/mixins/index'

  export default {
    mixins: [PLAY_AUDIO],
    data() {
      return {
        keyword: '',
        togglePanel: true,
        total: null,
        songList: []
      }
    },
    async asyncData({app}){
      const {data} = await app.$axios.get('/@mobilecdn.kugou.com/api/v3/search/hot?format=json&plat=0&count=30')
      return {
        hotList: data.data.info
      }
    },
    methods: {
      replaceInput(index) {
        this.keyword = this.hotList[index]
        this.Search()
      },
      search() {
        this.togglePanel = false
        if (this.keyword)
          this.$axios.get(`/@mobilecdn.kugou.com/api/v3/search/song?format=json&keyword=${this.keyword}&page=1&pagesize=30&showtype=1`).then(({data}) => {
            this.songList = data.data.info
            this.total = data.data.total
          })
      }
    }
  }
</script>
