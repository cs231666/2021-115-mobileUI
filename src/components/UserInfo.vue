<template>
  <div class="mineCom">
    <mt-header
      style="-webkit-transform: translateZ(0)"
      slot="header"
      class="primary_bg"
      title="我的"
      color="black"
      :fixed="true"
    >
      <mt-button slot="left">
        <img src="..\assets\reminder.png" style="width: 17px" alt="" />
      </mt-button>
      <mt-button slot="right">
        <img src="..\assets\icon\set.png" style="width: 20px" alt="" />
      </mt-button>
    </mt-header>
    <div slot="content">
      <section class="content has-header">
        <section class="header primary_bg">
          <section class="title">
            <h3 class="fn-16">未设置</h3>
            <span class="fn-13">13188888888</span>
          </section>
          <div class="right">
            <img src="..\assets\icon\right-w.png" alt="" />
          </div>
        </section>
        <div class="content-tabs">
          <div>
            <h3>
              0
              <span class="fn-10">分</span>
            </h3>
            <span>经验值</span>
          </div>
          <div>
            <h3>
              0
              <span class="fn-10">个</span>
            </h3>
            <span>加入班课数</span>
          </div>
          <div>
            <h3>
              0
              <span class="fn-10">个</span>
            </h3>
            <span>创建班课数</span>
          </div>
        </div>
        <br />
        <section class="content-list">
          <mt-cell
            is-link
            :title="item.title"
            v-for="item in list"
            :key="item.text"
          >
            <span>{{ item.text }}</span>
            <img slot="icon" :src="item.img" width="20" height="20" />
          </mt-cell>
        </section>
      </section>
      <br />
      <section class="content-list">
        <mt-cell
          is-link
          :title="item.title"
          v-for="item in list_two"
          :key="item.text"
        >
          <span>{{ item.text }}</span>
          <img slot="icon" :src="item.img" width="20" height="20" />
        </mt-cell>
      </section>
      <br />
      <section class="content-list">
        <mt-cell
          is-link
          :title="item.title"
          v-for="item in list_three"
          :key="item.text"
        >
          <span>{{ item.text }}</span>
          <img slot="icon" :src="item.img" width="20" height="20" />
        </mt-cell>
      </section>
      <mt-cell title="姓名" v-model="userInfo.realname"></mt-cell>
       <mt-cell title="学号" v-model="userInfo.studentId"></mt-cell>
      <mt-cell title="性别" v-model="userInfo.sex"></mt-cell>
      <mt-cell title="身份">{{userInfo.role ? '老师' : '学生'}}</mt-cell>
      <mt-cell title="所在学校" v-model="userInfo.org"></mt-cell>
	  <mt-cell
	    title="修改信息"
	    @click.native="updateInformation"
	    is-link
	  ></mt-cell>
      <mt-cell
        title="修改密码"
        :to="{ path: '/editpassword', params: { phone: userInfo.phone } }"
        is-link
      ></mt-cell>
    </div>
    <mt-button type="primary" @click.native="logout" size="large"
      >退出登录</mt-button
    >
    <mt-tabbar :selected.sync="selected">
      <mt-tab-item id="班课">
        <img slot="icon" src="../assets/banke.png" />
        <router-link to="/class">
          <font size="3">班课</font>
        </router-link>
      </mt-tab-item>
      <mt-tab-item id="发现">
        <img slot="icon" src="../assets/faxian.png" />
        <router-link to="/search">
          <font size="3">发现</font>
        </router-link>
      </mt-tab-item>
      <mt-tab-item id="我的">
        <img slot="icon" src="../assets/wode.png" />
        <router-link to="/userinfo">
          <font size="3">我的</font>
        </router-link>
      </mt-tab-item>
    </mt-tabbar>
  </div>
</template>

