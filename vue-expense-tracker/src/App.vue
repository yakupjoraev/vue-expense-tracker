<script setup>
import { ref, computed, onMounted } from 'vue'
import { useToast } from 'vue-toastification'
import Balance from './components/Balance.vue'
import Header from './components/Header.vue'
import IncomeExpenses from './components/IncomeExpenses.vue'
import TransationList from './components/TransationList.vue'
import AddTransaction from './components/AddTransaction.vue'

const toast = useToast()

const transactions = ref([
  // { id: 1, text: 'Flower', amount: -19.99 },
  // { id: 2, text: 'Salary', amount: 299.97 },
  // { id: 3, text: 'Book', amount: -10 },
  // { id: 4, text: 'Camera', amount: 300 }
])

onMounted(() => {
  const savedTransactions = JSON.parse(localStorage.getItem('transactions'))

  if (savedTransactions) {
    transactions.value = savedTransactions
  }
})

// Get total
const total = computed(() => {
  return transactions.value.reduce((acc, transaction) => {
    return acc + transaction.amount
  }, 0)
})

// Get income
const income = computed(() => {
  return transactions.value
    .filter((transaction) => transaction.amount > 0)
    .reduce((acc, transaction) => {
      return acc + transaction.amount
    }, 0)
    .toFixed(2)
})

// Get expenses
const expenses = computed(() => {
  return transactions.value
    .filter((transaction) => transaction.amount < 0)
    .reduce((acc, transaction) => {
      return acc + transaction.amount
    }, 0)
    .toFixed(2)
})

// Add transactions
const handleTransactionSubmitted = (transactionData) => {
  transactions.value.push({
    id: generateUniqueId(),
    text: transactionData.text,
    amount: transactionData.amount
  })

  saveTransactionsLocalStroge()
  toast.success('Транзакция успешно добавлена !')
}

// Generate unique ID

const generateUniqueId = () => {
  return Math.floor(Math.random() * 1000000)
}

// delete transaction

const handleTransactionDeleted = (id) => {
  transactions.value = transactions.value.filter((transaction) => {
    return transaction.id !== id
  })

  saveTransactionsLocalStroge()
  toast.success('Транзакция успешно уделана !')
}

// Save to LocalStroge
const saveTransactionsLocalStroge = () => {
  localStorage.setItem('transactions', JSON.stringify(transactions.value))
}
</script>

<template>
  <div>
    <Header />

    <div class="container">
      <Balance :total="total" />
      <IncomeExpenses :income="+income" :expenses="+expenses" />
      <TransationList :transactions="transactions" @transactionDeleted="handleTransactionDeleted" />
      <AddTransaction @transactionSubmitted="handleTransactionSubmitted" />
    </div>
  </div>
</template>


