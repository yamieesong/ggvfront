<template>
  <div id="container">
    <ul>
      <li class="contents">
        <!-- contents -->
        <!-- content -->
        <div class="content">
          <p class="Location">
            <a class="btn_set home">메인으로</a>
            <a class="btn_nav bold">마이페이지</a>
            <span class="btn_nav bold">회원정보</span>
          </p>

          <p class="conTitle">
            <span>회원정보</span>
          </p>

          <form id="myForm" action="" method="post">
            <fieldset>
              <p>
                <label>아이디</label>
                <input
                  v-model="loginId"
                  type="text"
                  readonly
                />
              </p>
              <p>
                <label>이름</label>
                <input
                  v-model="userNm"
                  type="text"
                  readonly
                />
              </p>
              <p>
                <label>휴대폰번호</label>
                <input
                  v-model="hp"
                  id="hp"
                  type="text"
                  name="hp"
                  placeholder="010xxxxxxxx"
                  style="ime-mode: inactive"
                  @keypress="checkCode"
                />
              </p>
              <p>
                <label>메일주소</label>
                <input
                  v-model="email"
                  id="email"
                  name="email"
                  placeholder="xx@xx.com"
                  style="ime-mode: inactive"
                  @keypress="checkCode"
                />
              </p>
              <p>
                <label>비밀번호</label>
                <input v-model="pw" />
                <!--                <a @click="modPwd()">비밀번호 변경</a>-->
              </p>
              <p>
                <label>비밀번호확인</label>
                <input v-model="pwConfirm" />
              </p>
              <div class="form-check" style="display: flex;">
                <div class="radioBtn">
                  <input class="form-check-input" type="radio" name="flexRadioDefault" id="flexRadioDefault1" value="Y"
                         v-model="alarmYn">
                  <label class="form-check-label" for="flexRadioDefault1">
                    동의
                  </label>
                </div>
                <div class="radioBtn" style="margin-left: 40px;">
                  <input class="form-check-input" type="radio" name="flexRadioDefault" id="flexRadioDefault2" value="N"
                         v-model="alarmYn">
                  <label class="form-check-label" for="flexRadioDefault2">
                    미동의
                  </label>
                </div>
              </div>
              <p v-if="alarmYn == 'Y'">
                <label>목표설정</label>
                <input
                  v-model="goal"
                  id="EMP_ID"
                  type="text"
                  name="lgn_Id"
                  placeholder="ex) 70%"
                />
              </p>
              <!-- Login Btn -->
              <button type="button" class='btn btn-primary' @click="updateUser()">수정</button>
              <button type="button" class="btn btn-danger">취소</button>
            </fieldset>
          </form>
        </div>
        <!--// content -->
      </li>
    </ul>
  </div>
</template>

<script>
import { openModal } from 'jenesius-vue-modal';
import modifyPasswordPop from './ModifyPasswordPop.vue';

export default {
  data: function() {
    return {
      items: [],
      loginId: '',
      userNm: '',
      alarmYn: 'N',
      goal: '',
      email: '',
      hp: '',
      pw: '',
      pwConfirm: '',
    };
  },
  methods: {
    modPwd: async function() {
      //alert('비밀번호 변경 팝업 예정');

      //let action = 'I';

      const modal = await openModal(modifyPasswordPop, {
        title: '비밀번호 수정',
      });

      modal.onclose = () => {
        alert('창 닫음');
        //return false; //Modal will not be closed
      };
    },

    updateUser: async function() {
      console.log(this.hp);
      console.log(this.email);
      console.log(this.pw);
      console.log(this.pwConfirm);

      if (this.pw != this.pwConfirm) {
        alert('비밀번호와 비밀번호확인이 같지않음');
        return;
      }

      let params = new URLSearchParams();
      params.append('loginId', this.$store.state.loginInfo.loginId);
      params.append('hp', this.hp);
      params.append('email', this.email);
      params.append('pw', this.pw);
      params.append('alarmYn', this.alarmYn);

      if (confirm('수정?')) {
        await this.axios
          .post('/mypage/updateUser.do', params)
          .then(function(res) {
            console.log(res);
            alert('수정성공');
          });
      }
    },
  },
  mounted() {
    let loginInfo = this.$store.state.loginInfo;
    console.log('loginInfo', loginInfo);

    this.loginId = loginInfo.loginId;
    this.userNm = loginInfo.userNm;
  },
};
</script>