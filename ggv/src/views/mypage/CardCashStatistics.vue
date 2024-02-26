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
            <span class="btn_nav bold">카드/현금</span>
          </p>

          <p class="conTitle">
            <span>카드/현금</span>
          </p>

          <input @change="dateChange()" type="date" v-model="startDate" /> - <input
          @change="dateChange()"
          type="date" v-model="endDate" />
          <button type="button" @click='search()'>검색</button>
          <div style="width:800px;margin-top:50px;">
            <span id="cardSpan">123</span>
            <span id="cashSpan" style="float:right;">456</span>
          </div>
          <div id="progress" class="progress" style="width:800px;height:50px;">
            <div id="cardArea" class="progress-bar" role="progressbar" aria-valuenow="15" aria-valuemin="0"
                 aria-valuemax="100" style="width: 70%;"></div>
            <div id="cashArea" class="progress-bar bg-success" role="progressbar" aria-valuenow="30"
                 aria-valuemin="0" aria-valuemax="100" style="width: 30%;"></div>
          </div>
          <div>* 체크카드 지출금액은 현금에 포함됩니다.</div>
          <div class="box-wrap">
            <div class="cardArea">
              <table class="tableCardCash">
                <tr>
                  <th>일자</th>
                  <th>카드</th>
                </tr>
                <template v-if="this.cardList.length == 0">
                  <tr>
                    <td colspan="2" class="text-center">
                      <strong>해당 일자 데이터가 없습니다</strong>
                    </td>
                  </tr>
                </template>
                <template v-else>
                  <tr v-for="(item, index) in cardList" :key="index">
                    <td class="tdDate">{{ item.mn_upd_dtm }}</td>
                    <td>{{ Number(item.sum_amount).toLocaleString() }}</td>
                  </tr>
                </template>
              </table>
            </div>
            <div class="cashArea">
              <table class="tableCardCash">
                <tr>
                  <th>일자</th>
                  <th>현금</th>
                </tr>
                <template v-if="this.cashList.length == 0">
                  <tr>
                    <td colspan="2" class="text-center">
                      <strong>해당 일자 데이터가 없습니다</strong>
                    </td>
                  </tr>
                </template>
                <template v-else>
                  <tr v-for="(item, index) in cashList" :key="index">
                    <td class="tdDate">{{ item.mn_upd_dtm }}</td>
                    <td>{{ Number(item.sum_amount).toLocaleString() }}</td>
                  </tr>
                </template>
              </table>
            </div>
          </div>
          <!--          <GChart-->
          <!--            type="LineChart"-->
          <!--            :data="chartData"-->
          <!--            :options="chartOptions"-->
          <!--          />-->
        </div>
        <!--// content -->
      </li>
    </ul>
  </div>
</template>

<script>
//import axios from 'axios';
//  import VueDatePicker from '@vuepic/vue-datepicker';
//   import '@vuepic/vue-datepicker/dist/main.css';
//import "./loader";
// import { GChart } from 'vue-google-charts';