<script>
import moment from "moment";
import { MessageBox } from "mint-ui";
import { getUserInfo } from "@/api/user.js";
export default {
  data() {
    return {
      selected: "1",
      isScroll: true,
      transitionName: "slideInRight",
      showHome: false,
      positons: {},
      userInfo: {
        name: "",
        studentId:"",
        sex: "",
        tel: "",
        identity: "",
        org: "未设置",
      },
      list: [],
      list_two: [],
      list_three: [],
    };
  },
  activated() {
    this.getUserInfo();
  },
  watch: {
    ["$store.state.common.popObj"]: {
      handler(newVal) {
        this.isScroll = true;
        for (var key in newVal) {
          if (newVal[key]) {
            this.isScroll = false;
          }
        }
        this.$store.commit("SHOW_FOOTER", this.isScroll);
      },
      deep: true,
    },

    selected(newVal, oldVal) {
      this.$store.commit("TAB_SELECTED", newVal);
      this.transitionName = oldVal < newVal ? "slideInRight" : "slideInLeft";
      this.$router.currentRoute.name !== "home" &&
        this.$router.push({ name: "baseHome" });
    },
  },
  methods: {
    //滚动到相对位置
    toPositon(key) {
      document
        .getElementById("scroll-content")
        .scrollTo(0, this.positons[key] ? this.positons[key].y : 0);
    },
    //保存位置信息
    savePositon(key) {
      this.positons[key] = {
        y:
          document.getElementById("scroll-content") &&
          document.getElementById("scroll-content").scrollTop,
      };
    },
    //时间格式化
    dateFormat(data) {
      var date = data;
      if (date == undefined) {
        return "";
      }
      return moment(date).format("YYYY-MM-DD HH:mm:ss");
    },
    async getUserInfo() {
      getUserInfo().then((res) => {
        if (res.data.code != 200) {
          MessageBox("提示", "获取用户列表失败");
        } else {
          this.userInfo = res.data.obj;
          localStorage.setItem("realname", res.data.obj.realname);
          localStorage.setItem("role", res.data.obj.role);
          localStorage.setItem("studentId", res.data.obj.studentId);
          localStorage.setItem("id", res.data.obj.id);
        }
        if(this.userInfo.realname == null){
          MessageBox.alert("姓名不能为空","提示").then(action => {
			      this.$router.push({ name: "EditUserInfo", params: { phone:this.userInfo.phone } });
			    });
        }
        else if(this.userInfo.studentId == null){
          MessageBox.alert("学工号不能为空","提示").then(action => {
			      this.$router.push({ name: "EditUserInfo", params: { phone:this.userInfo.phone } });
			    });
        }
      });
    },
    //退出登录
    logout() {
      this.cookie.clearCookie("sid");
      window.localStorage.removeItem("sid");
      this.$router.push("/login");
    },
    updateInformation(){
      this.$router.push({ name: "EditUserInfo", params: { phone:this.userInfo.phone } });
    },
  },
};
</script>

<style scoped lang="scss">
.scroll-content {
  z-index: 0; // -webkit-overflow-scrolling: touch;
  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0; // z-index: 1;
  display: block;
  overflow-x: hidden;
  -webkit-overflow-scrolling: touch;
  will-change: scroll-position;
  contain: size style layout;
}
.mineCom {
  .content-list {
    .mint-cell-value {
      span {
        font-size: 13px;
      }
    }
  }
  .content {
    .content-tabs {
      display: flex;
      background-color: white;
      div {
        padding: 12px 0px;
        text-align: center; // display: flex;
        // align-items: center;
        // height: $hei;
        text-align: center;
        border-right: 1px solid rgb(229, 229, 229);
        flex: 1;
        h3 {
          font-size: 20px;
          color: rgb(244, 180, 21);
        }
        &:last-child {
          border-right: none;
        }
      }
    }
    .header {
      min-height: 80px;
      width: 100%;
      display: flex;
      align-items: center;
      .left {
        flex: 0.5;
        text-align: left;
        img {
          width: 50px;
          height: 50px;
          margin-left: 20px;
          margin-top: 40px;
          border-radius: 50%;
        }
      }
      .title {
        flex: 1;
        color: white;
      }
      .right {
        flex: 0.5;
        text-align: right;
        img {
          width: 16px;
          margin-right: 20px;
        }
      }
    }
  }
}
.infoDiv {
  margin-top: 10%;
  margin-left: 10%;
  margin-right: 10%;
  margin-bottom: 10%;
  border: 1px solid #96c2f1;
  background: #eff7ff;
  border-radius: 5px;
}
.UserInfoContainer {
  width: 100%;
  height: 100%;
  background: #fff;
}
</style>
