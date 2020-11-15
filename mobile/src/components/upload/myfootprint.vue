<template>
  <div>
    <mHeader>我的足迹</mHeader>
    <van-tabs v-model="active">
      <van-tab title="今天">
        
      </van-tab>
      <van-tab title="历史">
        
      </van-tab>
    </van-tabs>
  </div>
</template>
<script>
import mHeader from "../other/header";
export default {
  name: "myFootPrint",
  data() {
    return {
      active: null,
      todayfoot: [],
      historyfoot: []
    };
  },
  created() {
    this.getfootprint();
  },
  methods: {
    getfootprint() {
      this.$post(this.$api.API_URL_FOOTPRINT_SELECT, {
        userId: this.$store.getters.user.id
      }).then(res => {
        this.todayfoot = res.today;
        this.historyfoot = res.history;
      });
    }
  },
  components: { mHeader }
};
</script>

<style>

</style>
