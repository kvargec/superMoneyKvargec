<template xmlns="http://www.w3.org/1999/html">
  <div v-if="!showCalc">

    <div class="row">
      <div class="col-lg-3 fw-bold">DEBT NAME</div>
      <div class="col-lg-3 fw-bold">REMAINING DEBT AMOUNT</div>
      <div class="col-lg-2 fw-bold">CURRENT APR</div>
      <div class="col-lg-3 fw-bold">CURRENT MONTHLY PAYMENT</div>
      <div class="col-lg-1 fw-bold">&nbsp;</div>
    </div>
    <div class="row mt-2" v-for="(debt, index) in debts">
      <div class="col-lg-3">
        <div class="input-group">
          <input type="text" v-bind:name="'db_'+index" class="form-control" v-model="debt.debt_name" placeholder="e.g.Medical">
        </div>
      </div>
      <div class="col-lg-3">
        <div class="input-group">
          <div class="input-group-text">$</div>
          <input class="form-control" type="number" v-bind:name="'rm_'+index"  v-model="debt.remain" placeholder="5,000">
        </div>
      </div>
      <div class="col-lg-2">
        <div class="input-group">
          <input class="form-control" type="number" v-model="debt.apr"  v-bind:name="'apr_'+index"  placeholder="15.5">
          <div class="input-group-text">%</div>
        </div>
      </div>
      <div class="col-lg-3">
        <div class="input-group">
          <div class="input-group-text">$</div>
          <input class="form-control" type="number" v-model="debt.monthly"  v-bind:name="'mon_'+index" placeholder="300">
        </div>
      </div>
      <div class="col-lg-1">
        <div class="input-group">
          <button @click="deleteRow(index)" class="btn btn-close" v-show="index>0"></button>
        </div>
      </div>
    </div>
    <button class="btn text-primary fw-bold" @click="addRow()">
      <BIconPlus></BIconPlus>
      Add Another Debt
    </button>
    <div class="row">
      <div class="col-lg-12">
        <button class="btn btn-primary col-lg-11" @click="showCalc=!showCalc" :disabled="isDisable">Calculate Savings</button>
      </div>
    </div>
  </div>
  <div v-else>
    <button class="btn text-primary fw-bold" @click="showCalc=!showCalc">
      <BIconArrowLeft></BIconArrowLeft>
      Update Your Current Debts
    </button>
    <div class="card">
      <div class="card-body">
        <div class="row">
          <div class="col-lg-8">
            <p class="fw-bold text-uppercase">Configure your consolidated loan</p>
            <p>Use the sliders below to simulate the new APR and loan term</p>
          </div>
        </div>
        <div class="row">
          <div class="col-lg-3">
            <p>DESIRED APR</p>
            <h4 class="text-primary">{{ sliderapr.value.toFixed(2) }}%</h4>
          </div>
          <div class="col-lg-5">
            <div>
              <vue-slider v-model="sliderapr.value" :min="sliderapr.min" :marks="sliderapr.marks" :max="sliderapr.max"
                          :interval="sliderapr.interval" :tooltip-formatter="sliderapr.formatter1"></vue-slider>
            </div>
          </div>
        </div>
        <div class="row">
          <div class="col-lg-3">
            <p>DESIRED LOAN TERM</p>
            <h4 class="text-primary">{{ sliderLoan.value }} months</h4>
          </div>
          <div class="col-lg-5">
            <div>
              <vue-slider v-model="sliderLoan.value" :min="sliderLoan.min" :marks="sliderLoan.marks"
                          :max="sliderLoan.max"
                          :interval="sliderLoan.interval" :tooltip-formatter="sliderLoan.formatter1"></vue-slider>
            </div>
          </div>
        </div>
        <hr />
        <div class="row">
          <div class="col-lg-6  border-end">
            <div class="row">
              <div class="col-lg-6 h6">New Total Repayment</div>
              <div class="col-lg-4 h6"><span class="text-primary">{{ formatCurrency(newSuma) }}</span></div>
            </div>
            <div class="row">
              <div class="col-lg-6 h6">Current Total Repayment</div>
              <div class="col-lg-4 h6"><span class="text-primary">{{ formatCurrency(repay) }}</span></div>
            </div>
            <div class="row">
              <div class="col-lg-6 h6">Total Repayment Savings</div>
              <div class="col-lg-4 h6"><span class="text-primary">{{ formatCurrency((repay - newSuma)) }}</span></div>
            </div>
          </div>
          <div class="col-lg-6">
            <div class="row">
              <div class="col-lg-6 h6">New Monthly Payment</div>
              <div class="col-lg-4 h6"><span class="text-primary">{{ formatCurrency(newMonth) }}</span></div>
            </div>
            <div class="row">
              <div class="col-lg-6 h6">Current Monthly Payment</div>
              <div class="col-lg-4 h6"><span class="text-primary">{{ formatCurrency(sumaPay) }}</span></div>
            </div>
            <div class="row">
              <div class="col-lg-6 h6">Total Monthly Savings</div>
              <div class="col-lg-6 h6"><span class="text-primary">{{ formatCurrency(sumaPay - newMonth) }}</span></div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>


