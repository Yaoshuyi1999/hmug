<template>
  <view>
    <van-card :thumb-link="`/subpkg/good_detail/good_detail?id=${item.goods_id}`" v-for="item in goods"
      :key="item.goods_id" :price="item.goods_price |toFixed" :title="item.goods_name"
      :thumb='item.goods_small_logo || defaultPic' />
  </view>
</template>

<script>
  import {
    getGoodsList
  } from '@/api/goods.js'
  import toast from '@/utils/toast.js'
  export default {
    data() {
      return {
        queryData: {
          query: '', //关键字
          cid: '', //分类id
          pagenum: 1, //页码
          pagesize: 10 //每页10个数据
        },
        goods: [],
        total: 0,
        isLoading: false,
        defaultPic: 'https://img3.doubanio.com/f/movie/8dd0c794499fe925ae2ae89ee30cd225750457b4/pics/movie/celebrity-default-medium.png'
      };
    },
    methods: {
      async loadGoodsList(stopPullDown) {
        this.isLoading = true
        const res = await getGoodsList(this.queryData)
        this.isLoading = false
        // console.log(res);
        this.goods = [...this.goods, ...res.goods]
        this.total = res.total
        stopPullDown && stopPullDown()
      }
    },
    onLoad({
      query
    }) {
      // console.log(query);
      this.queryData.query = query
      this.loadGoodsList()
    },
    // 监听下拉行为
    onPullDownRefresh() {
      this.queryData = {
        query: this.queryData.query, //关键字
        cid: '', //分类id
        pagenum: 1, //页码
        pagesize: 10 //每页10个数据
      }
      this.goods = []
      this.total = 0
      this.loadGoodsList(() => {
        uni.stopPullDownRefresh()
      })
    },
    // 监听上拉触底
    onReachBottom() {
      if (this.queryData.pagenum * this.queryData.pagesize >= this.total) return toast('没有更多数据~')
      if (this.isLoading) return
      this.queryData.pagenum++
      this.loadGoodsList()
    }
  }
</script>

<style lang="scss">

</style>
