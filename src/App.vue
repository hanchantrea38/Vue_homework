<template>
  <div class="min-h-screen bg-gradient-to-br from-blue-50 to-indigo-100 py-8 px-4">
    <div class="max-w-6xl mx-auto">
      <!-- Header -->
      <div class="mb-8">
        <h1 class="text-4xl font-bold text-gray-800 mb-2"> Personal Finance Tracker</h1>
        <p class="text-gray-600">Manage your income and expenses with ease</p>
      </div>

      <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
        <!-- Left Column: Input Form -->
        <div class="lg:col-span-1">
          <div class="bg-white rounded-xl shadow-lg p-6">
            <h2 class="text-xl font-bold text-gray-800 mb-4">Add Transaction</h2>
            
            <div class="space-y-4">
              <input
                v-model="description"
                type="text"
                placeholder="Description"
                class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500"
              />
              
              <input
                v-model.number="amount"
                type="number"
                placeholder="Amount"
                class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500"
              />
              
              <select
                v-model="type"
                class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500 bg-white cursor-pointer"
              >
                <option value="income">Income</option>
                <option value="expense">Expense</option>
              </select>

              <button
                @click="addTransaction"
                class="w-full bg-blue-600 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded-lg transition duration-200"
              >
                Add Transaction
              </button>

              <button
                @click="clearAll"
                class="w-full bg-red-500 hover:bg-red-600 text-white font-bold py-2 px-4 rounded-lg transition duration-200"
              >
                Clear All
              </button>
            </div>

            <!-- Budget Limit Section -->
            <div class="mt-6 pt-6 border-t border-gray-200">
              <label class="block text-sm font-semibold text-gray-700 mb-2">Budget Limit: ${{ budgetLimit }}</label>
              <input
                v-model.number="budgetLimit"
                type="range"
                min="100"
                max="5000"
                step="100"
                class="w-full h-2 bg-gray-300 rounded-lg appearance-none cursor-pointer"
              />
              <p class="text-xs text-gray-500 mt-2">Set your monthly budget limit</p>
            </div>

            <!-- Notifications -->
            <div v-if="notificationLog.length" class="mt-6 p-4 bg-yellow-50 border border-yellow-200 rounded-lg">
              <p class="text-sm font-semibold text-yellow-800 mb-2">⚠️ Alerts:</p>
              <div v-for="(notification, idx) in notificationLog" :key="idx" class="text-sm text-yellow-700">
                • {{ notification }}
              </div>
            </div>
          </div>
        </div>

        <!-- Middle & Right Column: Statistics & Transactions -->
        <div class="lg:col-span-2 space-y-6">
          <!-- Stats Cards -->
          <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
            <!-- Total Income -->
            <div class="bg-gradient-to-br from-green-50 to-green-100 rounded-xl shadow-lg p-6 border-green-500">
              <p class="text-gray-600 text-sm font-semibold">Total </p>
              <p class="text-3xl font-bold text-green-600 mt-2">${{ totalIncome.toFixed(2) }}</p>
            </div>

            <!-- Total Expenses -->
            <div class="bg-gradient-to-br from-red-50 to-red-100 rounded-xl shadow-lg p-6 border-red-500">
              <p class="text-gray-600 text-sm font-semibold">Total Expenses</p>
              <p class="text-3xl font-bold text-red-600 mt-2">${{ totalExpenses.toFixed(2) }}</p>
            </div>

            <!-- Balance -->
            <div :class="['bg-gradient-to-br rounded-xl shadow-lg p-6', balance >= 0 ? 'from-blue-50 to-blue-100 border-blue-500' : 'from-orange-50 to-orange-100 border-orange-500']">
              <p class="text-gray-600 text-sm font-semibold">Balance</p>
              <p :class="['text-3xl font-bold mt-2', balance >= 0 ? 'text-blue-600' : 'text-orange-600']">
                ${{ balance.toFixed(2) }}
              </p>
            </div>

            <!-- Budget Status -->
            <div :class="['bg-gradient-to-br rounded-xl shadow-lg p-6', isOverBudget ? 'from-red-50 to-red-100 border-red-500' : 'from-purple-50 to-purple-100 border-purple-500']">
              <p class="text-gray-600 text-sm font-semibold">Budget Status</p>
              <p :class="['text-lg font-bold mt-2', isOverBudget ? 'text-red-600' : 'text-purple-600']">
                {{ budgetStatus }}
              </p>
            </div>
          </div>

          <!-- Budget Progress Bar -->
          <div class="bg-white rounded-xl shadow-lg p-6">
            <div class="flex justify-between items-center mb-3">
              <p class="font-semibold text-gray-800">Budget Progress</p>
              <span class="text-sm font-bold" :class="isOverBudget ? 'text-red-600' : 'text-green-600'">
                {{ expensePercentage }}% of budget used
              </span>
            </div>
            <div class="w-full bg-gray-200 rounded-full h-4 overflow-hidden">
              <div
                :style="{ width: Math.min(expensePercentage, 100) + '%' }"
                :class="['h-full transition-all duration-300', expensePercentage > 100 ? 'bg-red-500' : 'bg-green-500']"
              ></div>
            </div>
            <p class="text-xs text-gray-500 mt-2">Monthly limit: ${{ budgetLimit }}</p>
          </div>

          <!-- Category Summary -->
          <div class="bg-white rounded-xl shadow-lg p-6">
            <h3 class="text-lg font-bold text-gray-800 mb-4">Expense Categories</h3>
            <div v-if="Object.keys(categorySummary).length > 0" class="space-y-2">
              <div v-for="(amount, category) in categorySummary" :key="category" class="flex justify-between items-center p-3 bg-gray-50 rounded-lg">
                <span class="font-medium text-gray-700">{{ category }}</span>
                <span class="font-bold text-gray-900">${{ amount.toFixed(2) }}</span>
              </div>
            </div>
            <p v-else class="text-gray-500 text-center py-4">No transactions yet</p>
          </div>

          <!-- Filter Section -->
          <div class="bg-white rounded-xl shadow-lg p-6">
            <h3 class="text-lg font-bold text-gray-800 mb-4">Filter Transactions</h3>
            <div class="flex gap-2 flex-wrap">
              <button
                @click="filterType = 'all'"
                :class="['px-4 py-2 rounded-lg font-medium transition', filterType === 'all' ? 'bg-blue-600 text-white' : 'bg-gray-200 text-gray-700 hover:bg-gray-300']"
              >
                All
              </button>
              <button
                @click="filterType = 'income'"
                :class="['px-4 py-2 rounded-lg font-medium transition', filterType === 'income' ? 'bg-green-600 text-white' : 'bg-gray-200 text-gray-700 hover:bg-gray-300']"
              >
                Income
              </button>
              <button
                @click="filterType = 'expense'"
                :class="['px-4 py-2 rounded-lg font-medium transition', filterType === 'expense' ? 'bg-red-600 text-white' : 'bg-gray-200 text-gray-700 hover:bg-gray-300']"
              >
                Expenses
              </button>
            </div>
          </div>

          <!-- Transactions List -->
          <div class="bg-white rounded-xl shadow-lg p-6">
            <h3 class="text-lg font-bold text-gray-800 mb-4">Transactions</h3>
            <div v-if="filteredTransactions.length > 0" class="space-y-2 max-h-96 overflow-y-auto">
              <div
                v-for="transaction in filteredTransactions"
                :key="transaction.id"
                class="flex justify-between items-center p-4 bg-gray-50 rounded-lg hover:bg-gray-100 transition"
              >
                <div class="flex-1">
                  <p class="font-semibold text-gray-800">{{ transaction.desc }}</p>
                  <p class="text-sm text-gray-500">{{ transaction.date }}</p>
                </div>
                <div class="flex items-center gap-4">
                  <span
                    :class="['text-lg font-bold', transaction.type === 'income' ? 'text-green-600' : 'text-red-600']"
                  >
                    {{ transaction.type === 'income' ? '+' : '-' }}${{ transaction.amount.toFixed(2) }}
                  </span>
                  <button
                    @click="deleteTransaction(transaction.id)"
                    class="bg-red-500 hover:bg-red-600 text-white px-3 py-1 rounded-lg text-sm font-medium transition"
                  >
                    Delete
                  </button>
                </div>
              </div>
            </div>
            <p v-else class="text-gray-500 text-center py-8">No transactions yet. Start adding some!</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, watch } from 'vue'

