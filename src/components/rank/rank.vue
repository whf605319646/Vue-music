<template>
  <div class="rank">
    <scroll class="toplist" :data="topList" ref="topList">
      <ul>
        <li @click="selectItem(list)" class="item" v-for="(list,index) in topList" :key="index">
          <div class="icon">
            <img width="100" height="100" v-lazy="list.picUrl">
          </div>
          <ul class="songlist">
            <li class="song" v-for="(song,key) in list.songList" :key="key">
              <span>{{key + 1}}</span>
              <span>{{song.songname}} -- {{song.singername}}</span>
            </li>
          </ul>
        </li>
      </ul>
      <div class="loading-container" v-show="!topList.length">
        <loading></loading>
      </div>
    </scroll>
    <router-view></router-view>
  </div>
</template>
<script>
import Scroll from 'base/scroll/scroll';
import Loading from 'base/loading/loading';
import {getTopList} from 'api/rank';
import {ERR_OK} from 'api/config';
import {playlistMixin} from 'common/js/mixin';
import {mapMutations} from 'vuex';
export default {
  name: 'rank',
  mixins: [playlistMixin],
  data() {
    return {
      topList: []
    };
  },
  created() {
    this._getTopList();
  },
  methods: {
    selectItem(list) {
      this.setTop(list);
      this.$router.push(`/rank/${list.id}`);
    },
    playlistHandler(playlist) {
      this.$refs.topList.$el.style.height = '';
      let deHeight = playlist.length > 0 ? 60 : 0;
      let elHeight = window.getComputedStyle(this.$refs.topList.$el).height;
      this.$refs.topList.$el.style.height = parseFloat(elHeight) - deHeight + 'px';
      this.$refs.topList.refresh();
    },
    _getTopList() {
      getTopList().then((res) => {
        if (res.code === ERR_OK) {
          this.topList = res.data.topList;
        };
      });
    },
    ...mapMutations({
      setTop: 'SET_TOP'
    })
  },
  components: {
    Loading,
    Scroll
  }
};
</script>
<style lang="less" scoped>
@import "~common/less/variable";
@import "~common/less/mixin";

.rank{
  position: fixed;
  width: 100%;
  top: 88px;
  bottom: 0;
  .toplist{
    height: 100%;
    overflow: hidden;
    .item{
      display: flex;
      margin: 0 20px;
      padding-top: 20px;
      height: 100px;
      &:last-child{
        padding-bottom: 20px;
      }
      .icon{
        flex: 0 0 100px;
        width: 100px;
        height: 100px;
      }
      .songlist{
        flex: 1;
        display: flex;
        flex-direction: column;
        justify-content: center;
        padding: 0 20px;
        height: 100px;
        overflow: hidden;
        background: @color-highlight-background;
        color: @color-text-d;
        font-size: @font-size-small;
        .song{
          .no-wrap();
          line-height: 26px;
        }
      }
    }
    .loading-container{
      position: absolute;
      width: 100%;
      top: 50%;
      transform: translateY(-50%);
    }
  }
}
</style>