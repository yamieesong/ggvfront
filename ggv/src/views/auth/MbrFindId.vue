<template>
  <div id="background_board">
    <div class="login_form shadow" align="center" id="box">
      <div id="rgst">
        <AuthMenu></AuthMenu>
        <p>개인회원 아이디 찾기</p>
        <p>가입 시 입력한 휴대폰 번호를 입력해주세요</p>

        <div id="bt">
          <AuthSms></AuthSms>
        </div>
        <div id="bt">
          <button>다음단계</button> <button>비밀번호 찾기</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import AuthSms from '@/components/Auth/AuthSms.vue';
import AuthMenu from '@/components/Auth/AuthMenu.vue';

export default {
  components: {
    AuthSms,
    AuthMenu,
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
