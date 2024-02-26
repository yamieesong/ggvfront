<template>
  <div class="Register">
    <div id="background_board">
      <div class="login_form shadow" align="center" id="box">
        <div id="rgst">
          <AuthMenu></AuthMenu>

          <div>
            <p id="in">
              <label for="name">*이름</label>
              <input
                v-model="mbr_nm"
                id="NM"
                type="text"
                name="mbr_nm"
                @input="formKr"
                @keypress="checkCode"
              />
            </p>
            <p>
              <label for="phone">*휴대폰번호</label>
              <input
                v-model="mbr_hp"
                id="HP"
                type="text"
                name="mbr_hp"
                style="ime-mode: disabled"
                maxlength="13"
                placeholder="숫자만 입력해주세요"
                @input="formatPhoneNumber"
                @keypress="checkCode"
              />
            </p>
            <p>
              <label for="mail">*메일주소</label>
              <input id="MAIL" v-model="mbr_mail" @input="formEng" />@
              <input
                type="text"
                id="mailDirect"
                name="mailDirect"
                v-model="directMail"
                :disabled="selectedDomain !== 'direct'"
              />
              <select
                id="domain-list"
                v-model="selectedDomain"
                @change="handleDomainChange"
              >
                <option value="naver.com">naver.com</option>
                <option value="nate.com">nate.com</option>
                <option value="google.com">google.com</option>
                <option value="daun.net">daun.net</option>
                <option value="direct">직접입력</option>
              </select>
            </p>
            <p>
              <label for="id">아이디</label>
              <input
                v-model="mbr_id"
                id="ID"
                type="text"
                style="ime-mode: inactive"
                placeholder="숫자, 영문자 조합으로 6~20자리"
                @input="formId"
                :disabled="inputId"
              />
              <button @click="checkId" id="ID_CHECK">{{ idBtCheck }}</button>
              <button
                v-if="showUseBt"
                @click="useId"
                id="USE_ID"
                :disabled="useIdBt"
              >
                사용하기
              </button>
              <button
                v-if="showUnUseBt"
                @click="unUseId"
                id="UNUSE_ID"
                :disabled="unUseIdBt"
              >
                사용취소
              </button>
            </p>
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
            <p>
              <label for="agree">목표관리 알림 수신 동의</label>
              <input
                type="radio"
                id="agree"
                name="agree"
                value="Y"
                v-model="agree"
              />동의
              <input
                type="radio"
                id="disagree"
                name="agree"
                value="N"
                v-model="agree"
              />미동의
            </p>
            <span
              >재정 관리를 위해 [목표관리] 기능을 활용해보세요. 설정한 목표
              금액의 70% 도달 시 메일로 알려드립니다.</span
            ><br />
            <span
              >(지출목표 알림 %는 가입 시 70%가 기본값으로 설정되며,
              마이페이지를 통해 수정하실 수 있습니다.)</span
            >
            <p>
              <input
                type="checkbox"
                id="agree1"
                value="Y"
                v-model="agreements.agree1"
              />
              이용약관 동의 (보기)
            </p>
            <p>
              <input
                type="checkbox"
                id="agree2"
                value="Y"
                v-model="agreements.agree2"
              />
              개인정보 수집 이용 동의 (보기)
            </p>
            <p>
              <input
                type="checkbox"
                id="agree3"
                value="Y"
                v-model="agreements.agree3"
              />
              만 14세 이상입니다.
            </p>
            <p>
              <button @click="validateAndRegister">가입하기</button>
            </p>
          </div>
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
  data() {
    return {
      mbr_nm: '',
      mbr_hp: null,
      mbr_id: '',
      mbr_mail: '',
      directMail: '',
      selectedDomain: '',
      pw: '',
      pwCheck: '',
      pwValidFlag: true,
      pwCheckValidFlag: true,
      showPassword: false, // 비밀번호를 표시할지 여부를 제어할 데이터
      showPasswordCk: false,
      pwBtText: '보기',
      pwCkBtText: '보기',
      idBtCheck: '중복확인',
      useIdBt: false,
      unUseIdBt: false,
      inputId: false,
      showUseBt: false,
      showUnUseBt: false,
      agree: '', // 목표관리수신알림
      agreements: {
        // 필수동의항목
        agree1: false,
        agree2: false,
        agree3: false,
      },
    };
  },
  computed: {
    /** 비밀번호 input 타입 */
    // 비밀번호
    passwordFieldType() {
      return this.showPassword ? 'text' : 'password';
    },
    // 비밀번호 확인
    passwordCkFieldType() {
      return this.showPasswordCk ? 'text' : 'password';
    },
  },

  methods: {
    /** validation check */
    isValidated: function () {
      let chk = this.checkNotEmpty([
        ['NM', '이름를 입력해 주세요.'],
        ['HP', '휴대폰번호를 입력해 주세요.'],
        ['MAIL', '메일주소를 입력해 주세요.'],
        ['mailDirect', '메일주소 도메인을 입력해 주세요.'],
        ['ID', '아이디를 입력해 주세요.'],
        ['PW_IN', '비밀번호 입력해 주세요.'],
        ['PW_CHECK', '비밀번호를 확인을 진행해주세요.'],
      ]);
      return chk;
    },
    /* id duplication */
    isValidatedId: function () {
      let chk = this.checkNotEmpty([['ID', '아이디를 입력해 주세요.']]);
      return chk;
    },
    /* null check */
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

    /** 텍스트 입력 포맷 */
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
    /* 한글 */
    formKr() {
      this.mbr_nm = this.mbr_nm.replace(/[^ㄱ-ㅎㅏ-ㅣ가-힣]/g, '');
    },
    /* 영어 */
    formEng() {
      this.mbr_id = this.mbr_id.replace(/[^a-zA-Z]/g, '');
    },
    /* 아이디 */
    formId() {
      const regex = /^[a-zA-Z0-9]{8,15}$/;
      if (!regex.test(this.mbr_id)) {
        this.mbr_id = this.mbr_id.slice(0, 15).replace(/[^a-zA-Z0-9]/g, '');
      }
    },

    /** 메일주소 */
    handleDomainChange() {
      if (this.selectedDomain !== 'direct') {
        // "직접입력"이 아닌 다른 옵션을 선택했을 때, directMail에 선택한 도메인을 할당
        this.directMail = this.selectedDomain;
      } else {
        // "직접입력"을 선택했을 때,directMail을 비워두고 입력 필드에 포커스를 맞춤
        this.directMail = '';
        this.$nextTick(() => {
          this.$refs.mailDirect.focus();
        });
      }
    },

    /** 비밀번호 */
    /* 비밀번호 포맷 확인 */
    pwValid() {
      if (/^(?=.*[a-z])(?=.*[A-Z])(?=.*[0-9]).{8,16}$/.test(this.pw)) {
        this.pwValidFlag = true;
      } else {
        this.pwValidFlag = false;
      }
    },
    /* 비밀번호 일치 여부 확인 */
    pwCheckValid() {
      if (this.pw === this.pwCheck) {
        this.pwCheckValidFlag = true;
      } else {
        this.pwCheckValidFlag = false;
      }
    },
    /** 버튼 */
    /* 패스워드 버튼 */
    togglePasswordVisibility() {
      this.showPassword = !this.showPassword;
      this.pwBtText = this.showPassword ? '가리기' : '보기';
    },
    togglePasswordCkVisibility() {
      this.showPasswordCk = !this.showPasswordCk;
      this.pwCkBtText = this.showPasswordCk ? '가리기' : '보기';
    },

    /** 아이디 */
    /* 아이디 중복 확인 */
    checkId: function () {
      if (this.isValidatedId()) {
        this.axios
          .post('/register.do', new URLSearchParams({ loginId: this.mbr_id }))
          .then((resp) => {
            let data = resp.data;
            console.log('조회결과' + data);
            if (data > 0) {
              alert(this.mbr_id + '은 사용중인 ID입니다.');
            } else {
              alert(this.mbr_id + '은 사용 가능한 ID입니다.');
              this.showUseBt = true;
              this.useIdBt = false;
              this.showUnUseBt = false;
            }
          })
          .catch(function (error) {
            console.log(error);
          });
      }
    },
    useId: function () {
      this.inputId = true;
      this.showUnUseBt = true; // 사용취소 버튼 보이기
      this.useIdBt = true;
    },
    unUseId: function () {
      this.inputId = false;
      this.useIdBt = false; // 사용하기 버튼 비활성화
      this.unUseIdBt = false;
      this.mbr_id = ''; // 입력란 초기화
      this.showUnUseBt = false; // 사용취소 버튼 숨기기
      this.showUseBt = false;
    },

    /** 회원가입 */
    /* 입력항목 validation check */
    checkCode: function (event) {
      if (event.keyCode == 13) this.validateAndRegister();
    },

    validateAndRegister: function () {
      if (!this.isValidated()) {
        return;
      }

      if (!this.agree) {
        alert('목표관리 알림 수신 동의를 선택하세요.');
        return;
      }

      if (
        !this.agreements.agree1 ||
        !this.agreements.agree2 ||
        !this.agreements.agree3
      ) {
        alert('필수 약관에 동의해야 합니다.');
        return;
      }

      this.registerMember();
    },

    /* 회원가입 */
    registerMember() {
      console.log('parameter 확인(아이디) ::: ' + this.mbr_id);
      this.axios
        .post(
          '/rgstMbrGgv.do',
          new URLSearchParams({
            joinNm: this.mbr_nm,
            joinHp: this.mbr_hp,
            joinMail: this.mbr_mail + '@' + this.selectedDomain,
            joinId: this.mbr_id,
            joinPw: this.pw,
            joinPushYn: this.agree,
          })
        )
        .then((resp) => {
          let data = resp.data;
          console.log('가입결과' + data);
          if (data > 0) {
            this.$router.push('/rgstcplt/');

            this.$router.push(`/rgstcplt/${this.mbr_id}`);
          } else {
            alert('잠시 후 다시 시도해주세요.');
            return;
          }
        })
        .catch(function (error) {
          console.log(error);
        });
    },
  },
};
</script>

<style>
#logo {
  margin-bottom: 50px;
}
#description {
  color: red;
  font-size: small;
}
#margin-nav {
  margin-bottom: 30px;
}
#rgst {
  width: 1000px;
  height: 600px;
  transform: translate(15%, 14%);
}
</style>
