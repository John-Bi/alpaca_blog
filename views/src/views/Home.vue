<template>
  <div @mousewheel="mouseWheel">
    <div class="nav">
      <template v-for="(item, index) in this.$router.options.routes">
        <router-link :to="{name: item.name}" :key="index" exact><i @click="clickRouteChange" class="nav-item"></i></router-link>
      </template>
    </div>
    <div class="page">
      <transition
        mode="out-in"
        :duration="{ enter: animateOptions.enterTime, leave: animateOptions.leaveTime }"
        :leave-active-class="'animated ' + animateOptions.leave"
        :enter-active-class="'animated ' + animateOptions.enter">
        <router-view />
      </transition>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
import { State, Action } from 'vuex-class';
import 'animate.css';


@Component
export default class Home extends Vue {
  @State private animateOptions: any;

  @Action private next!: (x: string | undefined | null ) => any;
  @Action private last!: (x: string | undefined | null ) => any;
  @Action private clickRouteChange!: () => void;

  private lastScroll: number = 0;

  private mouseWheel(event: any) {
    // 防止用户短时间内滚动多次，设置滚动间隔大于一秒才能生效
    // 判断滚动间隔时间
    const scrollDuration = event.timeStamp - this.lastScroll;
    if (scrollDuration > 1000) {
    // 将这一次的滚动时间记录为上一次合法的滚动时间
    this.lastScroll = event.timeStamp;
    // console.log('合法的滚动')
    // 判断滚动方向进行操作
    if (event.deltaY > 0) {
      const presentName: string | undefined | null = this.$route.name;
      this.next(presentName).then((nextPageName: string)  => {
        this.$router.push({name: nextPageName});
      });
    } else {
      const presentName: string | undefined | null = this.$route.name;
      this.last(presentName).then((lastPageName: string) => {
        this.$router.push({name: lastPageName});
      });
    }
  }
}

}
</script>

<style lang="scss" scoped>
.nav {
  position: fixed;
  left: 20px;
  top: 45%;
  z-index: 2;
  color: grey;
  a {
    text-decoration-line: none;
  }

  .nav-item {
    display: block;
    font-size: 11px;
    margin-top: 5px;
    height: 12px;
    width: 12px;
    border-radius: 100%;
    border: 1px solid white;
    opacity: 0.5;
  }
  .router-link-active {
    .nav-item {
      background-color: white !important;
    }
  }
}
.page{
  position: absolute;
  top: 0;
  background-color: black;
}

@media screen and (max-width: 767px){
  .nav{
    display: none;
  }
}
</style>