</template>

<script>
import * as fin from "../assets/finance.js";
import VueSlider from 'vue-slider-component'
import 'vue-slider-component/theme/default.css'
import {BIconArrowLeft, BIconPlus, BIconX} from 'bootstrap-icons-vue';

export default {
  name: "Calculator",
  components: {VueSlider, BIconArrowLeft, BIconPlus, BIconX},
  showCalc: false,
  suma: 0,
  setup() {


  },
  data() {
    return {
      showCalc: false,
      suma: 0,
      new_suma: 0,
      sumaPay: 0,
      repay: 0,
      new_month: 0,
      //isDisable:false,
      debts: [{debt_name: 'credit card', remain: '5000', apr: '15.5', monthly: '300'}, {
        debt_name: 'medical',
        remain: '1000',
        apr: '11.25',
        monthly: '150'
      }],
      sliderapr: {
        value: 8,
        min: 4,
        max: 36,
        interval: 0.5,
        formatter1: '{value}%',
        marks: {'4': '4%', '36': '36%'}
      },
      sliderLoan: {
        value: 24,
        min: 12,
        max: 60,
        interval: 12,
        marks: {'12': '12', '60': '60'},
        formatter1: '{value} month',
      }
    }

    // return debts;
  },
  methods: {

    formatCurrency(value) {
      return fin.finance.format(value, 'USD', {precision: 2});
    },
    addRow: function () {
      this.debts.push({debt_name: '', remain: '', apr: '', monthly: ''});
      console.log(this.debts.length)
    },
    deleteRow(index) {
      this.debts.splice(index, 1);
    },
    saveFile: function() {
      const data = JSON.stringify(this.debts)
      window.localStorage.setItem('debts', data);
      console.log(JSON.parse(window.localStorage.getItem('debts')))
    }
  },
  computed: {
    isDisable(){
      let temp=false;
      this.debts.forEach(element => {
        if(element.remain==='') temp=true;
        if(element.monthly==='') temp=true;
        if(element.apr==='') temp=true;
        if(element.debt_name==='') temp=true;
      });
      return temp;
    },
    newMonth() {
      this.suma = 0;
      this.sumaPay = 0;
      this.repay = 0;
      this.debts.forEach(element => {
        let tmonths = element.remain / element.monthly;
        this.repay += element.remain * (1 + element.apr / 100);
        this.suma += parseFloat(element.remain);
        this.sumaPay += parseFloat(element.monthly);
      });
      this.new_month = fin.finance.calculatePayment(this.suma, this.sliderLoan.value, this.sliderapr.value);
      return fin.finance.calculatePayment(this.suma, this.sliderLoan.value, this.sliderapr.value);
    },
    newSuma() {
      return this.sliderLoan.value * this.new_month;
      //return fin.finance.calculateAmount(this.sliderLoan.value , this.sliderapr.value,this.new_month);
    }
  }

}
</script>
<style src="@vueform/slider/themes/default.css"></style>
<style scoped>

</style>