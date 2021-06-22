<template>
  <div class="home">

    <div class="col-lg-6 offset-3">
      <h3>Dept Consolidation Savings Calculator</h3>
      <p class="h6 mb-3">Enter the details of your current unsecured dept and see how much you may be able to save after consolidating the debts into a single loan.
        Only include credit card debt, medical debt, personal loan debt, and other od unsecured debt.</p>
   <Calculator/>


    </div>

  </div>
</template>

<script>

import Calculator from "@/components/Calculator";

export default {
  name: 'Home',
  date:function(){
    return{
    }

  },
  components: {

    Calculator,

  },
}
</script>
