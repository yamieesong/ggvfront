<template>
  <dl style="border:5px solid white;padding: 10px;">
    <dt>
      <strong style="font-size: 20px">{{ title }}</strong>
    </dt>
    <dd class="content">
      <p>
        <label>비밀번호</label>
        <input v-model="pw" />
      </p>
      <p>
        <label>비밀번호 확인</label>
        <input v-model="pwConfirm" />
      </p>
      <div class="btn_areaC mt30">
        <button type="button" class='btn btn-primary' @click="updatePw()">확인</button>
        <button type="button" class="btn btn-danger" @click="closePop()">취소</button>
        <!--        <a class="btnType blue" id="btnSaveNotice" name="btn" @click="saveNotice()"-->
        <!--        ><span>{{ btnNm }}</span></a-->
        <!--        >-->
        <!--        <a class="btnType gray" id="btnClose" name="btn" @click="closePop()"-->
        <!--        ><span>취소</span></a-->
        <!--        >-->
      </div>
    </dd>
  </dl>
</template>


<script>
import { closeModal } from 'jenesius-vue-modal';

export default {
  props: {
    title: String,
  },
  data: function() {
    return {
      items: [],
      btnNm: '확인',
      loginId: '',
      loginNm: '',
      pw: '',
      pwConfirm: '',
    };
  },
  mounted() {
    this.loginId = this.$store.state.loginInfo.loginId;
    //let loginInfo = this.$store.state.loginInfo;
    //console.log(loginInfo)
  },
  methods: {
    closePop: function() {
      // await this.emitter.emit('pw', this.pw);
      closeModal();
    },
    updatePw: async function() {
      if (this.pw === '') {
        alert('비밀번호를 입력해주세요.');
        return;
      } else if (this.pw != this.pwConfirm) {
        alert('비밀번호와 비밀번호확인이 같지않음');
        return;
      }

      let params = new URLSearchParams();
      params.append('loginId', this.loginId);
      params.append('password', this.pw);

      await this.axios
        .post('/mypage/updatePw', params)
        .then(function(res) {
          console.log('updatePw res start');
          if (200 == res.status) {
            alert('비밀번호 변경 완료');
            closeModal();
          } else {
            alert('에러');
          }
        });
    },
  },
};
</script>