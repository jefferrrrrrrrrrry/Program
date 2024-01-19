<template>
  <view>
    <!-- 轮播图的区域 -->
    <swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" :circular="true">
      <swiper-item v-for="(item, i) in swiperList" :key="i">
        <navigator class="swiper-item" :url="'/subpkg/goods_detail/goods_detail?goods_id=' + item.goods_id">
          <image :src="item.image_src"></image>
        </navigator>
      </swiper-item>
    </swiper>
   </view>
   <view class="card about-us">
         <view class="card-content">
           <view class="about-us-title">关于我们</view>
           <view >
			   <text class="about-us-content">宁波市海曙杰盛气动工具有限公司专注于气动工具的研发、生产和销售，广泛服务于铸造行业、船舶修造企业、航空航天制造维修以及工业制造加工领域。我们引以为豪的产品是舵手（RUDDER）系列打磨类气动工具，它们是在吸取世界各大同行品牌技术优点的基础上进行改造的杰出之作。\n</text>
			   <text class="about-us-content">舵手系列气动工具以高性能、高耐用和高扭力为制造目标，旨在满足客户对卓越品质和可靠性的需求。通过不断努力，我们致力于将舵手打磨类气动工具打造成为中国民族气动工具的代表，为客户提供卓越的工具解决方案。\n</text>
			   <text class="about-us-content">宁波市海曙杰盛气动工具有限公司期待与您携手合作，共同发展，为行业的进步贡献力量。</text>
           </view>
         </view>
     </view>
    <!-- 楼层区域 -->
    <!-- 楼层的容器 -->
    <view class="floor-list">
      <!-- 每一个楼层的 item 项 -->
      <view class="floor-item" v-for="(item, i) in floorList" :key="i">
        <!-- 楼层的标题 -->
        <image :src="item.floor_title.image_src" class="floor-title"></image>
        <!-- 楼层的图片区域 -->
        <view class="floor-img-box">
          <!-- 左侧大图片的盒子 -->
          <navigator class="left-img-box" :url="item.product_list[0].url">
            <image :src="item.product_list[0].image_src" :style="{width: item.product_list[0].image_width + 'rpx'}" mode="widthFix"></image>
          </navigator>
          <!-- 右侧 4 个小图片的盒子 -->
          <view class="right-img-box">
            <navigator class="right-img-item" v-for="(item2, i2) in item.product_list" :key="i2" :url="item2.url">
              <image :src="item2.image_src" :style="{width: item2.image_width + 'rpx'}"  v-if="i2 !== 0" mode="widthFix"></image>
            </navigator>
          </view>
        </view>
      </view>
    </view>
</template>

<script>
  export default {
    data() {
      return {
        // 这是轮播图的数据列表
        swiperList: [],
        // 分类导航的数据列表
        navList: [],
        // 楼层的数据
        floorList: [],
        companyIntroduction: "宁波市海曙杰盛气动工具有限公司专注于气动工具的研发、生产和销售，广泛服务于铸造行业、船舶修造企业、航空航天制造维修以及工业制造加工领域。我们引以为豪的产品是舵手（RUDDER）系列打磨类气动工具，它们是在吸取世界各大同行品牌技术优点的基础上进行改造的杰出之作。\n舵手系列气动工具以高性能、高耐用和高扭力为制造目标，旨在满足客户对卓越品质和可靠性的需求。通过不断努力，我们致力于将舵手打磨类气动工具打造成为中国民族气动工具的代表，为客户提供卓越的工具解决方案。\n宁波市海曙杰盛气动工具有限公司期待与您携手合作，共同发展，为行业的进步贡献力量。"
      };
    },
    onLoad() {
      // 调用方法，获取轮播图的数据
      this.getSwiperList()
      this.getNavList()
      this.getFloorList()
    },
    methods: {
      async getSwiperList() {
        const { data: res } = await uni.$http.get('/api/public/v1/home/swiperdata')
        // 请求失败
        if (res.meta.status !== 200) return uni.$showMsg()

        this.swiperList = res.message
      },
      async getNavList() {
        const { data: res } = await uni.$http.get('/api/public/v1/home/catitems')
        if (res.meta.status !== 200) return uni.$showMsg()
        this.navList = res.message
      },
      // 获取首页楼层数据的方法
      async getFloorList() {
        const { data: res } = await uni.$http.get('/api/public/v1/home/floordata')
        if (res.meta.status !== 200) return uni.$showMsg()

        // 对数据进行处理
        res.message.forEach(floor => {
          floor.product_list.forEach(prod => {
            prod.url = '/subpkg/goods_list/goods_list?' + prod.navigator_url.split('?')[1]
          })
        })
        this.floorList = res.message
      },
	  showCustomModal() {
	    this.setData({
		showModal: true,
	  });
	},
	hideModal() {
	  this.setData({
		showModal: false,
	  });
	}
    }
  }
</script>

<style lang="scss">
  swiper {
    height: 330rpx;

    .swiper-item,
    image {
      width: 100%;
      height: 100%;
    }
  }

  .nav-list {
    display: flex;
    justify-content: space-around;
    margin: 15px 0;

    .nav-img {
      width: 128rpx;
      height: 140rpx;
    }
  }

  .floor-title {
    width: 100%;
    height: 60rpx;
  }

  .right-img-box {
    display: flex;
    flex-wrap: wrap;
    justify-content: space-around;
  }

  .floor-img-box {
    display: flex;
    padding-left: 10rpx;
  }
  .card {
    background-color: #bde237; /* 设置蓝色背景 */
    border-radius: 10rpx; /* 圆角 */
    box-shadow: 0 2rpx 4rpx rgba(0, 0, 0, 0.1); /* 阴影效果 */
    overflow: hidden;
  }
  
  .card-content {
    padding: 40rpx;
  }
  
  .about-us-title {
    font-size: 50rpx;
    font-weight: bold;
    color: #333;
    margin-bottom: 10rpx;
  }
  
  .about-us-content {
    font-size: 35rpx;
	text-indent: 2em;
	white-space: pre-line;
    color: #666;
    line-height: 1.5;
	display: block;
  }
</style>
