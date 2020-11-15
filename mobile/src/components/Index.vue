<template>
  <div class="index">
    <van-search
      show-action
      placeholder="请输入你想要搜索的内容"
      v-model="value">
      <div slot="action" @click="search">搜索</div>
    </van-search>

    <div
      class="main"
      infinite-scroll-disabled="loading"
      infinite-scroll-distance="10"
      v-infinite-scroll="loadMore"
    >
      <div class="col">
        <div
          class="item"
          @click="nav(item.id)"
          v-for="(item, index) of img1"
          :key="index"
        >
          <img
            :src="
              $api.BASE_SERVER_URL + item.attachment_rows_data[0].attachmentPath
            "
            class="img"
          />
          <div>{{ item.noteContent }}</div>
          <div class="user">
            <div class="left">
              <img src="/static/1.jpg" alt="" />
              <span>{{
                name2[index]
              }}</span>
            </div>
            <div class="right">
              <van-icon name="like-o" />
              <span>{{ item.praiseNum }}</span>
            </div>
          </div>
        </div>
      </div>
      <div class="col">
        <div
          class="item"
          @click="nav(item.id)"
          v-for="(item, index) of img2"
          :key="index"
        >
          <img
            :src="
              $api.BASE_SERVER_URL + item.attachment_rows_data[0].attachmentPath
            "
            class="img"
          />
          <div>{{ item.noteContent }}</div>
          <div class="user">
            <div class="left">
              <img src="/static/1.jpg" alt="" />
              <span>{{
                name1[index]
              }}</span>
            </div>
            <div class="right">
              <van-icon name="like-o" />
              <span>{{ item.praiseNum }}</span>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Index",
  data() {
    return {
      img1: [],
      img2: [],
      value: null,
      content: [],
      count: 1,
      pageCount: 0,
      name1: [],
      name2: []
    };
  },
  methods: {
    nav(id) {
      this.$router.push(`/samsara/indexdetail/${id}`);
    },
    search() {
      if (this.value == null) {
        this.$dialog.alert({ message: "输入为空！" });
      } else {
        this.$router.push("/samsara/search/" + this.value);
      }
    },
    loadMore() {
      if (this.count <= this.pageCount) {
        this.count = this.count + 1;
        this.$post(this.$api.API_getDict, {
          dataType: 2,
          makeEmp: this.$store.getters.user.id,
          nowPage: this.count
        }).then(res => {
       
          this.$store.commit("addDict", res.data.rows_data);
          res.data.rows_data.forEach((val, index) => {
            if (index % 2) this.img2.push(val);
            else this.img1.push(val);
          });
          res.data.users.forEach((val, index) => {
            if (val != null) {
              if (index % 2) this.name1.push(val.nickname);
              else this.name2.push(val.nickname);
            } else {
              if (index % 2) this.name1.push("");
              else this.name2.push("");
            }
          });
        });
      } else return 0;
    }
  },
  mounted() {
    this.$post(this.$api.API_getDict, {
      dataType: 2,
      nowPage: 1,
      userName: this.$store.getters.user.userName
    }).then(res => {
      if (res.errorCode == 0) {
        this.pageCount = res.data.pageCount;
           console.log(res.data.rows_data)
        this.$store.commit("setDict", res.data.rows_data);
        res.data.rows_data.forEach((val, index) => {
          if (index % 2) this.img2.push(val);
          else this.img1.push(val);
        });
        res.data.users.forEach((val, index) => {
          if (val != null) {
            if (index % 2) this.name1.push(val.nickname);
            else this.name2.push(val.nickname);
          } else {
            if (index % 2) this.name1.push("");
            else this.name2.push("");
          }
        });

      } else {
        this.$dialog.alert({ message: "网络错误！" });
      }
    });
  }
};
</script>

<style lang="stylus" scoped>
.main{
  display flex;
  justify-content space-between
}
.col{
  display flex
  flex-direction column
  flex 1
}
.item{
  background #ffffff

  padding 5px
}
.img{
  width 100%;
  border-radius 10px 10px 0 0
}
.user{
  font-size .6em
  display flex
  justify-content space-between
  align-items center
}
.left{
  margin 3px
  display flex
  justify-content center
  align-items  center
}
.left>img{
  margin-right 5px
  height 20px
  width  20px
  border-radius 50%
}
.right{
   margin-right 5px
}
</style>
