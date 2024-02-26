<template>
  <div id="background_board">
    <div class="login_form shadow" align="center" id="box">
      <div id="rgst">
        <AuthMenu></AuthMenu>
        <br />
        <br />
        <div id="bt">
          <AuthSms eventid="eventfindPW" :authtype="PW"></AuthSms>
        </div>
        <div id="bt">
          <button :disabled="btnNext" @click="editBtnPW">수정</button>
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
      btnNext: true,
      PW: 'PW',
    };
  },
  components: {
    AuthSms,
    AuthMenu,
  },
  mounted: function () {
    this.emitter.on('eventfindPW', (params) => {
      this.defaultvalue = params;
      if (this.defaultvalue) {
        this.btnNext = false;
      }
    });
  },
  beforeUnmount() {
    this.emitter.off('eventfindPW');
  },
  methods: {
    editBtnPW() {},
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