export default {
  data: function() {
    return {
      startDate: ''
      , endDate: ''
      , card: 0
      , cash: 0
      , total: 0
      , cardPercent: 0
      , cashPercent: 0
      , cardList: ''
      , cashList: ''
      , loginId: ''
      , chartData: [
        ['Year', 'Sales', 'Expenses', 'Profit'],
        ['2014', 1000, 400, 200],
        ['2015', 1170, 460, 250],
        ['2016', 660, 1120, 300],
        ['2017', 1030, 540, 350],
      ]
      , chartOptions: {
        chart: {
          title: 'Company Performance',
          subtitle: 'Sales, Expenses, and Profit: 2014-2017',
          curveType: 'function',
        },
        // curveType: 'function',
      },
    };
  },
  mounted() {
    console.log('CardCashStatistics mounted start');
    this.loginId = this.$store.state.loginInfo.loginId;

    const date = new Date();
    const firstDay = new Date(date.getFullYear(), date.getMonth(), 1);
    const lastDay = new Date(date.getFullYear(), date.getMonth() + 1, 0);
    console.log(firstDay);
    console.log(lastDay);

    let fYear = firstDay.getFullYear();
    let fMonth = firstDay.getMonth();
    let fDate = firstDay.getDate();

    let lYear = lastDay.getFullYear();
    let lMonth = lastDay.getMonth();
    let lDate = lastDay.getDate();

    // console.log(fYear, fMonth, fDate)
    // console.log(lYear, lMonth, lDate)

    fMonth = fMonth + 1;
    lMonth = lMonth + 1;

    if (fMonth.toString().length === 1) fMonth = '0' + fMonth;
    if (fDate.toString().length === 1) fDate = '0' + fDate;
    if (lMonth.toString().length === 1) lMonth = '0' + lMonth;
    if (lDate.toString().length === 1) lDate = '0' + lDate;

    console.log(fYear + '-' + fMonth + '-' + fDate);
    console.log(lYear + '-' + lMonth + '-' + lDate);

    this.startDate = fYear + '-' + fMonth + '-' + fDate;
    this.endDate = lYear + '-' + lMonth + '-' + lDate;

    // let params = new URLSearchParams();
    // params.append("id", 'wallmart');
    // params.append("startDate", this.startDate);
    // params.append("endDate", this.endDate);

    this.search();
  },
  watch: {
    startDate: {
      handler(a, b) {
        console.log(a, b);
        // this.testMethod();
      },

    },
  },
  methods: {
    testMethod: function() {
      alert('!!!!!!');
    },
    search: async function() {
      console.log('search start');
      const vm = this;

      const returnValue = this.dateCheck(vm);
      if (!returnValue.returnValue) {
        if (returnValue.type === 'startDate') {
          alert('시작일자가 더 큽니다');
        } else {
          alert('날자범위가 180일을 초과했습니다');
        }
      }

      // console.log(this.startDate + "," + this.endDate);
      let params = new URLSearchParams();
      params.append('mn_use_dvs', '1'); // 1 : 지출, 2 : 수입
      params.append('from_date', this.startDate);
      params.append('to_date', this.endDate);
      // alert(this.loginId)
      params.append('loginId', this.loginId);
      params.append('typeChk', 'pay'); // typeChk 값이 pay면 쿼리에서 group by mn_pay_dvs
      params.append('menuName', 'CardCashStatistics');

      await this.axios
        .post('/mypage/expenditureListVue.do', params)
        .then((res) => {
          console.log('res start', res);
          let card = 0;
          let cash = 0;
          if (res.data.expenditureList.length === 1) {

            if (res.data.expenditureList[0].mn_pay_dvs == 1)
              card = res.data.expenditureList[0].sum_amount;
            else
              cash = res.data.expenditureList[0].sum_amount;

          } else if (res.data.expenditureList.length === 2) {

            card = res.data.expenditureList[0].sum_amount;
            cash = res.data.expenditureList[1].sum_amount;

          }

          const total = card + cash;

          let cardPercent = card / total * 100;
          let cashPercent = cash / total * 100;

          this.card = card;
          this.cash = cash;
          this.total = total;
          this.cardPercent = cardPercent;
          this.cashPercent = cashPercent;

          const cardSpan = document.getElementById('cardSpan');
          const cashSpan = document.getElementById('cashSpan');
          const cardArea = document.getElementById('cardArea');
          const cashArea = document.getElementById('cashArea');

          cardSpan.innerHTML = '카드 ' + card.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ',');
          cashSpan.innerHTML = cash.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ',') + ' 현금';

          if (res.data.expenditureList.length === 0) {
            cardArea.innerHTML = '0%';
            cashArea.innerHTML = '0%';
            cardArea.style.width = '50%';
            cashArea.style.width = '50%';
          } else {
            cardArea.innerHTML = cardPercent.toFixed(2) + '%';
            cashArea.innerHTML = cashPercent.toFixed(2) + '%';

            if (100 > cardPercent && cardPercent >= 90) {
              cardPercent = 90;
              cashPercent = 10;
            } else if (100 > cashPercent && cashPercent >= 90) {
              cardPercent = 10;
              cashPercent = 90;
            }

            cardArea.style.width = cardPercent + '%';
            cashArea.style.width = cashPercent + '%';
          }

          vm.cardList = res.data.cardList;
          vm.cashList = res.data.cashList;

          // mn_upd_dtm
          // sum_amount
        })
        .catch((error) => {
          console.log(error);
          alert('API 요청에 오류가 있습니다');
        });
    }
    ,
    dateChange: function() {
      // alert(this.startDate + ',' + this.endDate)

    }
    ,
    dateCheck: (vm) => {
      console.log('dateCheck start');
      let returnValue = { 'type': null, 'returnValue': false };

      let startDate = vm.startDate;
      let endDate = vm.endDate;

      const sDateArr = startDate.split('-');
      const eDateArr = endDate.split('-');

      startDate = new Date(sDateArr[0], sDateArr[1], sDateArr[2]);
      endDate = new Date(eDateArr[0], eDateArr[1], eDateArr[2]);

      if (startDate.getTime() > endDate.getTime()) returnValue.type = 'startDate';
      if ((endDate - startDate) / (24 * 60 * 60 * 1000) >= 180) returnValue.type = 180;
      if (returnValue.type === null) returnValue.returnValue = true;

      return returnValue;
    },
  },
  components: {
    // GChart,
  },
//components: { VueDatePicker }
}
;
</script>
<style>
.box-wrap {
  display: flex;

}

.cardArea {
  width: 50%;

}

.cashArea {
  width: 50%;

}

.tdDate {
  text-align: center;
}

.tableCardCash {
  border: 1px solid;
  width: 300px;
  margin-top: 50px;
}

.tableCardCash td {
  border: 1px solid;
}

.tableCardCash th {
  width: 50%;
  padding: 5px;
  text-align: center;
  background-color: gold;
  font-weight: bold;
  border: 1px solid;
}

.tableCardCash td {
  width: 50%;
  padding: 5px;
  text-align: right;
  border: 1px solid;
}
</style>