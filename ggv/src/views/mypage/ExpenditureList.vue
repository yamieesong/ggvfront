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
            <span class="btn_nav bold">지출 통계</span>
          </p>

          <p class="conTitle">
            <span>지출 통계</span>
          </p>

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
                  <th scope="col">지출율</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="val in items" :key="val.mn_use_dvs_det">
                  <td>{{ val.mn_use_dvs_det }}</td>
                  <td>{{ val.sum_amount.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",") + "원" }}</td>
                  <td>{{ (val.sum_amount / expenditureTotalAmount * 100).toFixed(0) + "%" }}</td>
                </tr>
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
      expenditureTotalAmount: 0,
      curRoute: this.$router.currentRoute._value.fullPath,
      params:{},
    };
  },
  created() {
    //this.search();
    //console.log("this.items", this.items)
    alert("ExpenditureList")
    this.emitter.on('expenditureType', function(exType){
      console.log("exType", exType);
    }.bind(this));
  },
  watch: {
    /*
    curRoute: {
        immediate: true,
        handler(newVal, oldVal) {
          alert("watch curRoute")
          console.log("watch curRoute", newVal, oldVal)
          let splitNewVal = newVal.split("?");
          if(splitNewVal[1] === "expend"){
            this.params.mn_use_dvs = "1";
          }else if(splitNewVal[1] === "income"){
            this.params.mn_use_dvs = "2";
          }
          this.search();
        }
    },
    */
   /*
    test: {
        immediate: true,
        handler(newVal, oldVal) {
          alert("watch curRoute")
          console.log("watch curRoute", newVal, oldVal)
          let splitNewVal = newVal.split("?");
          if(splitNewVal[1] === "expend"){
            this.params.mn_use_dvs = "1";
          }else if(splitNewVal[1] === "income"){
            this.params.mn_use_dvs = "2";
          }
          this.search();
        }
    },
    */
  },
  computed: {
    /*
    test: function(){
      let tmp = this.$router.currentRoute._value.fullPath;
      return tmp;
    }
    */
  },
  components: {
  },
  mounted() {
    //this.search();
  },
  methods: {
    search: function(){
      console.log(this.test)
      //console.log("this.$router", this.$router)
      //console.log("this.$router.currentRoute._value.fullPath", this.$router.currentRoute._value.fullPath)
      //alert("검색") 

      //let vm = this;
      let params = new URLSearchParams();
      params.append("mn_use_dvs", this.params.mn_use_dvs);
      /*
      params.append("title", this.sch_title);
      params.append("currentPage", this.currentPage);
      params.append("pageSize", this.pageSize);
      params.append("from_date", this.sch_from_date);
      params.append("to_date", this.sch_to_date);
      */
      
      this.axios
        .post("/mypage/expenditureListVue.do", params)
        .then(function (res) {
          //console.log("expenditureListVue res", res);
          //console.log("expenditureListVue res.data", res.data);
          this.items = res.data.expenditureList;
          this.items.forEach(function(exList){
            //console.log("exList", exList.sum_amount, exList)
            this.expenditureTotalAmount += exList.sum_amount;
          }.bind(this))
         console.log("this.items", this.items)
         console.log("this.expenditureTotalAmount", this.expenditureTotalAmount)
        }.bind(this))
        .catch(function (error) {
          alert("에러! API 요청에 오류가 있습니다. " + error);
        });
    },
  },
};
</script>
