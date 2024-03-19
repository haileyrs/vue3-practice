<template>
  <Header />
  <div class="container">
    <Balance :total="+total"/>
    <IncomeExpenses :income="+income" :expenses="+expenses"/>
  </div>
  <TransactionList :transactions="transactions" @transactionDeleted="handleDelete" />
  <AddTransaction @transactionSubmitted="handleTransactionSubmitted" />
</template>

<!-- <script lang="ts">
  import Header from './components/Header.vue'
  import Balance from './components/Balance.vue'
  import IncomeExpenses from './components/IncomeExpenses.vue';
  import TransactionList from './components/TransactionList.vue';
  import AddTransaction from './components/AddTransaction.vue';

  export default {
    components: {
      Header,
      Balance,
      IncomeExpenses,
      TransactionList,
      AddTransaction
    }
  }
</script> -->

<script setup>
  import Header from './components/Header.vue'
  import Balance from './components/Balance.vue'
  import IncomeExpenses from './components/IncomeExpenses.vue';
  import TransactionList from './components/TransactionList.vue';
  import AddTransaction from './components/AddTransaction.vue';

  import { ref, computed, onMounted } from 'vue';
  import { useToast } from 'vue-toastification';

  const toast = useToast();

  const transactions = ref([]);

  onMounted(() => {
    const savedTransactions = JSON.parse(localStorage.getItem('transactions'))
    if (savedTransactions) {
      transactions.value = savedTransactions;
    }
  })

  const total = computed(() => {
    return transactions.value.reduce((acc, t) => {
      return acc + t.amount
    }, 0)
  });

  const income = computed(() => {
    return transactions.value
    .filter((n) => n.amount > 0)
    .reduce((acc, t) =>{
      return acc + t.amount
    }, 0).toFixed(2)
  });

  const expenses = computed(() => {
    return transactions.value
    .filter((n) => n.amount < 0)
    .reduce((acc, t) =>{
      return acc + t.amount
    }, 0).toFixed(2)
  });

  const handleTransactionSubmitted = (transactionData) => {
    let newTransaction = {
      id: generateUniqueId(),
      text: transactionData.text,
      amount: transactionData.amount
    }

    transactions.value.push(newTransaction)
    saveToLocal();
    toast.success('Transaction added!')
  };

  const handleDelete = (id) => {
    transactions.value = transactions.value.filter((t) => t.id !== id)
    saveToLocal();
    toast.success('Transaction Deleted!')
  }

  const saveToLocal = () => {
    localStorage.setItem('transactions', JSON.stringify(transactions.value))
  }

  const generateUniqueId = () => {
    return Math.floor(Math.random() * 1000000)
  }
</script>


