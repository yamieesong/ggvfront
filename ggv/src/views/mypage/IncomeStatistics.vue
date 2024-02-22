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
            <span class="btn_nav bold">수입 통계</span>
          </p>

          <p class="conTitle">
            <span>수입 통계</span>
          </p>

          <table
            width="100%"
            cellpadding="5"
            cellspacing="0"
            border="1"
            align="left"
            style="border-collapse: collapse; border: 1px #50bcdf"
          >
            <tr style="border: 0px; border-color: blue">
              <td width="100" height="25" style="font-size: 120%">
                &nbsp;&nbsp;
              </td>
              
              <td width="50" height="25" style="font-size: 100%">
                <input
                  type="date"
                  style="width: 120px"
                  id="from_date"
                  name="from_date"
                  v-model="sch_from_date"
                />
              </td>
              <td width="50" height="25" style="font-size: 100%">
                <input
                  type="date"
                  style="width: 120px"
                  id="to_date"
                  name="to_date"
                  v-model="sch_to_date"
                />
              </td>
            </tr>
          </table>

          <div style="width: 50%;">
            <table class="col" style="position: relative;right: -50%;">
              <colgroup>
                <col width="33%" />
                <col width="33%" />
                <col width="33%" />
              </colgroup>
              <thead>
                <tr>
                  <th scope="col">카테고리</th>
                  <th scope="col">금액</th>
                  <th scope="col">수입율</th>
                </tr>
              </thead>
              <tbody>
                <template v-if="itemsCnt > 0">
                  <tr v-for="val in items" :key="val.mn_use_dvs_det">
                    <td>{{ val.mn_use_dvs_det }}</td>
                    <td>{{ val.sum_amount.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",") + "원" }}</td>
                    <td>{{ (val.sum_amount / incomeTotalAmount * 100).toFixed(0) + "%" }}</td>
                  </tr>
                </template>
                <template v-else>
                  <tr>
                    <td colspan="3">수입내역이 없습니다.</td>
                  </tr>
                </template>
              </tbody>
            </table>

          </div>
        </div>
        <!--// content -->
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  data: function () {
    return {
      items: [],
      itemsCnt: 0,
      incomeTotalAmount: 0,
      sch_from_date: "",
      sch_to_date: "",
    };
  },
  created() {
    let basicDate = new Date();
    let curYear = basicDate.getFullYear().toString();
    let curMonth = (basicDate.getMonth() + 1).toString();
    let curDay = basicDate.getDate().toString();
    if(curMonth.length === 1){
      curMonth = "0" + curMonth;
    }
    this.sch_from_date = curYear + "-" + curMonth + "-01";
    this.sch_to_date = curYear + "-" + curMonth + "-" + curDay;
  },
  computed: {
  },
  components: {
  },
  mounted() {
  },
  watch: {
    sch_to_date: {
      immediate: false,
      handler(newVal, oldVal){
        if(newVal != oldVal){
          this.getList();
        }
      }
    },
  },
  methods: {
    getList: function(){
      let loginInfo = this.$store.state.loginInfo;
      
      let params = new URLSearchParams();
      params.append("mn_use_dvs", "2");
      //params.append("typeChk", "pay");
      params.append("from_date", this.sch_from_date);
      params.append("to_date", this.sch_to_date);
      params.append("loginId", loginInfo.loginId);
      
      this.axios
        .post("/mypage/expenditureListVue.do", params)
        .then(function (res) {
          this.items = res.data.expenditureList;
          this.itemsCnt = this.items.length;

          this.items.forEach(function(exList){
            this.incomeTotalAmount += exList.sum_amount;
          }.bind(this))
        }.bind(this))
        .catch(function (error) {
          alert("에러! API 요청에 오류가 있습니다. " + error);
        });
    },
  },
};
</script>
