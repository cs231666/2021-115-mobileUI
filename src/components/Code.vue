<template>
  <div>
    <mt-header title="班课信息">
      <router-link
        to="/classTeacherdetail/${this.$route.params.courseId}"
        slot="left"
      >
        <mt-button icon="back">班课详情</mt-button>
      </router-link>
    </mt-header>
    <div class="qrcode" ref="qrCodeUrl"></div>
    <mt-cell title="扫一扫加入云班课！"></mt-cell>
  </div>
</template>

<script>
import QRCode from "qrcodejs2";
export default {
  data() {
    return {};
  },

  methods: {
    creatQrCode() {
      console.log(this.$route.params.courseId);
      var qrcode = new QRCode(this.$refs.qrCodeUrl, {
        text: this.$route.params.courseId,
        width: 300,
        height: 300,
        colorDark: "#000000",
        colorLight: "#ffffff",
        correctLevel: QRCode.CorrectLevel.H,
      });
    },
  },
  mounted() {
    this.creatQrCode();
  },
};
</script>

<style scoped>
.qrcode {
  position: absolute;
  top: 30%;
  left: 50%;
  transform: translate(-50%, -50%);
}
.mint-cell {
  position: absolute;
  top: 60%;
  left: 50%;
  transform: translate(-50%, -50%);
}
</style>