// Reactive State
const transactions = ref([])
const filterType = ref('all')
const budgetLimit = ref(1000)
const description = ref('')
const amount = ref('')
const type = ref('income')
const notificationLog = ref([])

// Computed Properties
const filteredTransactions = computed(() => {
  if (filterType.value === 'income') {
    return transactions.value.filter(t => t.type === 'income')
  }
  if (filterType.value === 'expense') {
    return transactions.value.filter(t => t.type === 'expense')
  }
  return transactions.value
})

const totalIncome = computed(() => {
  return transactions.value
    .filter(t => t.type === 'income')
    .reduce((sum, t) => sum + t.amount, 0)
})

const totalExpenses = computed(() => {
  return transactions.value
    .filter(t => t.type === 'expense')
    .reduce((sum, t) => sum + t.amount, 0)
})

const balance = computed(() => {
  return totalIncome.value - totalExpenses.value
})

const isOverBudget = computed(() => {
  return totalExpenses.value > budgetLimit.value
})

const expensePercentage = computed(() => {
  return budgetLimit.value > 0 ? Math.round((totalExpenses.value / budgetLimit.value) * 100) : 0
})

const categorySummary = computed(() => {
  const summary = {}
  transactions.value
    .filter(t => t.type === 'expense')
    .forEach(t => {
      summary[t.desc] = (summary[t.desc] || 0) + t.amount
    })
  return summary
})

