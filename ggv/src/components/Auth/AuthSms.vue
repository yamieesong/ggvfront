<template>
  <div>
    <p>
      휴대폰 번호
      <input
        v-model="mbr_hp"
        id="JOIN_HP"
        type="text"
        name="mbr_hp"
        style="ime-mode: disabled"
        maxlength="14"
        placeholder="숫자만 입력해주세요"
        @input="formatPhoneNumber"
        @keypress="checkCode"
      />
      <button @click="runMethodAndStartTimer">인증번호 발송</button>
      <span v-if="timerRunning"
        >남은 시간: {{ minutes }}:{{ displaySeconds }}</span
      >
    </p>
  </div>
  <div>
    <p>
      인증번호
      <input v-model="authNum" />
      <button @click="autnCheck">확인</button>
    </p>
  </div>
  <div>
    <button v-if="showBtMail" @click="moveFindMail">메일로 찾기</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      mbr_hp: '',
      timerRunning: false,
      minutes: 3,
      seconds: 0,
      showBtMail: false,
      authNum: '',
    };
  },
  computed: {
    displaySeconds() {
      // displaySeconds computed 속성 추가
      return this.seconds < 10 ? `0${this.seconds}` : this.seconds;
    },
  },
  methods: {
    /* 휴대폰번호 포맷 변경 */
    formatPhoneNumber() {
      let cleanedPhoneNumber = this.mbr_hp.replace(/[^0-9]/g, '');

      // "-"을 적절한 위치에 추가하여 핸드폰 번호 형식으로 만들기
      let formattedPhoneNumber = '';
      for (let i = 0; i < cleanedPhoneNumber.length; i++) {
        if (i === 3 || i === 7) {
          formattedPhoneNumber += '-';
        }
        formattedPhoneNumber += cleanedPhoneNumber[i];
      }

      // formattedPhoneNumber을 다시 mbr_hp에 할당
      this.mbr_hp = formattedPhoneNumber;
    },

    /** 3분타이머 */
    /* 화면 */
    runMethodAndStartTimer() {
      // 인증번호 생성
      const verificationCode = Math.floor(1000 + Math.random() * 9000);
      // 인증번호 SMS 발송 요청
      this.sendVerificationCode(verificationCode);

      // 3분 타이머 시작
      this.timerRunning = true;
      const timerInterval = setInterval(() => {
        if (this.minutes === 0 && this.seconds === 0) {
          clearInterval(timerInterval); // 타이머 종료
          this.timerRunning = false;
          this.showBtMail = true;
          return;
        }
        if (this.seconds === 0) {
          this.minutes--;
          this.seconds = 59;
        } else {
          this.seconds--;
        }
      }, 1000);
    },

    /* 인증번호 SMS 발송 서버요청 */
    sendVerificationCode(code) {
      console.log(`인증번호 ${code}를 ${this.mbr_hp}로 전송합니다.`);
      this.axios
        .post(
          '/authSmsSend.do',
          new URLSearchParams({ authHp: this.mbr_hp, code: this.code })
        )
        .then((resp) => {
          let data = resp.data;
          console.log('인증결과' + data);
          if (data > 0) {
            this.$router.push('/rgstcplt/');

            this.$router.push(`/rgstcplt/${this.mbr_id}`);
          } else {
            alert('인증이 실패하였습니다. 잠시 후 다시 시도해주세요.');
            return;
          }
        })
        .catch(function (error) {
          console.log(error);
        });
    },

    /* 인증번호 입력 */
    autnCheck() {
      if (this.code === this.authNum) {
        alert('인증되었습니다.');
      } else {
        alert('인증번호가 일치하지 않습니다.');
        return;
      }
    },
  },
};
</script>
