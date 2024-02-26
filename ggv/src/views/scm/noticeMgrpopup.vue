<template>
  <dl id="grpInfoWrap">
    <dd class="content"></dd>
    <!-- s : 여기에 내용입력 -->
    <table id="grpInfo">
      <caption>
        caption
      </caption>
      <colgroup>
        <col width="120px" />
        <col width="*" />
        <col width="120px" />
        <col width="*" />
      </colgroup>
      <tbody>
        <tr>
          <td colspan="4" class="text-center">
            <div class="my-4">
              <strong style="font-size: 30px">
                {{ mn_dtm }}
              </strong>
            </div>
          </td>
        </tr>
        <tr>
          <th scope="row">사용 내용<span class="font_red">*</span></th>
          <td>
            <input
              type="text"
              class="form-control"
              name="mn_use_memo"
              id="mn_use_memo"
              v-model="mn_use_memo"
            />
          </td>
          <th scope="row">구분 <span class="font_red">*</span></th>
          <td>
            <select v-model="mn_use_dvs" class="form-control">
              <option value="" disabled selected>선택</option>
              <option
                v-for="code in mnUseDvsCodeList"
                :key="code.group_code"
                :value="code.detail_code"
              >
                {{ code.detail_name }}
              </option>
            </select>
          </td>
        </tr>
        <tr>
          <th scope="row">항목 선택<span class="font_red">*</span></th>
          <td colspan="3">
            <select v-model="mn_use_dvs_det" class="form-control">
              <option value="" disabled selected>선택</option>
              <!-- Initial option -->
              <option
                v-for="code in mnUseDvsDetCodeList"
                :key="code.group_code"
                :value="code.detail_code"
              >
                {{ code.detail_name }}
              </option>
            </select>
          </td>
        </tr>
        <tr>
          <th scope="row">결제 구분<span class="font_red">*</span></th>
          <td colspan="3">
            <select v-model="mn_pay_dvs" class="form-control">
              <option value="" disabled selected>선택</option>
              <!-- Initial option -->
              <option
                v-for="code in mnPayDvsCodeList"
                :key="code.group_code"
                :value="code.detail_code"
              >
                {{ code.detail_name }}
              </option>
            </select>
          </td>
        </tr>

        <tr>
          <th scope="row">결제 금액<span class="font_red">*</span></th>
          <td colspan="3">
            <input
              type="number"
              class="form-control"
              name="mn_amount"
              id="mn_amount"
              v-model="mn_amount"
              @input="inputNumber($event)"
            />
          </td>
        </tr>
      </tbody>
    </table>
    <!-- e : 여기에 내용입력 -->

    <div class="btn_areaC mt30">
      <a class="btn btn-primary" id="btnSave" name="btn" @click="save()">
        <span>{{ btn }}</span>
      </a>
      <a
        class="btn btn-danger mx-2"
        id="btnDeletegagevue"
        name="btn"
        v-show="delshow"
        @click="deletegagevue()"
      >
        <span>삭제</span>
      </a>
    </div>
  </dl>
</template>
<script>
import { closeModal } from "jenesius-vue-modal";

