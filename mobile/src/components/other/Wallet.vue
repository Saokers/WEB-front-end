<template>
  <div>
    <mHeader>钱包充值</mHeader>
    <div style="text-align:left">
      <mt-cell title="电子钱包充值" />
      <mt-field
        label="当前余额"
        :value="'￥' + balance"
        disabled
        disableClear
      ></mt-field>
      <mt-field
        label="充值金额"
        placeholder="请输入充值金额!"
        required
        type="number"
        v-model="money"
      >
      </mt-field>
      <span class="am">*</span>
    </div>
    
    <mt-button
      type="danger"
      @click="changer"
      size="large"
      plain
      style="margin-top:30px"
      >确认充值</mt-button
    >

    <mt-cell>
      <div slot="icon" class="req">
        <span style="margin-left:8px">ID</span>
        <span style="margin-right:30px">充值金额</span>
        <span style="margin-right:45px">充值时间</span>
      </div>
    </mt-cell>

    <mt-cell v-for="(item, index) in row" :key="index">
      <div slot="icon" style="margin-bottom:10px" class="req">
        <span>{{ item.id }}</span>
        <span>{{ item.amount }}</span>
        <span>{{ item.updTime }}</span>
      </div>
    </mt-cell>
  </div>
</template>

<script>
import mHeader from "./header";
export default {
  data() {
    return {
      balance: null,
      money: null,
      row: []
    };
  },
  methods: {
    /**点击充值 */
    changer() {
      if (this.money > 0) {
        this.$post(this.$api.API_URL_CUSTOMER_Recharge_Account, {
          userId: this.$store.getters.user.id,
          amount: this.money
        }).then(res => {
          if (res.errorCode == 0) {
            this.$dialog.alert({ message: "充值成功" });
            this.getBalance();
            this.money = null;
          } else {
            this.$dialog.alert({ message: "网络超时！" });
          }
        });
      } else {
        this.$dialog.alert({ message: "请输入大于0的整数！" });
      }
    },
    /**查询余额 */
    getBalance() {
      this.getRecord();
      this.$post(this.$api.API_URL_CUSTOMER_Account, {
        userId: this.$store.getters.user.id
      }).then(res => {
        if (res.errorCode == 0) {
          this.balance = res.data.amount;
        } else {
          this.$dialog.alert({ message: "网络超时！" });
        }
      });
    },
    /**充值记录 */
    getRecord() {
      this.$post(this.$api.API_URL_CUSTOMER_Record, {
        userId: this.$store.getters.user.id
      }).then(res => {
        if (res.errorCode == 0) {
          this.row = res.data;
        } else {
          this.$dialog.alert({ message: "网络超时！" });
        }
      });
    }
  },
  created() {
    this.getBalance();
  },
  components: {
    mHeader
  }
};
</script>

<style lang="stylus" scoped>
.req{

  display:flex;
  justify-content:space-between;

}
.am{
  position absolute;
  top 154px
  left 3px
  color red

}
</style>
