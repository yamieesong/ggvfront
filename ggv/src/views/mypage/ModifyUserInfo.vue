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
                <span v-html="loginId" style="margin-left: 10px"></span>
<!--                <input-->
<!--                  v-model="loginId"-->
<!--                  type="text"-->
<!--                  readonly-->
<!--                />-->
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
                  v-model="email"
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
                <input v-model="pw" />
                <!--                <a @click="modPwd()">비밀번호 변경</a>-->
              </p>
              <p>
                <label>비밀번호확인</label>
                <input v-model="pwConfirm" />
              </p>
              <div class="form-check" style="display: flex;">
                <div class="radioBtn">
                  <input class="form-check-input" type="radio" name="flexRadioDefault" id="flexRadioDefault1" value="Y" v-model="alarmYn">
                  <label class="form-check-label" for="flexRadioDefault1">
                    동의
                  </label>
                </div>
                <div class="radioBtn" style="margin-left: 40px;">
                  <input class="form-check-input" type="radio" name="flexRadioDefault" id="flexRadioDefault2" value="N" v-model="alarmYn">
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
                  placeholder="70 (%단위 입니다.)"
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
import { openModal } from "jenesius-vue-modal";
import modifyPasswordPop from "./ModifyPasswordPop.vue";

export default {
  data: function () {
    return {
      items: [],
      userInfo: [],
      loginId: "",
      userNm: "",
      alarmYn: "N",
      goal:"",
      email: '',
      hp: '',
      pw: '',
      pwConfirm: '',
      year: "",
      month: "",
      userNo: "",
    };
  },
  watch: {
    alarmYn: {
      immediate: false,
      handler(newVal, oldVal){
        if(newVal === "N"){
          this.goal = 0;
        }
        this.alarmYn = newVal;
        //alert(this.goal)
      }
    }
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

    updateUser: async function() {
      console.log('updateUser start')
      console.log(this.hp);
      console.log(this.email);
      console.log(this.pw);
      console.log(this.pwConfirm);
      
      if (this.pw === "") {
        alert('비밀번호를 입력해주세요.');
        return;
      }
      if (this.pw != this.pwConfirm || this.pw === "") {
        alert('비밀번호와 비밀번호확인이 같지않음');
        return;
      }

      let params = new URLSearchParams();
      params.append('loginId', this.$store.state.loginInfo.loginId);
      params.append('hp', this.hp);
      params.append('email', this.email);
      params.append('pw', this.pw);
      params.append('alarmYn', this.alarmYn);

      let params2 = new URLSearchParams();
      params2.append('goal', this.goal);
      params2.append('mbr_no', this.userNo);
      params2.append('goal_yr', this.year);
      params2.append('goal_m', this.month);
      //console.log("updateUserGaol params2", this.year, this.month, this.goal)

      if (confirm('수정?')) {

        await this.axios
          .post('/mypage/updateUser.do', params)
          .then(function(res) {
            console.log(res);
            //alert('수정성공');
          });

        await this.axios
          .post('/mypage/updateUserGoal.do', params2)
          .then(function(res) {
            console.log(res);
            //alert('수정성공2');
          });
      }
    },
  },
  mounted() {
    let loginInfo = this.$store.state.loginInfo;
    console.log("loginInfo", loginInfo)

    this.loginId = loginInfo.loginId;
    this.userNm = loginInfo.userNm;

    let date = new Date();
    let year = date.getFullYear().toString();
    let month = (date.getMonth() + 1).toString();
    this.year = year;
    this.month = month;



    let params = new URLSearchParams();
    params.append("loginId", loginInfo.loginId);
    params.append("goal_yr", year);
    params.append("goal_m", month);

    this.axios
      .post("/mypage/getMypageUserInfo.do", params)
      .then(function (res) {
        this.userInfo = res.data.getMypageUserInfo;
        console.log("this.userInfo", this.userInfo)
        //console.log("this.userInfo[0]", this.userInfo[0].loginID)
        this.loginId = this.userInfo[0].loginID;
        this.userNm = this.userInfo[0].name;
        this.goal = this.userInfo[0].goal;
        this.email = this.userInfo[0].mbr_mail;
        this.hp = this.userInfo[0].mbr_hp;
        this.alarmYn = this.userInfo[0].mbr_yn;
        this.userNo = this.userInfo[0].mbr_no;
      }.bind(this))
      .catch(function (error) {
        alert("에러! API 요청에 오류가 있습니다. " + error);
      });
  },
};
</script>