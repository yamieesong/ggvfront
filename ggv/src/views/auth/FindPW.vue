<template>
  <div id="background_board">
    <div class="login_form shadow" align="center" id="box">
      <div id="rgst">
        <AuthMenu></AuthMenu>
        <br />
        <br />
        <br />
        <div v-show="showSCSS">
          <p>아이디 : {{ mbr_id }}</p>
          <p>
            패스워드<input
              v-model="pw"
              id="PW_IN"
              :type="passwordFieldType"
              maxlength="16"
              @blur="pwValid"
            /><button @click="togglePasswordVisibility">
              {{ pwBtText }}
            </button>
          </p>
          <p v-if="!pwValidFlag">
            유효하지 않은 비밀번호입니다(8~16자리 대문자, 소문자, 숫자가 1개
            이상 존재하게 만들어주세요)
          </p>
          <p>
            패스워드 확인<input
              v-model="pwCheck"
              id="PW_CHECK"
              :type="passwordCkFieldType"
              maxlength="16"
              @blur="pwCheckValid"
            /><button @click="togglePasswordCkVisibility">
              {{ pwCkBtText }}
            </button>
          </p>
          <p v-if="!pwCheckValidFlag">비밀번호가 동일하지 않습니다.</p>
          <br />
          <br />
          <button v-if="pwCheckValidFlag" @click="modifyPW" :disabled="btn">
            비밀번호 변경
          </button>
        </div>

        <div v-show="showPWCOM">
          <p>비밀번호가 정상적으로 변경되었습니다.</p>
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
      pw: '',
      pwCheck: '',
      pwValidFlag: true,
      pwCheckValidFlag: true,
      showPassword: false,
      showPasswordCk: false,
      pwBtText: '보기',
      pwCkBtText: '보기',
      btn: true,
      mbr_no: '',
      showPWCOM: false,
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
          this.mbr_no = data.mbr_no;
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
  computed: {
    passwordFieldType() {
      return this.showPassword ? 'text' : 'password';
    },
    passwordCkFieldType() {
      return this.showPasswordCk ? 'text' : 'password';
    },
  },
  methods: {
    /* 검증 */
    isValidated: function () {
      let chk = this.checkNotEmpty([
        ['PW_IN', '비밀번호 입력해 주세요.'],
        ['PW_CHECK', '비밀번호를 확인을 진행해주세요.'],
      ]);
      return chk;
    },
    checkNotEmpty: function (arr) {
      for (var i = 0, len = arr.length; i < len; i++) {
        var elem = document.getElementById(arr[i][0]);
        console.log('elem is...');
        console.log(elem);
        if (elem.length <= 0) {
          continue;
        }
        var elemValue = elem.value;
        var alertMsg = arr[i][1];

        console.log(elemValue);
        if (elemValue == '') {
          alert(alertMsg);
          elem.focus();
          return false;
        }
      }
      return true;
    },
    pwCheckValid() {
      if (this.pw === this.pwCheck) {
        this.pwCheckValidFlag = true;
        this.btn = false;
      } else {
        this.pwCheckValidFlag = false;
      }
    },
    /* 포맷 */
    pwValid() {
      if (/^(?=.*[a-z])(?=.*[A-Z])(?=.*[0-9]).{8,16}$/.test(this.pw)) {
        this.pwValidFlag = true;
      } else {
        this.pwValidFlag = false;
      }
    },
    togglePasswordVisibility() {
      this.showPassword = !this.showPassword;
      this.pwBtText = this.showPassword ? '가리기' : '보기';
    },
    togglePasswordCkVisibility() {
      this.showPasswordCk = !this.showPasswordCk;
      this.pwCkBtText = this.showPasswordCk ? '가리기' : '보기';
    },

    /* axios */
    modifyPW() {
      let vm = this;
      axios
        .post(
          '/modifyPW.do',
          new URLSearchParams({ mbr_no: vm.mbr_no, new_pw: vm.pw })
        )
        .then((resp) => {
          let data = resp.data;
          console.log('비밀번호 변경 결과' + data);
          if (data > 0) {
            alert('성공');
            vm.showSCSS = false;
            vm.showPWCOM = true;
          } else {
            alert('오류가 발생하였습니다. 다시 시도해주세요');
            return;
          }
        })
        .catch((error) => {
          console.error('서버 요청 에러:', error);
        });
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