export default {
  props: {
    title: String,
    mn_no: String,
    action: String,
    mn_dtm: String,
  },
  data: function () {
    return {
      gagevueOne: [],
      mn_dtm: "",
      mn_use_memo: "",
      mn_use_dvs: "",
      mn_use_dvs_det: "",
      mn_pay_dvs: "",
      mn_amount: "",
      loginId: "",
      mn_rgst_id: "",
      paction: "",
      codeList: [],
      btn: "",
      delshow: false,
    };
  },
  created() {
    this.mn_dtm = this.$props.mn_dtm;
  },
  computed: {
    mnUseDvsCodeList() {
      /* 구분 1.지출 2.수입 */
      return this.codeList.filter((code) => code.group_code === "USE_DVS");
    },
    mnUseDvsDetCodeList() {
      /* 항목 U01. 식비 등등.. */
      return this.codeList.filter(
        (code) =>
          code.group_code === "USE_DETAIL" || code.group_code === "PAY_DETAIL"
      );
    },
    mnPayDvsCodeList() {
      /* 결제구분 1.카드 2.현금 */
      return this.codeList.filter((code) => code.group_code === "PAY_DVS");
    },
  },
  mounted: function () {
    let vm = this;
    this.axios
      .post("/scm/setCode.do")
      .then((response) => {
        /* 공통code 할당 */
        console.log("/scm/setCode.do");
        vm.codeList = response.data;
      })
      .catch((error) => {
        console.error("데이터 가져오기 실패:", error);
      });

    this.paction = this.action;

    if (this.paction == "INSERT") {
      console.log(this.paction);
      this.delshow = false;
      this.btn = "저장";
    } else {
      this.delshow = true;
      console.log("UPDATE");
      console.log(this.paction);
      this.btn = "수정";

      let params = new URLSearchParams();
      params.append("mn_no", this.mn_no);

      this.axios
        .post("/scm/selectgagevueOne.do", params)
        .then(function (response) {
          /* 가계부 상세조회 > 수정 response.data.gagevueOne */
          vm.mn_dtm = response.data.gagevueOne.mn_dtm; // * 사용날짜
          vm.mn_use_memo = response.data.gagevueOne.mn_use_memo; // * 사용내용 : 메모
          vm.mn_use_dvs = response.data.gagevueOne.mn_use_dvs; // * 구분 1: 지출 2:수입
          vm.mn_use_dvs_det = response.data.gagevueOne.mn_use_dvs_det; // * 항목 선택 : U06 교육/육아 등등
          vm.mn_pay_dvs = response.data.gagevueOne.mn_pay_dvs; // * 결제구분 : 1:카드 2:현금
          vm.mn_amount = response.data.gagevueOne.mn_amount; // * 결제금액
        })
        .catch(function (error) {
          alert("에러! API 요청에 오류가 있습니다. " + error);
        });
    }
  },
  methods: {
    inputNumber(event) {
      /* mn_amount(금액) 숫자만 입력하게 체크 */
      this.result = event.target.value;
    },
    save: function () {
      let params = new URLSearchParams();

      params.append("mn_no", this.mn_no);
      params.append("mn_dtm", this.mn_dtm);
      params.append("mn_use_memo", this.mn_use_memo);
      params.append("mn_use_dvs", this.mn_use_dvs);
      params.append("mn_use_dvs_det", this.mn_use_dvs_det);
      params.append("mn_pay_dvs", this.mn_pay_dvs);
      params.append("mn_amount", this.mn_amount);
      params.append("loginID", this.$store.state.loginInfo.loginId);
      params.append("action", this.paction);
      params.append("mn_rgst_id", this.$store.state.loginInfo.loginId);

      // 저장 - 입력값 Validate check
      if (
        !this.mn_use_memo ||
        !this.mn_use_dvs ||
        !this.mn_use_dvs_det ||
        !this.mn_pay_dvs ||
        !this.mn_amount
      ) {
        alert("모든 필수 항목을 모두 입력해주세요."); //  error message
        return;
      }

      this.axios
        .post("/scm/savegagevue.do", params)
        .then(function (response) {
          console.log(response.data.resultMsg);
          alert(response.data.resultMsg);
          closeModal();
        })
        .catch(function (error) {
          alert("에러! API 요청에 오류가 있습니다. " + error);
        });
    },
    deletegagevue: function () {
      if (confirm("정말 삭제하겠습니까? \n 삭제시 복구불가합니다.")) {
        let params = new URLSearchParams();
        params.append("mn_no", this.mn_no);

        this.axios
          .post("/scm/deletegagevue.do", params)
          .then(function (response) {
            console.log(response);

            if (response.data.result == "SUCCESS") {
              alert(response.data.resultMsg);
              closeModal();
            } else {
              alert("실패 했습니다.");
            }
          })
          .catch(function (error) {
            alert("에러! API 요청에 오류가 있습니다. " + error);
          });
      }
    },
  },
};
</script>
<style>
#grpInfoWrap {
  width: 600px;
  margin: 50px auto;
  padding: 20px;
  background-color: #fff;
  border: 1px solid #ccc;
  border-radius: 5px;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
}

#grpInfo {
  width: 100%;
  margin-top: 20px;
  border-collapse: collapse;
}

#grpInfo caption {
  font-size: 20px;
  font-weight: bold;
  margin-bottom: 10px;
}

#grpInfo th,
#grpInfo td {
  padding: 10px;
  border: 1px solid #ccc;
}

#grpInfo th {
  text-align: left;
  background-color: #f8f9fa;
}

.form-control {
  width: 100%;
  padding: 8px;
  border: 1px solid #ccc;
  border-radius: 5px;
}

.btn_areaC {
  text-align: center;
  margin-top: 30px;
}

.btn_areaC a {
  display: inline-block;
  padding: 10px 20px;
  margin: 0 5px;
  text-decoration: none;
  color: #fff;
  border-radius: 5px;
}

.btn-primary {
  background-color: #007bff;
}

.btn-danger {
  background-color: #dc3545;
}
</style>
