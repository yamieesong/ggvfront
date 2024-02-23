<template>
  <div id="container">
    <ul>
      <li class="contents">
        <!-- contents -->
        <!-- content -->
        <div class="content" style="padding-bottom: 20px;">
          <p class="Location">
            <a class="btn_set home">메인으로</a>
            <a class="btn_nav bold">마이페이지</a>
            <span class="btn_nav bold">수입 통계</span>
          </p>

          <p class="conTitle">
            <span>수입 통계</span>
          </p>

          <!-- <GChart :type="chartType" :data="chartData" :options="chartOptions" /> -->
          <GChart v-show="incomeTotalAmount > 0" :type="chartType" :data="chartData" :options="chartOptions" style="padding-left: 30%;"/>
            <div style="width: 50%; left: 32%; position: relative; margin-bottom: 20px;">
              <input
                type="date"
                style="width: 120px; margin-right: 20%;"
                id="from_date"
                name="from_date"
                v-model="sch_from_date"
              />
              <input
                type="date"
                style="width: 120px"
                id="to_date"
                name="to_date"
                v-model="sch_to_date"
              />
            </div>
          <!--
          <table
            width="100%"
            cellpadding="5"
            cellspacing="0"
            border="1"
            align="left"
            style="border-collapse: collapse; border: 1px #50bcdf; margin-bottom: 30px;"
          >
            <tr style="border: 0px; border-color: blue">
              <td width="100" height="25" style="font-size: 120%">
                &nbsp;&nbsp;
              </td>
              
              <td width="50" height="25" style="font-size: 100%">
                <input
                  type="date"
                  style="width: 120px;"
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
          -->
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
                    <td>{{ val.detail_name }}</td>
                    <td>{{ val.sum_amount.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",") + "원" }}</td>
                    <td>{{ (val.sum_amount / incomeTotalAmount * 100).toFixed(1) + "%" }}</td>
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
import { GChart } from 'vue-google-charts';

export default {
  data: function () {
    return {
      items: [],
      itemsCnt: 0,
      incomeTotalAmount: 0,
      sch_from_date: "",
      sch_to_date: "",

      chartType: "PieChart",
      chartOptions: {
        pieHole: 0.4,
        width: 450,
        height: 300,
        is3D: true,
      },
      chartData: [
      ],
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
    GChart: GChart,
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
    sch_from_date: {
      immediate: false,
      handler(newVal, oldVal){
        if(newVal != oldVal){
          this.getList();
        }
      }
    },
    chartData: {
      immediate: false,
      handler(newVal, oldVal){
        if(newVal != oldVal){
          this.reloadGChart(newVal, this.chartOptions, this.chartType);
        }
      }
    },
  },
  methods: {
    reloadGChart(data, options, type) {
      return () =>
        (GChart, {
          data,
          options,
          type,
        });
    },
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

          this.chartData = [];
          this.incomeTotalAmount = 0;
          
          this.chartData.push(["detail_name", "sum_amount"]);
          this.items.forEach(function(exList){
            this.incomeTotalAmount += exList.sum_amount;
          }.bind(this))

          this.items.forEach(function(items){
            //console.log("items", items)
            this.chartData.push([items.detail_name, items.sum_amount]);
          }.bind(this))

        }.bind(this))
        .catch(function (error) {
          alert("에러! API 요청에 오류가 있습니다. " + error);
        });
    },
  },
};
</script>
