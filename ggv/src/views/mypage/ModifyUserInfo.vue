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
                  v-model="lgn_Id"
                  id="EMP_ID"
                  type="text"
                  name="lgn_Id"
                  placeholder="010xxxxxxxx"
                  style="ime-mode: inactive"
                  @keypress="checkCode"
                />
              </p>
              <p>
                <label>메일주소</label>
                <input
                  v-model="lgn_Id"
                  id="EMP_ID"
                  type="text"
                  name="lgn_Id"
                  placeholder="xx@xx.com"
                  style="ime-mode: inactive"
                  @keypress="checkCode"
                />
              </p>
              <p>
                <label>비밀번호</label>
                <a @click="modPwd()">비밀번호 변경</a>
              </p>
              <div class="form-check" style="display: flex;">
                <div class="radioBtn">
                  <input class="form-check-input" type="radio" name="flexRadioDefault" id="flexRadioDefault1">
                  <label class="form-check-label" for="flexRadioDefault1">
                    동의
                  </label>
                </div>
                <div class="radioBtn" style="margin-left: 40px;">
                  <input class="form-check-input" type="radio" name="flexRadioDefault" id="flexRadioDefault2" checked>
                  <label class="form-check-label" for="flexRadioDefault2">
                    미동의
                  </label>
                </div>
              </div>
              <!-- Login Btn -->
              <a>수정</a>
              <a>취소</a>
            </fieldset>
          </form>
        </div>
        <!--// content -->
      </li>
    </ul>
  </div>
</template>

<script>
import { openModal } from "jenesius-vue-modal";
import modifyPasswordPop from "./ModifyPasswordPop.vue";

export default {
  data: function () {
    return {
      items: [],
      loginId: "",
      userNm: "",
    };
  },
  methods: {
    modPwd: async function () {
      //alert('비밀번호 변경 팝업 예정');

      //let action = 'I';

      const modal = await openModal(modifyPasswordPop, {
        title: "비밀번호 수정",
      });

      modal.onclose = () => {
        alert('창 닫음');
        //return false; //Modal will not be closed
      };
    },
  },
  mounted() {
    let loginInfo = this.$store.state.loginInfo;
    console.log("loginInfo", loginInfo)

    this.loginId = loginInfo.loginId;
    this.userNm = loginInfo.userNm;
  },
};
</script>