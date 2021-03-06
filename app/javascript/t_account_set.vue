<template>
  <div>
    <h1>{{ title }}</h1>

    <div class="container">
      <div class="account" 
        v-for="(account, index) in accounts" :key="account.id">

        <t-account
          :id="account.id"
          :account="account"
          :canEdit="canEdit" 
        />

        <i v-if="equalsPos >= 0 && index != accounts.length - 1" 
          ref="eqsymbol"
          v-bind:class="[
            'equation', 
            'fas', 
            index == equalsPos ? (isEqual ? 'fa-equals' : 'fa-not-equal') : 'fa-plus']
          ">
        </i>

      </div>
    </div>
  </div>
</template>

<script>
import TAccount from './t_account.vue';
import uuid from 'uuid';

export default {
  props: {
    title: String,
    accounts: Array,
    canEdit: Boolean,
    equalsPos: Number,
  },

  methods: {

    evaluateEquation() {
      let leftHand = 0, rightHand = 0;
      for (let i = 0; i < this.accounts.length; i++) { 
        leftHand += i <= this.equalsPos ? this.sumAccount(this.accounts[i]) : 0;
        rightHand += i > this.equalsPos ? this.sumAccount(this.accounts[i]) : 0;
      }
      return (leftHand + rightHand);
    },

    sumAccount(account) {
      return account.transactions.reduce((sum, tx) => sum + (tx.side == 'debit' ? tx.amount : -tx.amount), 0);
    },

    highlightLinks(e) {
      console.log('hover')
      this.$emit('highlight', e.target.props.account);
    }
  },

  computed: {
    isEqual() {
      return this.evaluateEquation() == 0;
    }
  },
  
  mounted() {
    
    this.evaluateEquation();

    this.$on('account-changed', () => {
      this.evaluateEquation();
    })
  },

  components: {
    TAccount
  }
}
</script>

<style scoped>
h1 {
  font-size: 2em;
  text-align: center;
}

.container {
  display: flex;
  flex-flow: row wrap;
  justify-content: center;
}

.account {
  display: flex;
  align-items: flex-start;
}

t-account {
  margin: 0 5px;
}

.equation {
  margin-top: 24px;
}

.fa-equals {
  color: limegreen;
}
.fa-not-equal {
  color: lightcoral;
}

</style>
