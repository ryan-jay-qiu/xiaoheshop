<template>
  <ul class="app-header-nav">
    <li class="home"><RouterLink to="/">首页</RouterLink></li>
    <li v-for="item in list" :key="item.id" @mousemove="show1(item)" @mouseleave="hide1(item)">
      <RouterLink @click="hide1(item)" :to="`/category/${item.id}`">{{item.name}}</RouterLink>
      <div class="layer" :class="{open:item.open}">
        <ul>
          <li v-for="sub in item.children" :key="sub.id">
            <RouterLink  @click="hide1(item)" :to="`/category/sub/${sub.id}`">
              <img :src="sub.picture" alt="">
              <p>{{sub.name}}</p>
            </RouterLink>
          </li>
        </ul>
      </div>
    </li>
  </ul>
</template>
<script>
import { computed } from 'vue'
import { mapMutations,mapState} from 'vuex'
export default {
  name: 'AppHeaderNav',
  computed:{
...mapState('m_category',['list'])
  },
  created(){
      this.setList()
  },
  methods:{
...mapMutations('m_category',['show','hide','setList']),
show1(item){
this.show(item.id)
},
hide1(item){
this.show(item.id)
}
  },
}
</script>
<style scoped lang="less">
.app-header-nav {
  width: 820px;
  display: flex;
  justify-content: space-around;
  padding-left: 40px;
  position: relative;
  z-index: 999;
  > li {
    margin-right: 40px;
    width: 38px;
    text-align: center;
    > a {
      font-size: 16px;
      line-height: 32px;
      height: 32px;
      display: inline-block;
    }
    &:hover {
      > a {
        color: #eee;
        border-bottom: 1px solid #eee;
      }
      // 显示二级类目
      // > .layer {
      //   height: 132px;
      //   opacity: 1;
      // }
    }
  }
}
// 二级类名弹层
.layer {
  &.open {
    height: 132px;
    opacity: 1;
  }
  width: 1240px;
  background-color: #fff;
  position: absolute;
  left: -200px;
  top: 56px;
  height: 0;
  overflow: hidden;
  opacity: 0;
  box-shadow: 0 0 5px #ccc;
  transition: all .2s .1s;
  ul {
    display: flex;
    flex-wrap: wrap;
    padding: 0 70px;
    align-items: center;
    height: 132px;
    li {
      width: 110px;
      text-align: center;
      img {
        width: 60px;
        height: 60px;
      }
      p {
        padding-top: 10px;
      }
      &:hover {
        p {
          color: #eee;
        }
      }
    }
  }
}
</style>