const budgetStatus = computed(() => {
  if (isOverBudget.value) {
    return ` Over Budget by $${(totalExpenses.value - budgetLimit.value).toFixed(2)}`
  }
  return ` ${(budgetLimit.value - totalExpenses.value).toFixed(2)} remaining`
})

// Methods
function addTransaction() {
  if (!description.value.trim() || !amount.value || amount.value <= 0) {
    alert('Please fill in all fields with valid values')
    return
  }

  const newTransaction = {
    id: Date.now(),
    desc: description.value,
    amount: parseFloat(amount.value),
    type: type.value,
    date: new Date().toLocaleDateString()
  }

  transactions.value.push(newTransaction)
  description.value = ''
  amount.value = ''
  type.value = 'income'
}

function deleteTransaction(id) {
  transactions.value = transactions.value.filter(t => t.id !== id)
}

function clearAll() {
  if (confirm('Are you sure you want to delete all transactions?')) {
    transactions.value = []
    notificationLog.value = []
  }
}

// Watchers
watch(transactions, (newVal) => {
  localStorage.setItem('transactions', JSON.stringify(newVal))
}, { deep: true })

watch(balance, (newBalance) => {
  if (newBalance < 0) {
    console.warn(' Warning: Your balance is negative!')
  }
})

watch(isOverBudget, (newVal) => {
  if (newVal) {
    const message = ` Budget exceeded! Spent $${totalExpenses.value.toFixed(2)} of $${budgetLimit.value}`
    if (!notificationLog.value.includes(message)) {
      notificationLog.value.push(message)
    }
  } else {
    notificationLog.value = notificationLog.value.filter(
      msg => !msg.includes('Budget exceeded')
    )
  }
})

// Load transactions from localStorage on mount
const savedTransactions = localStorage.getItem('transactions')
if (savedTransactions) {
  transactions.value = JSON.parse(savedTransactions)
}
</script>