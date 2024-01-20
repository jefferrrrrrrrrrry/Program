<template>
  <view>
    <view class="search-box">
      <uni-search-bar @input="input" :radius="100" cancelButton="none" placeholder="请输入搜索内容"></uni-search-bar>
    </view>

    <!-- 搜索建议列表 -->
    <view class="sugg-list" v-if="searchResults.length !== 0">
      <view class="sugg-item" v-for="(item, i) in searchResults" :key="i" @click="gotoDetail(item)">
        <view class="goods-name">{{item.goods_name}}</view>
        <uni-icons type="arrowright" size="16"></uni-icons>
      </view>
    </view>

    <!-- 搜索历史 -->
    <view class="history-box" v-else>
      <!-- 标题区域 -->
      <view class="history-title">
        <text>搜索历史</text>
        <uni-icons type="trash" size="17" @click="clean"></uni-icons>
      </view>
      <!-- 列表区域 -->
      <view class="history-list">
        <uni-tag :text="item" v-for="(item, i) in histories" :key="i" @click="gotoGoodsList(item)"></uni-tag>
      </view>
    </view>
  </view>
</template>

<script>
  export default {
    data() {
      return {
        timer: null,
        kw: '',
        // 搜索的结果列表
        searchResults: [],
        // 搜索历史的数组
        historyList: []
      };
    },
    onLoad() {
      this.historyList = JSON.parse(uni.getStorageSync('kw') || '[]')
    },
    methods: {
      // input 输入事件的处理函数
      input(e) {
		
        clearTimeout(this.timer)

        this.timer = setTimeout(() => {
          this.kw = e
		  console.log(e)
          this.getSearchList()
        }, 500)
      },
      async getSearchList() {
        // 判断搜索关键词是否为空
        if (this.kw.length === 0) {
          this.searchResults = []
          return
        }

        const { data: res } = await uni.$http.get('/api/public/v1/goods/qsearch', { query: this.kw })
        if (res.meta.status !== 200) return uni.$showMsg()
        this.searchResults = res.message

        this.saveSearchHistory()
      },
      gotoDetail(item) {
        uni.navigateTo({
          url: '/subpkg/goods_detail/goods_detail?goods_id=' + item.goods_id
        })
      },
      saveSearchHistory() {
        // this.historyList.push(this.kw)

        const set = new Set(this.historyList)
        set.delete(this.kw)
        set.add(this.kw)

        this.historyList = Array.from(set)

        // 对搜索历史数据，进行持久化的存储
        uni.setStorageSync('kw', JSON.stringify(this.historyList))
      },
      clean() {
        this.historyList = []
        uni.setStorageSync('kw', '[]')
      },
      gotoGoodsList(kw) {
        this.kw = kw
        this.getSearchList()
      }
    },
    computed: {
      histories() {
        return [...this.historyList].reverse()
      }
    }
  }
</script>

<style lang="scss">
  .search-box {
    position: sticky;
    top: 0;
    z-index: 999;
  }

  .sugg-list {
    padding: 0 5px;

    .sugg-item {
      display: flex;
      align-items: center;
      justify-content: space-between;
      font-size: 12px;
      padding: 13px 0;
      border-bottom: 1px solid #fefefe;

      .goods-name {
        white-space: nowrap;
        overflow: hidden;
        text-overflow: ellipsis;
        margin-right: 3px;
      }
    }
  }

  .history-box {
      padding: 10px;
  
      .history-title {
        display: flex;
        justify-content: space-between;
        height: 40px;
        align-items: center;
        font-size: 16px;
        border-bottom: 1px solid #e0e0e0;
  
        text {
          color: #333; /* 标题文字颜色 */
        }
  
        .uni-icons {
          color: #ff5252; /* 删除图标颜色 */
          cursor: pointer;
        }
      }
  
      .history-list {
        display: flex;
        flex-wrap: wrap;
  
        .uni-tag {
          margin-top: 10px;
          margin-right: 10px;
          display: inline-block;
          background-color: #f0f0f0; /* 历史标签背景色 */
          color: #333; /* 历史标签文字颜色 */
          padding: 8px 15px;
          border-radius: 20px;
          cursor: pointer;
        }
      }
    }
</style>
