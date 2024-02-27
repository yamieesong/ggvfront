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
              <strong style="font-size: 30px">{{ title }}</strong>
            </div>
          </td>
        </tr>

        <tr>
          <th scope="row">상세코드<span class="font_red">*</span></th>
          <td>
            <input
              type="text"
              class="form-control"
              name="dtl_cod"
              id="dtl_cod"
              v-model="dtl_cod"
            />
          </td>
          <th scope="row">상세코드 명 <span class="font_red">*</span></th>
          <td>
            <input
              type="text"
              class="form-control"
              name="dtl_cod_nm"
              id="dtl_cod_nm"
              v-model="dtl_cod_nm"
            />
          </td>
        </tr>
        <tr>
          <th scope="row">상세코드 설명</th>
          <td colspan="3">
            <input
              type="text"
              class="form-control"
              name="dtl_cod_eplti"
              id="dtl_cod_eplti"
              v-model="dtl_cod_eplti"
            />
          </td>
        </tr>

        <tr>
          <th scope="row">그룹 코드 <span class="font_red">*</span></th>
          <td>
            <input
              type="text"
              class="form-control"
              name="dtl_grp_cod"
              id="dtl_grp_cod"
              v-model="dtl_grp_cod"
              readonly
            />
          </td>
          <th scope="row">사용 유무 <span class="font_red">*</span></th>
          <td>
            <input
              type="radio"
              name="dtl_use_poa"
              id="dtl_use_poa_1"
              value="Y"
              v-model="dtl_use_poa"
            />
            <label for="radio1-1">사용</label>
            &nbsp;&nbsp;&nbsp;&nbsp;
            <input
              type="radio"
              name="dtl_use_poa"
              id="dtl_use_poa_2"
              value="N"
              v-model="dtl_use_poa"
            />
            <label for="radio1-2">미사용</label>
          </td>
        </tr>
      </tbody>
    </table>

    <!-- e : 여기에 내용입력 -->

    <div class="btn_areaC mt30">
      <a @click="save()" class="btn btn-primary" id="btnSaveGrpCod" name="btn">
        <span>저장</span>
      </a>
      <a @click="deletefrpcd()" class="btn btn-danger mx-2" >
        <span>삭제</span>
      </a>
      <a class="btn btn-light mx-2" id="btnClose" name="btn" @click="close()">
        <span>취소</span>
      </a>
    </div>
  </dl>
</template>

<script>
import { closeModal } from "jenesius-vue-modal";
export default {
  // vue에서는 받아온 변수를 methods에서 직접 핸들링이 불가능하기 때문에
  // 임시 변수를 만들어서 받아온 변수를 넣어 줘야 함
  props: { title: String, dtlcd: String, action: String, groupCode: String },
  data: function () {
    return {
      pdtlcd: this.dtlcd,
      dtl_grp_cod: this.groupCode,
      dtl_cod: "",
      def_dtl_cod: this.dtl_cod,
      dtl_cod_nm: "",
      dtl_cod_eplti: "",
      dtl_use_poa: "",
      eventYn: "",
    };
  },
  computed: {
    adtlcd: {
      get: function () {
        return this.data.pdtlcd;
      },
      set: function (v) {
        this.data.pdtlcd = v;
      },
    },
  },
  // html 로딩, 가상 dom 실행, 이 두 개 연결 시 작동
  mounted: function () {
    let vm = this;
    
    // 신규 등록 시
    if (this.pdtlcd == null || this.pdtlcd == "") {
      vm.dtl_cod = "";
      vm.dtl_cod_nm = "";
      vm.dtl_cod_eplti = "";
      vm.dtl_use_poa = "";
    } else {
      //  수정 시 (grp_cod 에 해당하는 상세코드 정보 가져오기)
      let params = new URLSearchParams();
      params.append("grp_cod", this.dtl_grp_cod);
      params.append("dtl_cod", this.pdtlcd);

      this.axios
        .post("/system/selectComnDtlCod.do", params)
        .then(function (response) {
          console.log(response);
          // response data;

          // detailcnt: 0
          // fnl_mdfd_dtt: "2022-03-25 15:09:14.0"
          // fnl_mdfr_sst_id: null
          // fst_enlm_dtt: 1648177974000
          // fst_rgst_sst_id: null
          // grp_cod: "shipCD"
          // grp_cod_eplti: null
          // grp_cod_nm: "배송코드"
          // reg_date: null
          // row_num: 0
          //         // tmp_fld_01: "3"
          // tmp_fld_02: null
          // tmp_fld_03: null
          // use_poa: "Y"

          vm.dtl_cod = response.data.comnDtlCodModel.dtl_cod;
          vm.dtl_cod_nm = response.data.comnDtlCodModel.dtl_cod_nm;
          vm.dtl_cod_eplti = response.data.comnDtlCodModel.dtl_cod_eplti;
          vm.dtl_use_poa = response.data.comnDtlCodModel.use_poa;
        })
        .catch(function (error) {
          alert("에러! API 요청에 오류가 있습니다. " + error);
        });
    }
  },
  methods: {
    close: function () {
      closeModal();
    },
    save: function () {
      if (confirm("저장하시겠습니까?")) {
        let vm = this;
        let params = new URLSearchParams();

        params.append("dtl_cod", this.dtl_cod);
        params.append("dtl_cod_nm", this.dtl_cod_nm);
        params.append("dtl_cod_eplti", this.dtl_cod_eplti);
        params.append("dtl_use_poa", this.dtl_use_poa);
        params.append("dtl_grp_cod", this.dtl_grp_cod);
        params.append("action", this.action);
        params.append("pdtlcd",this.pdtlcd);
        this.axios
          .post("/system/saveComnDtlCod.do", params)
          .then(function (response) {
            console.log(response);
            let status = response.status;
            let msg = response.data.resultMsg;
            if (status == 200) {
              alert(msg);
              closeModal(vm);
            } else {
              alert(msg);
            }
          })
          .catch(function (error) {
            alert("에러! API 요청에 오류가 있습니다. " + error);
          });
      }
    },
    deletefrpcd: function () {
      let vm = this;
      let params = new URLSearchParams();
      params.append("dtl_cod", this.dtl_cod);
      params.append("dtl_grp_cod", this.dtl_grp_cod);

      this.axios
        .post("/system/deleteComnDtlCod.do", params)
        .then(function (response) {
          console.log(response);
          alert(response.data.resultMsg);
          closeModal(vm);
        })
        .catch(function (error) {
          alert("에러! API 요청에 오류가 있습니다. " + error);
        });
    },
  },
  created() {
    // 자식요소에서 부모 요소 method 호출
  },
};
</script>

<style>
#grpInfo {
  border-collapse: separate;
  border-spacing: 20px;
}
#grpInfoWrap {
  background-color: #fff;
  padding: 3rem;
  border-radius: 10px;
  border: 2px solid rgb(59, 59, 59);
}
</style>
