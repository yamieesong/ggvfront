<template>
  <div id="comnCodMgr">
    <p class="Location">
      <a class="btn_set home"></a>
      <span class="btn_nav bold">기준정보</span>
      <span class="btn_nav bold">공통코드 관리</span>
      <a class="btn_set refresh">새로고침</a>
    </p>

    <p class="conTitle">
      <span>그룹 코드</span>
      <span>
        <table
          style="collapse; border: 1px #50bcdf;"
          width="100%"
          cellpadding="5"
          cellspacing="0"
          border="1"
          align="left"
        >
          <tr style="border: 0px; border-color: blue">
            <td
              width="50"
              height="25"
              style="font-size: 100%; text-align: left"
            >
              <div id="searchArea" class="d-flex justify-content-around">
                <select
                  id="searchKey"
                  class="form-control"
                  style="width: 150px"
                  v-model="searchKey"
                >
                  <option value="">전체</option>
                  <option value="grp_cod">그룹코드</option>
                  <option value="grp_cod_nm">그룹코드명</option>
                </select>

                <input
                  type="text"
                  style="width: 55%"
                  class="form-control"
                  v-model="sname"
                />
                <span class="fr">
                  <a
                    class="btn btn-primary mx-2"
                    id="btnSearchGrpcod"
                    name="btn"
                  >
                    <span>검 색</span>
                  </a>
                  <a class="btn btn-light mx-2"
                    @click="rowGrdClicked()"
                    name="modal"
                    >
                    <span>신규등록</span>
                  </a>
                </span>
              </div>
            </td>
          </tr>
        </table>
      </span>
    </p>

    <div class="divComGrpCodList">
      <table class="col">
        <caption>
          caption
        </caption>
        <colgroup>
          <col width="6%" />
          <col width="17%" />
          <col width="20%" />
          <col width="20%" />
          <col width="10%" />
          <col width="15%" />
          <col width="10%" />
        </colgroup>

        <thead>
          <tr>
            <th scope="col">번호</th>
            <th scope="col">그룹코드</th>
            <th scope="col">그룹코드명</th>
            <th scope="col">그룹코드 설명</th>
            <th scope="col">사용여부</th>
            <th scope="col">등록일</th>
            <th scope="col">비고</th>
          </tr>
        </thead>
        <tbody>
          <template v-if="totalCnt == 0">
            <tr>
              <td colspan="7">일치하는 검색 결과가 없습니다</td>
            </tr>
          </template>
          <template v-else>
            <tr v-for="item in items" :key="item.grp_cod">
              <td>{{ item.indexNew }}</td>
              <td id="groupTitle" @click="searchdetail(item.grp_cod)">
                {{ item.grp_cod }}
              </td>
              <td>{{ item.grp_cod_nm }}</td>
              <td>{{ item.grp_cod_eplti }}</td>
              <td>{{ item.use_poa }}</td>
              <td>{{ item.fst_enlm_dtt }}</td>
              <td>
                <a @click="rowGrdClicked(item.grp_cod)" class="btnType3 color1 btn" style="background-color: rgb(1 61 125); border: 0px;"
                  ><span>수정</span></a
                >
              </td>
            </tr>
          </template>
        </tbody>
      </table>
    </div>

    <div id="comnGrpCodPagination">
      <paginate
        class="justify-content-center"
        v-model="currentPage"
        :page-count="totalPage"
        :page-range="5"
        :margin-pages="0"
        :click-handler="clickCallback"
        :prev-text="'Prev'"
        :next-text="'Next'"
        :container-class="'pagination'"
        :page-class="'page-item'"
      >
      </paginate>
    </div>

    <br />
    <br />
    <br />
    <br />

    <div>
      <p class="conTitle">
        <span style="margin-right: 630px;">그룹 코드 상세</span>
        <span>
          <table
            style="collapse; border: 1px #50bcdf;"
            width="100%"
            cellpadding="5"
            cellspacing="0"
            border="1"
            align="left"
          >
            <tr style="border: 0px; border-color: blue">
              <td
                width="50"
                height="25"
                style="font-size: 100%; text-align: left"
              ></td>
            </tr>
          </table>
        </span>
          <a class="btn btn-light mx-2"
          @click="rowDetailClicked()"
          name="modal"
          style="margin-bottom: 12px;"
          v-show="delshow"
          >
          <span>신규등록</span>
        </a>
      </p>

      <div class="divComGrpCodList">
        <table class="col">
          <caption>
            caption
          </caption>
          <colgroup>
            <col width="6%" />
            <col width="10%" />
            <col width="9%" />
            <col width="20%" />
            <col width="20%" />
            <col width="10%" />
            <col width="15%" />
            <col width="10%" />
          </colgroup>

          <thead>
            <tr>
              <th scope="col">번호</th>
              <th scope="col">그룹코드</th>
              <th scope="col">상세코드</th>
              <th scope="col">상세코드 명</th>
              <th scope="col">상세코드 설명</th>
              <th scope="col">사용여부</th>
              <th scope="col">등록일</th>
              <th scope="col">비고</th>
            </tr>
          </thead>
          <tbody>
            <template v-if="totalCnt2 == 0">
              <tr>
                <td colspan="8">일치하는 검색 결과가 없습니다</td>
              </tr>
            </template>
            <template v-else>
              <tr v-for="item in groupDetail" :key="item.grp_cod">
                <td>{{ item.row_num }}</td>
                <td>{{ item.grp_cod }}</td>
                <td>{{ item.dtl_cod }}</td>
                <td>{{ item.dtl_cod_nm }}</td>
                <td>{{ item.dtl_cod_eplti }}</td>
                <td>{{ item.use_poa }}</td>
                <td>{{ item.fst_enlm_dtt }}</td>
                <td>
                  <a @click="rowDetailClicked(item.dtl_cod)" class="btnType3 color1 btn" style="background-color: rgb(1 61 125); border: 0px;">
                    <span>수정</span>
                  </a>
                </td>
              </tr>
            </template>
          </tbody>
        </table>
      </div>

      <div id="comnGrpCodPagination2">
        <paginate
          class="justify-content-center"
          v-model="currentPage2"
          :page-count="page2()"
          :page-range="5"
          :margin-pages="0"
          :click-handler="clickCallback2"
          :prev-text="'Prev'"
          :next-text="'Next'"
          :container-class="'pagination'"
          :page-class="'page-item'"
        >
        </paginate>
      </div>
    </div>
  </div>
