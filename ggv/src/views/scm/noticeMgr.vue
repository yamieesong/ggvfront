<template>
  <div>
    <p class="Location">
      <a href="/dashboard" class="btn_set home"></a>
      <span class="btn_nav bold">가계부</span>
      <span class="btn_nav bold">리스트</span>
      <a href="/scm/userinfo.do" class="btn_set refresh"></a>
    </p>

    <!-- SearchArea -->
    <div class="d-flex justify-content-around" id="searchArea">
      <input
        type="date"
        style="width: 30%"
        id="from_date"
        name="from_date"
        v-model="sch_from_date"
        @change="updateEndDate"
      />
      ~
      <input
        type="date"
        style="width: 30%"
        id="to_date"
        name="to_date"
        v-model="sch_to_date"
      />
      <button
        type="button"
        class="btn btn-primary"
        id="searchBtn"
        @click="search()"
      >
        검색
      </button>
    </div>

    <!-- 가계부 리스트  -->
    <div
      class="gagevueList"
      style="overflow: scroll; width: 100%; height: 500px"
    >
      <table
        class="col"
        border="1"
        width="75%"
        cellpadding="5"
        align="center"
        style="border-collapse: collapse; border: 1px rgb(22, 22, 22)"
      >
        <template v-if="totalCnt == 0">
          <thead>
            <tr>
              <th scope="col">가계부리스트</th>
            </tr>
          </thead>
          <tbody>
            <tr>
              <td colspan="4">데이터가 없습니다</td>
            </tr>
          </tbody>
        </template>
        <template v-else>
          <tbody
            id="gagevueDetailList"
            v-for="(item, index) in gagevueList"
            :key="item.mn_no"
          >
            <template
              v-if="
                index === 0 || item.mn_dtm !== gagevueList[index - 1].mn_dtm
              "
            >
              <tr
                align="left"
                style="border: 1px; border-color: rgb(22, 22, 22)"
              >
                <th scope="col" colspan="4">
                  <!-- Display the date -->
                  <span
                    style="margin-right: 10px; font-size: large; color: black"
                    >{{ item.mn_dtm }}</span
                  >
                  <!-- Display the sums -->
                  <span style="margin-right: 10px; color: blue"
                    >수입 :{{ item.sum_for_use_dvs_import_sum }}
                  </span>
                  <span style="margin-right: 10px; color: red"
                    >지출 : {{ item.sum_for_use_dvs_expense_sum }}
                  </span>
                </th>
              </tr>
            </template>
            <tr align="left" @click="gagevuedatail(item.mn_no)">
              <td rowspan="2"><img src="@/assets/images/scm/shop.png" /></td>
              <!-- 사용처 -->
              <td rowspan="2" style="font-size: 18px">
                <span>{{ item.mn_use_memo }}</span>
              </td>
              <td align="right" v-if="item.mn_use_dvs === '1'">
                <span style="color: red">
                  <span v-if="item.mn_pay_dvs === '1'">
                    <img src="@/assets/images/scm/blackcard.png" />
                  </span>
                  <span v-if="item.mn_pay_dvs === '2'">
                    <img src="@/assets/images/scm/blackmoney.png" />
                  </span>
                  <!-- 사용금액 -->
                  {{ item.mn_amount }}
                </span>
              </td>
              <td align="right" v-if="item.mn_use_dvs === '2'">
                <span style="color: blue">
                  <span v-if="item.mn_pay_dvs === '1'">
                    <img src="@/assets/images/scm/blackcard.png" />
                  </span>
                  <span v-if="item.mn_pay_dvs === '2'">
                    <img src="@/assets/images/scm/greenmoney.png" />
                  </span>
                  {{ item.mn_amount }}
                </span>
              </td>
            </tr>
            <tr align="right">
              <td align="left">{{ item.mn_use_dvs_det_name }}</td>
            </tr>
          </tbody>
          <div></div>
        </template>
        <br />
      </table>
    </div>
  </div>
</template>

<script>
import { openModal } from "jenesius-vue-modal";
import noticeMgrpopup from "./noticeMgrpopup.vue";

export default {
  data: function () {
    return {
      gagevueList: [],
      sch_from_date: "",
      sch_to_date: "",
      totalCnt: 0,
      action: "",
      gagevueListTotalCnt: [],
    };
  },
  mounted() {
    // onload
    this.search();
  },
  methods: {
    search: function () {
      let vm = this;

      let params = new URLSearchParams();
      params.append("from_date", this.sch_from_date);
      params.append("to_date", this.sch_to_date);
      params.append("mn_rgst_id", this.$store.state.loginInfo.loginId);

      this.axios
        .post("/scm/selectgagevueList.do", params)
        .then(function (response) {
          vm.gagevueList = response.data.gagevueList;
          vm.totalCnt = response.data.gagevueListCnt;
          vm.gagevueListTotalCnt = response.data.gagevueListTotalCnt;
          console.log(vm.gagevueList);
        })
        .catch(function (error) {
          alert("에러! API 요청에 오류가 있습니다. " + error);
        });
    },
    gagevuedatail: async function (mn_no) {
      this.action = "UPDATE";
      const modal = await openModal(noticeMgrpopup, {
        title: "가계부 수정",
        mn_no: mn_no,
        action: this.action,
      });

      modal.onclose = () => {
        this.search();
      };
    },
  },
};
</script>

<style scoped>
/* Style for the entire table */
.gagevueList {
  width: 100%;
  border-collapse: collapse;
  margin-top: 20px;
}

/* Style for the table headers */
.gagevueList th {
  background-color: skyblue;
  color: #ffffff;
  padding: 10px;
  text-align: left;
}

/* Style for alternating rows */
.gagevueList tr:nth-child(even) {
  background-color: #f8f9fa;
}

/* Style for table cells */
.gagevueList td {
  border: 1px solid #dee2e6;
  padding: 10px;
}

/* Hover effect for table rows */
.gagevueList tbody tr:hover {
  background-color: #d1e8ff;
}

/* Style for links within the table */
.gagevueList a {
  color: skyblue;
  text-decoration: none;
}

/* Hover effect for links */
.gagevueList a:hover {
  text-decoration: underline;
}
/* Style for the date search area */
#searchArea {
  margin-top: 20px;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
  background-color: #f8f9fa;
}

/* Style for date input fields */
#searchArea input[type="date"] {
  width: 30%;
  padding: 8px;
  margin-right: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
}

/* Style for the search button */
#searchBtn {
  padding: 10px;
  background-color: skyblue;
  color: #fff;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

/* Hover effect for the search button */
#searchBtn:hover {
  background-color: skyblue;
}
#userListArea table {
  margin: 0 auto;
}
#userinfoarea {
  padding: 10px;
  border: 2px solid rgb(190, 190, 190);
  margin-bottom: 50px;
}
#userinfoarea table {
  border-collapse: separate;
  border-spacing: 10px 10px;
  margin: 0 auto;
}
.userInfoBtnArea {
  margin-top: 10px;
}
#sb-userType {
  margin-left: 0 !important;
}

#btn-close-daum {
  position: absolute;
  right: 0;
  bottom: 0;
  z-index: 11;
  cursor: pointer;
}
img {
  width: 25px;
}
</style>
