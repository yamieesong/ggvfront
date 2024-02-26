<template>
  <div class="Rgstcplt">
    <div id="background_board">
      <div class="login_form shadow" align="center" id="box">
        <div id="rgst">
          <AuthMenu></AuthMenu>
          <p>가계뷰 회원가입 완료</p>
          <p>가입 아이디 : {{ $route.params.data }}</p>
          <p>
            <button>
              <router-link
                :to="{ name: 'login' }"
                class="text-success text-gradient font-weight-bold"
                >로그인하기</router-link
              >
            </button>
          </p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import AuthMenu from '@/components/Auth/AuthMenu.vue';

export default {
  components: {
    AuthMenu,
  },
  methods: {},
};
</script>