</template>
<script>
import { openModal } from "jenesius-vue-modal";
import grddetailModal from "@/components/system/grddetailModal.vue";
import detailModal from "@/components/system/detailModal.vue";
import Paginate from "vuejs-paginate-next";

export default {
  data: function () {
    return {
      items: [],
      groupDetail: [],
      groupDetail2: [],
      currentPage: 1,
      currentPage2: 1,
      pageSize: 5,
      pageSize2: 5,
      totalPage: 1,
      totalPage2: 1,
      totalCnt: 0,
      totalCnt2: 0,
      searchKey: "",
      sname: "",
      delshow: false,
      action: "",
      groupCode: "",
      countShow: true,
      grdNo: 1,
    };
  },
  computed: {},
  components: {
    paginate: Paginate,
  },
  mounted() {
    this.listsearch();
  },
  methods: {
    //
    searchdetail: function (group_code) {
      let vm = this;

      vm.groupCode = group_code; // for 페이징 넘버 클릭시를 위함
      let params = new URLSearchParams();
      params.append("grp_cod", group_code);
      params.append("currentPage", this.currentPage2);
      params.append("pageSize", this.pageSize2);

      this.axios
        .post("/system/comcombo.do", params)
        .then(function (response) {
          //console.log(response.data.listComnDtlCodModel);
          vm.groupDetail = response.data.listComnDtlCodModel;
          vm.totalCnt2 = response.data.totalCntComnDtlCod;
          vm.delshow = true;
          vm.totalPage2 = vm.page2();
        })
        .catch(function (error) {
          alert("에러! API 요청에 오류가 있습니다. " + error);
        });
    },
    searchotton: function (pageno) {
      this.currentPage = pageno;
      this.listsearch();
    },
    rowGrdClicked: async function (grpcd) {
      if (grpcd == null || grpcd == "") {
        this.action = "I";

        const modal = await openModal(grddetailModal, {
          title: "그룹코드 등록",
          grpcd: "",
          action: this.action,
        });

        modal.onclose = () => {
          console.log("Close");
          this.listsearch();
          // return false; //Modal will not be closed
        };
      } else {
        this.action = "U";
        const modal = await openModal(grddetailModal, {
          title: "그룹코드 수정",
          grpcd: grpcd,
          action: this.action,
        });

        modal.onclose = () => {
          console.log("Close");
          this.listsearch();
          //this.searchnoticelist();
          //return false; //Modal will not be closed
        };
      }
    },
    rowDetailClicked: async function (dtlcd) {
      if (dtlcd == null || dtlcd == "") {
        this.action = "I";

        const modal = await openModal(detailModal, {
          title: "상세코드 등록",
          grpcd: "",
          action: this.action,
          groupCode: this.groupCode,
        });

        modal.onclose = () => {
          console.log("Close");
          this.listsearch();
          // return false; //Modal will not be closed
        };
      } else {
        this.action = "U";
        const modal = await openModal(detailModal, {
          title: "상세코드 수정",
          dtlcd: dtlcd,
          action: this.action,
          groupCode: this.groupCode,
        });

        modal.onclose = () => {
          console.log("Close");
          this.listsearch();
          //this.searchnoticelist();
          //return false; //Modal will not be closed
        };
      }
    },
    listsearch: function () {
      let vm = this;

      let params = new URLSearchParams();
      params.append("currentPage", this.currentPage);
      params.append("pageSize", this.pageSize);
      params.append("oname", this.searchKey);
      params.append("sname", this.sname);

      this.axios
        .post("/system/vueListComnGrpCod.do", params)
        .then(function (response) {
          console.log(response);
          vm.totalCnt = response.data.totalCntComnGrpCod;
          vm.items = response.data.listComnGrpCodModel;
          vm.totalPage = vm.page();
          
          vm.currentPage == 1
            ? (vm.grdNo = 1)
            : (vm.grdNo = 5 * (vm.currentPage - 1) + 1);

          for (let value of vm.items) {
            value.indexNew = vm.grdNo++;
          }
        })
        .catch(function (error) {
          alert("에러! API 요청에 오류가 있습니다. " + error);
        });
    },
    clickCallback: function (pageNum) {
      console.log(pageNum);

      this.currentPage = pageNum;
      //this.Paginate.pageNum = 10;
      this.listsearch();
    },
    clickCallback2: function (pageNum) {
      console.log(pageNum);

      this.currentPage2 = pageNum;
      //this.Paginate.pageNum = 10;

      this.searchdetail(this.groupCode);
    },
    page: function () {
      var total = this.totalCnt;
      var page = this.pageSize;
      var xx = total % page;
      var result = parseInt(total / page);

      if (xx == 0) {
        return result;
      } else {
        result = result + 1;
        return result;
      }
    },
    page2: function () {
      let total = this.totalCnt2;
      let page = this.pageSize2;
      let xx = total % page;
      let result = parseInt(total / page);

      if (xx == 0) {
        return result;
      } else {
        result = result + 1;
        return result;
      }
    },
  },
};
</script>

<style>
.btn-light{
  background-color: rgb(0 205 247);
  border-color: rgb(0 205 247);
  color: #f8f9fa;
}

#searchArea {
  margin-top: 25px;
  margin-bottom: 25px;
}
#searchArea > * {
  height: auto;
}

#groupTitle {
  text-decoration: underline;
  font-weight: bold;
  cursor: pointer;
}
</style>
