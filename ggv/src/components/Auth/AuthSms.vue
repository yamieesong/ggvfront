<template>
  <div>
    <form id="myform">
      <ul>
        <li>
          <input
            v-model="selectedOption"
            type="radio"
            name="myradio"
            value="checkMAIL"
          />이메일
        </li>
      </ul>
    </form>

    <div v-show="selectedOption === 'checkMAIL'">
      <p>가입 시 입력한 이메일주소 입력해주세요</p>
      <input v-model="mbr_mail" id="MAIL" type="text" @input="formMail" />
      <button :disabled="authMailBtn" @click="testMethod()">
        이메일로 인증 받기
      </button>

      <p v-show="ntcmail">
        이메일 형식이 올바르지 않습니다. 다시 한 번 확인 해주세요
      </p>

      <div>
        <p>
          인증번호
          <input v-model="authNumMail" />
          <button @click="autnCheckMail">확인</button>
        </p>
      </div>
    </div>
  </div>
</template>

<script>
import emailjs from 'emailjs-com'; //npm i emailjs-com로 설치

export default {
  props: ['eventid', 'authtype'],
  data() {
    return {
      mbr_hp: '',
      timerRunning: false,
      minutes: 0,
      seconds: 15,
      authNum: '',
      authNumMail: '',
      authCode: '',
      authCodeMail: '',
      useSendAuth: false,
      showReCode: false,
      mbr_mail: '',
      authMailBtn: true,
      ntcmail: false,
      authvalue: false,
      selectedOption: '',
      authmailaddr: '',
      mbrno: '',
    };
  },
  methods: {
    /* 휴대폰번호 */
    formatPhoneNumber() {
      let cleanedPhoneNumber = this.mbr_hp.replace(/[^0-9]/g, '');
      let formattedPhoneNumber = '';
      for (let i = 0; i < cleanedPhoneNumber.length; i++) {
        if (i === 3 || i === 7) {
          formattedPhoneNumber += '-';
        }
        formattedPhoneNumber += cleanedPhoneNumber[i];
      }
      this.mbr_hp = formattedPhoneNumber;
    },

    /* 메일 */
    formMail() {
      const regex =
        /^[0-9a-zA-Z]([-_.]?[0-9a-zA-Z])*@[0-9a-zA-Z]([-_.]?[0-9a-zA-Z])*.[a-zA-Z]{2,3}$/i;
      if (!regex.test(this.mbr_mail) || !this.mbr_mail) {
        this.authMailBtn = true;
        this.ntcmail = true;
      } else {
        this.authMailBtn = false;
        this.ntcmail = false;
      }
    },

    /* 검증 */
    isValidatedId: function () {
      let chk = this.checkNotEmpty([['MAIL', '메일주소를 입력해 주세요.']]);
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
    autnCheckMail() {
      console.log(this.authNumMail);
      if (this.authCodeMail == this.authNumMail) {
        if (this.eventid == 'eventfindID') {
          this.token = this.authmailaddr.split('/').pop();
          this.$router.push(`/auth/${this.token}`);
        } else if (this.eventid == 'eventfindPW') {
          this.token = this.authmailaddr.split('/').pop();
          this.$router.push(`/findPW/${this.token}`);
        } else {
          alert('잘못된 접근입니다.');
          return;
        }
        alert('인증되었습니다.');
      } else {
        alert('인증번호가 일치하지 않습니다.');
        return;
      }
    },

    /* axios */
    testMethod() {
      this.axios
        .post(
          '/mbrChekMail.do',
          new URLSearchParams({ mbr_mail: this.mbr_mail })
        )
        .then((resp) => {
          let data = resp.data;
          console.log('회원조회(메일) 결과 ::: ' + data.result);

          if (data.result == 'SUCCESS') {
            console.log('가계뷰회원(메일) ::: ' + this.mbr_mail);
            this.mbrno = data.mbr_no;
            this.mailToken();
          } else {
            alert('존재하지 않는 회원입니다. 메일주소를 확인해주세요.');
            return;
          }
        })
        .catch(function (error) {
          console.log(error);
        });
    },
    mailToken() {
      console.log('mailToken 진입' + this.authtype);
      if (this.isValidatedId(this.mbr_mail)) {
        this.axios
          .post(
            '/token.do',
            new URLSearchParams({
              mbr_mail: this.mbr_mail,
              mbr_no: this.mbrno,
              authtype: this.authtype,
            })
          )
          .then((resp) => {
            let data = resp.data;
            console.log('인증결과' + data.result);

            if (data.result == 'SUCCESS') {
              this.code = data.authNum;
              alert('인증번호 메일 발송 성공');
              console.log('인증링크' + data.mailURL);
              console.log('인증번호' + data.mailCODE);
              this.authCodeMail = data.mailCODE;
              this.authmailaddr = data.mailURL;
              //this.sendRequestEmail();
            } else {
              alert(
                '인증번호 발송 실패. 잠시 뒤 다시 요청해주세요. 오류가 지속되면 가계뷰로 문의해주세요.'
              );
              return;
            }
          })
          .catch(function (error) {
            console.log(error);
          });
      }
    },
    sendRequestEmail() {
      if (this.authtype === 'ID') {
        const payload = {
          mbr_mail: this.mbr_mail,
          mailCODE: this.authCodeMail,
          mailURL: this.authmailaddr,
          form_time: new Date(),
        };
        emailjs.send('', '', payload, '').then(
          (res) => {
            console.log(res);
          },
          (error) => {
            console.log(error);
          }
        );
      }
      if (this.authtype === 'PW') {
        const payload = {
          mbr_mail: this.mbr_mail,
          mailCODE: this.authCodeMail,
          mailURL: this.authmailaddr,
          form_time: new Date(),
        };
        emailjs.send('', '', payload, '').then(
          (res) => {
            console.log(res);
          },
          (error) => {
            console.log(error);
          }
        );
      }
    },
  },
};
</script>
