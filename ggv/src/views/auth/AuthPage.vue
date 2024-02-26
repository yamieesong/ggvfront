<template>
  <div id="background_board">
    <div class="login_form shadow" align="center" id="box">
      <div id="rgst">
        <AuthMenu></AuthMenu>
        <br />
        <br />
        <br />
        <div v-show="showSCSS">
          <p>회원님의 아이디는 {{ masking_id }}입니다.</p>
          <p>
            개인정보 도용에 대한 피해방지를 위하여 아이디 앞에서 네 자리 부터는
            ** 처리 합니다.
          </p>
          <p>
            아이디 전체 확인 버튼을 클릭하시면, 이메일로 아이디가 전송됩니다.
          </p>
          <button @click="sendID()">전체 아이디 확인</button>
        </div>

        <div v-show="showEXPI">
          <p>{{ msg }}</p>
        </div>

        <div v-show="showNON">
          <p>{{ msg }}</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import '@/assets/css/admin/login.css';
import axios from 'axios';
import emailjs from 'emailjs-com';
import AuthMenu from '@/components/Auth/AuthMenu.vue';

export default {
  props: ['token'],
  data() {
    return {
      mgs: '',
      mbr_id: '',
      mbr_mail: '',
      masking_id: '',
      showSCSS: false,
      showEXPI: false,
      showNON: false,
    };
  },
  components: {
    AuthMenu,
  },
  mounted() {
    /* 검증 */
    axios
      .post('/authMbr.do', new URLSearchParams({ token: this.token }))
      .then((resp) => {
        let data = resp.data;
        console.log('인증결과' + data.result);
        if (data.result == 'SUCCESS') {
          this.mbr_id = data.mbrID;
          this.masking_id = data.maskingID;
          this.mbr_mail = data.mbr_mail;
          this.showSCSS = true;
        } else if (data.result == 'EXPIRED') {
          this.msg = '완료된 인증링크입니다.';
          this.showEXPI = true;
          return;
        } else if (data.result == 'NON') {
          this.msg =
            'URL이 올바르지 않습니다.메일로 전달드린 인증링크로 접근해주세요.';
        } else {
          alert('잘못된 접근입니다.');
          return;
        }
      })
      .catch((error) => {
        console.error('서버 요청 에러:', error);
      });
  },
  methods: {
    sendID() {
      const payload = {
        mbr_id: this.mbr_id,
        mbr_mail: this.mbr_mail,
        form_time: new Date(),
      };
      emailjs.send('', '', payload, '').then(
        (res) => {
          console.log(res);
          if (res.status == 200) {
            alert('전체아이디가 메일로 발송되었습니다.');
          }
        },
        (error) => {
          console.log(error);
          alert('오류가 발생하였습니다.');
        }
      );
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
