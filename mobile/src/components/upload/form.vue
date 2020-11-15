<template>
  <div class="form">
    <mHeader>提交动态</mHeader>
    <div class="img">
      <img :src="item" v-for="(item, index) in src" :key="index" />
    </div>
    <div>
      <mt-cell
        class="cell"
        @click.native="show = true"
        :title="title"
        is-link
      />
      <mt-cell
        class="cell"
        :title="item.name"
        v-for="(item, index) in result"
        :key="index"
        is-link
      />
      <mt-field
        rows="4"
        v-model="values"
        type="textarea"
        placeholder="这一刻，想说点什么..."
      ></mt-field>
    </div>
    <mt-popup class="popup" position="bottom" v-model="show">
      <van-checkbox-group v-model="result">
        <van-cell
          v-for="(item, index) in colunms"
          clickable
          :key="item.id"
          @click="toggle(index)"
          :title="item.name"
        >
          <van-checkbox
            :name="item"
            ref="checkboxes"
            slot="right-icon"
          ></van-checkbox>
        </van-cell>
      </van-checkbox-group>
    </mt-popup>

    <van-button @click="send" type="primary" size="large">发布笔记</van-button>
  </div>
</template>

<script>
import mHeader from "../other/header";

export default {
  name: "vForm",
  components: { mHeader },
  data() {
    return {
      title: "选择标签",
      src: [],
      show: false,
      result: [],
      colunms: [],
      values: ""
    };
  },
  mounted() {
    this.$post(this.$api.API_getLabel, {
      dictType: 10
    }).then(res => {
      if (res.errorCode == 0) {
        this.colunms = res.data.map(val => {
          return { id: val.id, name: val.dictName };
        });
      }
    });
    this.src = this.$store.state.file;

    this.attachment_rows_data = this.Storage.Local.get("resources");
  },
  methods: {
    toggle(index) {
      this.$refs.checkboxes[index].toggle();
    },
    send() {
      let list = this.result.map(val => {
        return { dataDictId: val.id };
      });

      var attachment;
      attachment = {
        attachmentName: this.attachment_rows_data[0].attachmentName,
        attachmentPath: this.attachment_rows_data[0].attachmentPath
      };

      let attachment_rows_data = [];
      attachment_rows_data.push(attachment);

      this.$post(this.$api.API_SET_NOTEDATA, {
        makeEmp: this.$store.getters.user.id,
        label_rows_data: list,
        attachment_rows_data: attachment_rows_data,
        noteContent: this.values,
        dataDictId: 0
      }).then(res => {
        if (res.data != null) this.$router.push("/");
        else this.$dialog.alert({ message: "网路错误！" });
      });
    }
  },
  watch: {}
};
</script>

<style lang="stylus" scoped>
.img{
  display flex
  flex-wrap wrap
}
img{
  width 100px
  height 100px
  border-radius 10%
  margin 10px
}
.cell{
  text-align left
}
.popup{
  width 100%
}
</style>
