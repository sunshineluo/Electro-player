<script setup>
import { RouterView } from "vue-router";
import ElectroHeader from "components/electroHeader/ElectroHeader.vue";
import { ref, onMounted, nextTick } from "vue";
import { getPlayListById } from "./apis/musiclist";
import { usePlayListStore } from "./stores/playlist";
import { ELECTROPLAYER_CONFIG, VERSION, UPDATE_TIME } from "./config";

const usePlayList = usePlayListStore();

const initPlayList = async () => {
  const playList = await getPlayListById(ELECTROPLAYER_CONFIG.PLAYLIST_ID);
  const list = playList.tracks.slice(0, 100);
  usePlayList.setPlayList(list);
};

const electroPlayer = ref(null);
onMounted(() => {
  console.log("软件版本：", VERSION);
  console.log("更新时间：", UPDATE_TIME);
  console.log("网站作者：罗阳");
  console.log("代码来源：https://github.com/jianchuxin/Electro-player");
  console.log("声明：本网站是根据Electro-player大佬提供的代码进行搭建的，仅方便听歌之用，不得用做其他用途！当你看到这个页面时，你可以查看大佬代码或者原路返回继续听歌吧")
  // 初始化播放列表
  initPlayList();

  // 设置audio元素
  nextTick(() => {
    usePlayList.setAudioEle(electroPlayer.value);
  });
});

// 首次加载动画后移除动画
let loadDom = document.querySelector("#appLoading");
if (loadDom) {
  const animationendFunc = () => {
    loadDom.removeEventListener("animationend", animationendFunc);
    loadDom.removeEventListener("webkitAnimationEnd", animationendFunc);
    document.body.removeChild(loadDom);
    loadDom = null;
  };
  loadDom.addEventListener("animationend", animationendFunc);
  loadDom.addEventListener("webkitAnimationEnd", animationendFunc);
  loadDom.classList.add("removeAnimate");
}
</script>

<template>
  <ElectroHeader />
  <RouterView />
  <!-- 播放器 -->
  <audio ref="electroPlayer"></audio>
</template>

<style lang="less">
#app {
  position: relative;
  width: 100%;
  height: 100%;
  color: @text_color;
  font-size: @font_size_medium;
  audio {
    position: fixed;
  }
}
</style>
