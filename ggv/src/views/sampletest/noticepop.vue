<template>
  <dl class="layerPop layerType2" style="width: 600px">
    <dt>
      <strong>{{ title }}</strong>
    </dt>
    <dd class="content" style="width: 600px">
      <!-- s : 여기에 내용입력 -->
      <table class="row">
        <caption>
          caption
        </caption>

        <tbody>
          <tr>
            <th scope="row">작성자 <span class="font_red">*</span></th>
            <td>
              <input
                type="text"
                class="inputTxt p100"
                name="loginId"
                id="loginId"
                style="width: 500px"
                v-model="loginId"
              />
            </td>
          </tr>
          <tr>
            <th scope="row">제목 <span class="font_red">*</span></th>
            <td colspan="3">
              <input
                type="text"
                class="inputTxt p100"
                name="noticeTitle"
                id="noticeTitle"
                style="width: 500px"
                v-model="noticeTitle"
              />
            </td>
          </tr>
          <tr>
            <th scope="row">내용</th>
            <td colspan="3">
              <textarea
                class="inputTxt p100"
                name="noticeContent"
                id="noticeContent"
                style="width: 500px"
                v-model="noticeContent"
              >
              </textarea>
            </td>
          </tr>
        </tbody>
      </table>

      <!-- e : 여기에 내용입력 -->

      <div class="btn_areaC mt30">
        <a class="btnType blue" id="btnUpdateNotice" name="btn" @click="save()"
          ><span>{{ btndis }}</span></a
        >
        <a
          class="btnType blue"
          id="btnDeleteNotice"
          name="btn"
          v-show="delshow"
          @click="deletenoice()"
          ><span>삭제</span></a
        >
        <a class="btnType gray" id="btnClose" name="btn" @click="close()"
          ><span>취소</span></a
        >
      </div>
    </dd>
  </dl>
</template>
<script>
import { closeModal } from 'jenesius-vue-modal';

export default {
  props: {
    title: String,
    noticeNo: Number,
    action: String,
  },
  data: function () {
    return {
      items: [],
      ptitle: '',
      pnoticeNo: 0,
      paction: '',
      delshow: false,
      loginId: '',
      noticeTitle: '',
      noticeContent: '',
      btndis: '',
    };
  },
  mounted() {
    let loginInfo = this.$store.state.loginInfo;

    console.log(loginInfo.loginId);

    this.ptitle = this.title;
    this.pnoticeNo = this.noticeNo;
    this.paction = this.action;

    if (this.paction == 'I') {
      this.delshow = false;
      this.loginId = loginInfo.loginId;
      this.btndis = '저장';
    } else {
      this.delshow = true;
      this.btndis = '수정';

      let vm = this;

      let params = new URLSearchParams();
      params.append('noticeNo', this.pnoticeNo);

      this.axios
        .post('/system/detailNotice.do', params)
        .then(function (response) {
          //console.log(response);
          //console.log(response.data.notice);
          //console.log(response.data.noticeCnt);
          vm.loginId = response.data.result.loginId;
          vm.noticeTitle = response.data.result.noticeTitle;
          vm.noticeContent = response.data.result.noticeContent;
        })
        .catch(function (error) {
          alert('에러! API 요청에 오류가 있습니다. ' + error);
        });
    }
  },
  methods: {
    save: function () {
      //let vm = this;
      // { action:"I", noticeNo: "11", loginId : "aaa", noticeTitle : "sss", noticeContent : "ddd"}
      let params = new URLSearchParams();
      params.append('action', this.paction);
      params.append('noticeNo', this.pnoticeNo);
      params.append('loginId', this.loginId);
      params.append('noticeTitle', this.noticeTitle);
      params.append('noticeContent', this.noticeContent);

      this.axios
        .post('/system/noticeSave.do', params)
        .then(function (response) {
          console.log(response);
          if (
            response.data.resultMsg == 'SUCCESS' ||
            response.data.resultMsg == 'UPDATED'
          ) {
            alert('저장 되었습니다.');
            closeModal();
          }

          //console.log(response.data.notice);
          //console.log(response.data.noticeCnt);
          //vm.loginId = response.data.result.loginId;
          //vm.noticeTitle = response.data.result.noticeTitle;
          //vm.noticeContent = response.data.result.noticeContent;
        })
        .catch(function (error) {
          alert('에러! API 요청에 오류가 있습니다. ' + error);
        });
    },
    deletenoice: function () {
      //let vm = this;
      // { action:"I", noticeNo: "11", loginId : "aaa", noticeTitle : "sss", noticeContent : "ddd"}

      if (confirm('정말 삭제하겠습니까? \n 삭제시 복구불가합니다.')) {
        let params = new URLSearchParams();
        params.append('noticeNo', this.pnoticeNo);
        params.append('action', 'D');

        this.axios
          .post('/system/noticeDelete.do', params)
          .then(function (response) {
            console.log(response);

            if (response.data.result == 'SUCCESS') {
              alert(response.data.resultMsg);
              closeModal();
            } else {
              alert('실패 했습니다.');
            }
          })
          .catch(function (error) {
            alert('에러! API 요청에 오류가 있습니다. ' + error);
          });
      }
    },
    close: function () {
      closeModal();
    },
  },
};
</script>
