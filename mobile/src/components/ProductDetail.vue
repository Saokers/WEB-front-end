<template>
  <div class="centext">
    <mHeader>商品详情</mHeader>
    <div>
      <div>
        <img
          class="pro"
          :src="$api.BASE_SERVER_URL + product.defaultImg"
          alt=""
        />
        <div class="collectWrap">
          <img src="/static/img/collect.png" alt="" />
        </div>
        <div class="small">
          ￥<span class="large">{{ Math.floor(product.shopPrice / 100) }}.</span
          ><span>{{ product.shopPrice % 100 }}</span>
        </div>
        <div class="title">{{ product.name }}</div>
        <div>
          <van-tabs v-model="active">
            <van-tab title="商品详情">
              <div class="title">
                清场大甩卖<i class="mdi mdi-ab-testing:"></i>
              </div>
              <div>
                <span class="shopp"
                  >店面价格:
                  <span class="shops"
                    >￥{{ product.shopPrice / 100 }}</span
                  ></span
                >
                <span class="shopp"
                  >市场价格:
                  <span class="shopz">￥{{ product.price / 100 }}</span></span
                >
              </div>
            </van-tab>
            <van-tab title="商品评论">
              <van-field
                v-model="value"
                maxlength="50"
                placeholder="请输入评论"
              >
                <template #button>
                  <van-button size="small" @click="setComment()" type="primary"
                    >发表评论</van-button
                  >
                </template>
              </van-field>
              <van-cell
                style="text-align:left"
                :title="item.user.nickname + ' :'"
                v-for="(item, index) in comment"
                :key="index"
                :value="item.content"
              />
            </van-tab>
          </van-tabs>
          <div>
            <div></div>
            <div></div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import mHeader from "./other/header";
export default {
  data() {
    return {
      product: [],
      active: null,
      comment: [],
      value: null,
      user: this.$store.getters.user
    };
  },
  created() {
    this.getproductList();
    this.getproductCpmment();
    this.getfootPrint();
  },
  methods: {
    //添加足迹
    getfootPrint() {
      this.$post(this.$api.API_URL_FOOTPRINT_ADD, {
        userId: this.user.id,
        productId: this.$route.params.id
      })
    },
    //查询商品详情
    getproductList() {
      this.$get(
        this.$api.API_URL_CATALOG_PRODUCT_DETAILS + "/" + this.$route.params.id
      ).then(res => {
        this.product = res.data;
      });
    },
    //查询商品评论
    getproductCpmment() {
      this.$get(
        this.$api.API_URL_CATALOG_SHOW_REVIEW + "/" + this.$route.params.id
      ).then(res => {
        this.comment = res.data;
      });
    },
    //发表评论
    setComment() {
      if (this.value == null) {
        this.$toast("请输入正确的评论！");
      } else {
        this.$post(this.$api.API_URL_CATALOG_ADD_REVIEW, {
          productId: this.$route.params.id,
          userId: this.user.id,
          content: this.value,
          stars: 0,
          audit: 1
        }).then(res => {
          if (res.errorCode == 0) {
            this.$toast("评论成功！");
            this.value = "";
            this.getproductCpmment();
          } else {
            this.$toast("网络错误");
          }
        });
      }
    }
  },
  components: { mHeader }
};
</script>

<style scoped lang="stylus">
.centext{

}
.pro{
  width 100%
}
.small{
  color #FF1A00
  font-size 20px
  font-weight bold
  text-align left
  margin-left 10px
}
.large{
  font-size 30px
  font-weight bold
}
.title{
  font-size 18px
  text-align left
  padding 10px
  font-weight bold
}
.collectWrap {
  width: 2em; height: 2em;
  border-radius: 50%;
  background: rgba(0,0,0,0.5);
  position: fixed;
  right: 1em; top: 4em;
  cursor: pointer;
  & img {
    position: absolute;
    width: 70%;
    left: 15%;
    top: 15%;
  }
}
.shopp{
  margin-left:15px;
  color #000000

}

.shops{
  color #209F23
}
.shopz{
 text-decoration line-through
}
</style>
