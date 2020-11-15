<template>
  <div>
    <van-search
      placeholder="请输入你想要搜索的内容"
      show-action
      v-model="value"
    >
      <div slot="action" @click="$router.push('/samsara/search/' + value)">
        搜索
      </div>
    </van-search>
    <div>
      <div class="smallbox">
        <div
          class="cell"
          v-for="(item, index) in list"
          :key="index"
          @click="$router.push('/samsara/category/' + item.id)"
        >
          <img :src="' ../static/assets/' + (index + 1) + '.png'" alt="" />
          <span>{{ item.name }}</span>
        </div>
        <div class="cell">
          <img
            src="../../static/assets/more.png"
            @click="$router.push('/samsara/categoryList/')"
            alt=""
          />
          <span>其他</span>
        </div>
      </div>

      <div v-for="(product, index) in productList" :key="index">
        <div class="title">{{ product.name }}</div>
        <van-cell
          :to="'/samsara/productdetail/' + item.id"
          v-for="(item, index) in product.productRelationList"
          :key="index"
          is-link
        >
          <img
            slot="icon"
            :src="$api.BASE_SERVER_URL + item.defaultImg"
            height="50"
            width="50"
            alt=""
          />
          <span>
            <div class="productNa">{{ item.name }}</div>
            <div>
              <span class="shopp"
                >店面价格:
                <span class="shops">￥{{ item.shopPrice / 100 }}</span></span
              >
              <span class="shopp"
                >市场价格:
                <span class="shopz">￥{{ item.price / 100 }}</span></span
              >
            </div>
          </span>
        </van-cell> 
         <van-cell  :to="'../../samsara/category/'+product.id" is-link title="查看更多"></van-cell>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      value: null,
      list: [],
      productList: []
    };
  },
  created() {
    this.$post(this.$api.API_URL_CATALOG_INDEX_PRODUCTS + "/3").then(res => {
      this.productList = res.data;
    });

    this.$get(this.$api.API_URL_CATEGORY_CONDITION).then(res => {
      this.list = res.data.slice(0, 7);
    });
  },methods:{
    
  }
};
</script>

<style scoped lang="stylus">
.smallbox{
  display flex
  flex-wrap wrap
  background #ffffff
}
.cell{
  padding 10px
  width 70px
  height 90px
  display flex
  flex-direction column
  align-items  center
}
.cell>img{
  margin-bottom 10px
  width 50px
  height 50px
}
.productNa{
margin-left:15px;
overflow: hidden;
white-space: nowrap;
text-overflow:ellipsis;
font-size:16px
}
.shopp{
  margin-left:15px;
  color #cccccc

}
.title{
  text-align left
  margin-left 10px
}
.shops{
  color #209F23
}
.shopz{
 text-decoration line-through
}
</style>
