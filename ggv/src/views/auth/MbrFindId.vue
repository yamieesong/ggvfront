<template>
  <div id="background_board">
    <div class="login_form shadow" align="center" id="box">
      <div id="rgst">
        <AuthMenu></AuthMenu>
        <p>개인회원 아이디 찾기</p>

        <div id="bt">
          <AuthSms eventid="eventfindID" :authtype="ID"></AuthSms>
        </div>
        <div id="bt">
          <button v-show="showBtn" @click="goscssid">다음단계</button>
          <button @click="gofindPw">비밀번호 찾기</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import AuthSms from '@/components/Auth/AuthSms.vue';
import AuthMenu from '@/components/Auth/AuthMenu.vue';

export default {
  data() {
    return {
      next: true,
      showBtn: false,
      ID: 'ID',
      mailURL: '',
      token: '',
    };
  },
  components: {
    AuthSms,
    AuthMenu,
  },
  mounted: function () {
    this.emitter.on('eventfindID', (params) => {
      this.defaultvalue = params.data.result;
      this.mailURL = params.data.mailURL;
      if (this.defaultvalue) {
        this.showBtn = true;
      }
    });
  },
  beforeUnmount() {
    this.emitter.off('eventfindID');
  },
  methods: {
    gofindPw() {
      this.$router.push('/MbrFindPw');
    },
    goscssid() {
      this.token = this.mailURL.split('/').pop();
      this.$router.push(`/rgstcplt/${this.token}`);
    },
  },
};
</script>

<style>
#margin-nav {
  margin-bottom: 30px;
}
#rgst {
  width: 1000px;
  height: 600px;
  transform: translate(15%, 14%);
}
#bt {
  margin-top: 50px;
}
</style>
