<template>
  <div class="magnifier">
    <div class="outer-box">
      <!-- 放大图片 -->
      <div class="magnifier-box" v-show="elemenImgShow" ref="thumbnailBox">
        <div class="inner-box" ref="maginiferInnerbox">
          <img
            ref="thumbnailImg"
            style="width:1000px;height:1000px"
            :src="magnifierImgArr.magnifier[imgIndex].src"
            alt
          >
        </div>
      </div>
      <!-- 展示的图片 -->
      <div class="img-show" ref="imgShowBox">
        <div class="magnifier-img" v-show="elemenImgShow" ref="magnifierImg"></div>
        <img :src="magnifierImgArr.magnifier[imgIndex].src" alt>
        <div
          class="transparent"
          @mousemove="magnifiermove($event)"
          @mouseleave="elemenImgShow = false"
        ></div>
      </div>
      <!-- 缩略图
      <div class="img-thumbnail flexbox between">
        <div class="icon"
             @click="nextPic"
             v-if="iconShow">&lt;</div>
        <div class="rel-box"
             rel="ulOuterBox">
          <ul class=""
              ref="ulList">
            <li v-for="(item, index) in thumbnailForArr"
                :key="index"
                @mouseenter="thumbnailEnter(index)"
                :class="{'active':imgIndex===index}">
              <img :src="item.src"
                   alt=""
                   srcset="">
            </li>
          </ul>
        </div>
        <div class="icon"
             v-if="iconShow"
             @click="previous">&gt;</div>
      </div>-->
    </div>
  </div>
