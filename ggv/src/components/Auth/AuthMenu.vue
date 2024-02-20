<template>
  <div>
    <table id="aMenu">
      <tr>
        <td id="tLogo">
          <router-link :to="{ name: 'login' }">
            <img src="@/assets/images/admin/login/ggv_login_logo.png"
          /></router-link>
        </td>
        <td id="menu">
          <a
            ><router-link :to="{ name: 'Register' }"
              ><strong>회원가입</strong></router-link
            ></a
          >
          |
          <a
            ><router-link :to="{ name: 'MbrFindId' }"
              ><strong>아이디/비밀번호 찾기</strong></router-link
            ></a
          >
          |
          <a
            ><router-link :to="{ name: 'login' }"
              ><strong>로그인하기</strong></router-link
            ></a
          >
        </td>
      </tr>
    </table>
  </div>

  <!-- <div id="margin-nav">
    <p>
      <router-link
        :to="{ name: 'Register' }"
        class="text-success text-gradient font-weight-bold"
        >회원가입</router-link
      >
      | <span style="color: #41b982"> 아이디/비밀번호 찾기</span>
      | 로그인하기
    </p>
  </div> -->
</template>

<script>
export default {
  data() {
    return {
      rgst: '',
      find: '',
      loginGo: '',
      crtMenu: '',
    };
  },

  props: {
    menuType: String,
  },
};
</script>

<style>
#aMenu {
  width: 1000px;
  height: 50px;
  background-color: transparent;
}

#tLogo {
  width: 30%;
}
#menu {
  width: 70%;
  text-align: right;
}
</style>