</template>
<script>
export default {
  // props: ['magnifierImgArr', 'goodsBoxWidth', 'reload'],
  //  props:{
  //    magnifierImgArr:Object,
  //  }
  /*
    magnifierImgArr
    传入的图片对象集合 数据格式 :
      magnifierImgArr: {
          //缩略图图片数组
         thumbnail: [{src: ''}],
          //要放大的图片数组
         showImg: [{src:''}]
          //放大的图片数组
         magnifier: [{src:''}]
       },
      // 其中缩略图图片数组可以不传 如果不传将自动使用 要放大的图片数组 的数据
      // goodsBoxWidth
      // 设置放大的图片盒子的宽度 尽量沾满商品图片的右边部分
       // reload 如果传入的数据发生变化自动将图片索引下标变为0
*/
  watch: {
    reload() {
      if (this.reload === 1) {
        this.imgIndex = 0;
      }
    }
  },
  data() {
    return {
      magnifierImgArr: {
        // 缩略图图片数组
        thumbnail: [
          {
            src:
              "https://img.alicdn.com/tfs/TB18DwDbcLJ8KJjy0FnXXcFDpXa-126-36.png"
          },
          {
            src:
              "https://img.alicdn.com/tfs/TB18DwDbcLJ8KJjy0FnXXcFDpXa-126-36.png"
          },
          {
            src:
              "https://img.alicdn.com/tfs/TB18DwDbcLJ8KJjy0FnXXcFDpXa-126-36.png"
          },
          {
            src:
              "https://img.alicdn.com/tfs/TB18DwDbcLJ8KJjy0FnXXcFDpXa-126-36.png"
          }
        ],
        // 要放大的图片数组
        showImg: [
          {
            src:
              "https://img.alicdn.com/tfs/TB18DwDbcLJ8KJjy0FnXXcFDpXa-126-36.png"
          }
        ],
        // 放大的图片数组
        magnifier: [
          {
            src:
              "//img.alicdn.com/imgextra/i2/1130055117/O1CN01jQISH61nfdDmsArNp_!!1130055117.jpg_430x430q90.jpg"
          }
        ]
      },
      imgIndex: 0,
      elemenImgShow: false, //控制放大镜图片的开关
      leftData: 0,
    };
  },
  mounted() {},
  computed: {
    thumbnailForArr() {
      if (
        this.magnifierImgArr.thumbnail &&
        this.magnifierImgArr.thumbnail.length
      ) {
        return this.magnifierImgArr.thumbnail;
      } else {
        return this.magnifierImgArr.showImg;
      }
    },
    // 是否显示左右操作按钮
    iconShow() {
      if (this.thumbnailForArr.length > 5) {
        return true;
      } else {
        return false;
      }
    }
  },
  methods: {
    // 缩略图鼠标移入事件
    thumbnailEnter(i) {
      this.imgIndex = i;
    },
    initData() {
      //动态设置放大镜盒子的left值，避免重叠问题，因为放大镜盒子和展示图片的盒子是兄弟
      //但也同时设置了absolute，设置绝对定位的盒子，会相互的重叠，和float不同，float兄弟之间不会重叠
      // 将放大盒子向右移动
      let outerBoxWidth = parseInt(this.$refs.imgShowBox.offsetWidth);
      // 放大镜盒子
      let thumbnailBox = this.$refs.thumbnailBox;
      thumbnailBox.style.left = outerBoxWidth + 15 + "px";  //多出15像素
    },
    magnifiermove(e) {
      // 有鼠标事件才加载大图片的位移 以解决在数据未加载之前就赋值了错误的位移
      this.initData();
      // 鼠标移入展示图片
      this.elemenImgShow = true;
      // 鼠标下移动的半透明方块
      let magnifierEle = this.$refs.magnifierImg;
      // 待放大的图片盒子宽高
      let outerBoxHeight = parseInt(this.$refs.imgShowBox.offsetHeight);
      let outerBoxWidth = parseInt(this.$refs.imgShowBox.offsetWidth);
      //  半透明方块的宽高
      let magnifierHeight = parseInt(magnifierEle.offsetHeight);
      let magnifierWidth = parseInt(magnifierEle.offsetWidth);
      // ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
      // 刚开始赋值left的初始值时就要减去放大镜图片宽度的一般 保证移动距离大于宽度一半之后才开始移动图片 top同理
      //鼠标移动事件中的e.offset是相对于这个容器的xy
      let left = e.offsetX - magnifierWidth / 2;
      let top = e.offsetY - magnifierHeight / 2;
      // 左边临界点
      left = left < 0 ? 0 : left;
      //  右边临界点 透明方块可以移动的距离只有盛放商品展示图片盒子的宽度减去半透明盒子的宽度 如果移动的距离大于这个宽度 就将可移动距离设置为最大宽度 否则为鼠标移动的距离 top值同理
      left =
        left > outerBoxWidth - magnifierWidth
          ? outerBoxWidth - magnifierWidth
          : left;
      magnifierEle.style.left = left + "px";
      // 上面临界点
      top = top < 0 ? 0 : top;
      // 下面临界点
      top =
        top > outerBoxHeight - magnifierHeight
          ? outerBoxHeight - magnifierHeight
          : top;
      magnifierEle.style.top = top + "px";
      // 放大的图片位移通过使用margin改变图片的位置 使用比例换算出移动的距离
      // 获取到需要放大的图片元素
      let thumbnailImgEle = this.$refs.thumbnailImg;
      let maginiferInnerboxEle = this.$refs.maginiferInnerbox;
      // 用大图片的宽高减去盛放大图片盒子的宽高就是可以移动的最大距离
      let maxLeft =
        thumbnailImgEle.offsetWidth - maginiferInnerboxEle.offsetWidth;
      let maxTop =
        thumbnailImgEle.offsetHeight - maginiferInnerboxEle.offsetHeight;
      // 换算出小图片对大图片的比例
      // 使用大图片可移动的距离除以放大镜可以移动的距离 即小图对大图的移动比例
      // 放大镜可移动的距离就是小图片盒子宽高减去放大镜盒子的宽高
      let leftScale = parseFloat(
        (maxLeft / (outerBoxWidth - magnifierWidth)).toFixed(2)
      );
      let topScale = parseFloat(
        (maxTop / (outerBoxHeight - magnifierHeight)).toFixed(2)
      );
      //他们的关系
      // maxleft*leftScale = outerBoxWidth - magnifierWidth（即图片最大的width）
      // 通过动态改变大图片的margin值实现图片移动
      console.log("leftScale",leftScale,"left",left)
      console.log("topScale",topScale,"top",top)
      thumbnailImgEle.style.marginLeft = "-" + leftScale * left + "px";
      thumbnailImgEle.style.marginTop = "-" + topScale * top + "px";
    },
    // 缩略图列表箭头的点击事件
    // 下一个
    nextPic() {
      this.positionDistance("next");
    },
    // 上一个
    previous() {
      this.positionDistance("prv");
    },
    positionDistance(nextOrPre) {
      let ulListEle = this.$refs.ulList;
      let ulListEleWidth = ulListEle.offsetWidth;
      // 设置单次移动的距离
      let num = ulListEleWidth / 4;
      let distance = 0;
      if (nextOrPre === "next") {
        // 设置可以移动的最大距离
        let maxData = this.thumbnailForArr.length * num - ulListEleWidth;
        distance = this.leftData >= maxData ? maxData : this.leftData + num;
      } else {
        distance = this.leftData > num ? this.leftData - num : 0;
      }
      ulListEle.style.left = "-" + distance + "px";
      this.leftData = distance;
    }
  }
};
</script>
<style lang="less" scoped>
//最外层的容器
.magnifier {
  min-width: 270px;
  padding: 10px;
  box-sizing: border-box;
  //里面的容器
  .outer-box {
    position: relative;
    width: 100%;
    height: 100%;
    //要展示的正常图片
    .img-show {
      border: 1px solid #ccc;
      width: 300px;
      height: 300px;
      position: relative;
      //鼠标移动进去的遮罩层
      .magnifier-img {
        width: 90px;
        height: 90px;
        background: rgba(160, 203, 236, 0.5);
        position: absolute;
        top: 0;
        left: 0;
        z-index: 10;
      }
      //设置绝对定位脱离标准流则使同级的img变为他的背景
      .transparent {
        cursor: move;
        width: 300px;
        height: 300px;
        background: none;
        position: absolute;
        top: 0;
        left: 0;
        z-index: 20;
      }
      img {
        width: 300px;
        height: 300px;
      }
    }
    //右侧放大静后的
    .magnifier-box {
      position: absolute;
      // left: 50px;
      // top: -5px;
      width: 300px;
      height: 300px;
      border: 1px solid #e6e6e6;
      //   padding: 20px;
      z-index: 10;
      background-color: #fff;
      .inner-box {
        overflow: hidden;
        width: 100%;
        height: 100%;
        background-color: #fff;
      }
    }

    // .img-thumbnail {
    //   width: 100%;
    //   margin-top: 10px;
    //   height: 54px;
    //   .icon {
    //     width: 35px;
    //     height: 35px;
    //     line-height: 25px;
    //     font-size: 14px;
    //     text-align: center;
    //     cursor: pointer;
    //     padding: 5px;
    //     box-sizing: border-box;
    //     background-color: #f5f5f5;
    //     // 禁止浏览器选中文字
    //     moz-user-select: -moz-none;
    //     -moz-user-select: none;
    //     -o-user-select: none;
    //     -khtml-user-select: none;
    //     -webkit-user-select: none;
    //     -ms-user-select: none;
    //     user-select: none;
    //     &:hover {
    //       background-color: #007acc;
    //       color: #fff;
    //     }
    //   }
    //   .rel-box {
    //     width: calc(100% - 60px);
    //     height: 100%;
    //     position: relative;
    //     overflow: auto;
    //     // 隐藏滚动条
    //     &::-webkit-scrollbar {
    //       display: none;
    //     }
    //     ul {
    //       position: absolute;
    //       left: 0;
    //       z-index: 100;
    //       top: 0;
    //       width: 100%;
    //       white-space: nowrap;
    //       li {
    //         width: calc(100% / 4);
    //         display: inline-block;
    //         height: 50px;
    //         border: 2px solid transparent;
    //         vertical-align: middle;
    //         box-sizing: border-box;
    //         &.active {
    //           border-color: #f00;
    //         }
    //         text-align: center;
    //         img {
    //           width: calc(100%);
    //           height: calc(100%);
    //         }
    //       }
    //     }
    //   }
    // }
  }
}
</style>
 
 