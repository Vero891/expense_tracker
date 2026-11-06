<script setup>
import { ref, computed, watch } from "vue";

const expenses = ref(
  JSON.parse(localStorage.getItem("pennywise-expenses")) || [
    {
      name: "Campus bookstore",
      date: "Oct 24, 2026",
      category: "Education",
      amount: 64.2,
    },
    {
      name: "Lunch with friends",
      date: "Oct 22, 2026",
      category: "Food",
      amount: 18.5,
    },
    {
      name: "Monthly bus pass",
      date: "Oct 20, 2026",
      category: "Transport",
      amount: 42.0,
    },
    {
      name: "Coffee before class",
      date: "Oct 18, 2026",
      category: "Food",
      amount: 5.75,
    },
    {
      name: "Design software",
      date: "Oct 16, 2026",
      category: "Subscriptions",
      amount: 12.99,
    },
  ],
);

const activePage = ref("Dashboard");
const showForm = ref(false);

const totalSpending = computed(() => {
  return expenses.value.reduce((total, expense) => total + expense.amount, 0);
});

watch(
  expenses,
  (newExpenses) => {
    localStorage.setItem("pennywise-expenses", JSON.stringify(newExpenses));
  },
  { deep: true },
);

const newExpense = ref({
  name: "",
  amount: "",
  date: "",
  category: "Food",
});

function addExpense() {
  if (!newExpense.value.name || !newExpense.value.amount) return;

  expenses.value.unshift({
    name: newExpense.value.name,
    amount: Number(newExpense.value.amount),
    date: newExpense.value.date || new Date().toLocaleDateString(),
    category: newExpense.value.category,
  });

  newExpense.value = { name: "", amount: "", date: "", category: "Food" };
  showForm.value = false;
}
</script>

<template>
  <div class="min-h-screen bg-slate-50 md:flex">
    <aside
      class="w-full border-r border-slate-200 bg-white p-5 md:flex md:min-h-screen md:w-52 md:flex-col"
    >
      <div class="mb-10 flex items-center gap-3">
        <div class="rounded-xl bg-teal-700 p-3 text-white">▣</div>
        <div>
          <h2 class="font-bold text-slate-900">Pennywise</h2>
          <p class="text-xs text-slate-400">Student expenses</p>
        </div>
      </div>

      <nav class="space-y-2">
        <button
          v-for="page in ['Dashboard', 'Transactions', 'Add Expense', 'About']"
          :key="page"
          @click="
            page === 'Add Expense' ? (showForm = true) : (activePage = page)
          "
          class="w-full rounded-lg px-3 py-3 text-left text-sm font-medium"
          :class="
            activePage === page
              ? 'bg-teal-50 text-teal-800'
              : 'text-slate-600 hover:bg-slate-50'
          "
        >
          {{
            page === "Dashboard"
              ? "▦"
              : page === "Transactions"
                ? "▤"
                : page === "Add Expense"
                  ? "+"
                  : "ⓘ"
          }}
          <span class="ml-2">{{ page }}</span>
        </button>
      </nav>

      <div class="mt-auto hidden rounded-xl bg-slate-50 p-4 md:block">
        <p class="text-xs font-medium text-slate-400">MONTHLY BUDGET</p>
        <p class="mt-2 font-bold">
          $742.80
          <span class="text-xs font-normal text-slate-400">of $1,000</span>
        </p>
        <div class="mt-3 h-1.5 rounded-full bg-slate-200">
          <div class="h-1.5 w-3/4 rounded-full bg-teal-600"></div>
        </div>
      </div>
    </aside>

    <main class="min-w-0 flex-1 p-6 md:p-10">
      <div class="mx-auto max-w-5xl">
        <div class="mb-7 flex flex-wrap items-center justify-between gap-4">
          <div>
            <p class="text-xs font-semibold text-teal-700">OCTOBER 2026</p>
            <h1 class="mt-2 text-2xl font-bold text-slate-900">
              Good morning, Veronica
            </h1>
            <p class="mt-1 text-sm text-slate-500">
              Here's a quick look at your spending.
            </p>
          </div>

          <button
            @click="showForm = true"
            class="rounded-lg bg-teal-700 px-5 py-3 text-sm font-semibold text-white hover:bg-teal-800"
          >
            + Add Expense
          </button>
        </div>

        <div v-if="activePage === 'Dashboard' || activePage === 'Transactions'">
          <div
            v-if="activePage === 'Dashboard'"
            class="mb-6 grid gap-4 md:grid-cols-2"
          >
            <div class="rounded-2xl bg-teal-700 p-6 text-white">
              <p class="text-sm font-medium">Total spending</p>
              <p class="mt-2 text-3xl font-bold">
                ${{ totalSpending.toFixed(2) }}
              </p>
              <p class="mt-3 text-xs text-teal-100">All time</p>
            </div>

            <div class="rounded-2xl border border-slate-200 bg-white p-6">
              <p class="text-sm text-slate-500">This month</p>
              <p class="mt-2 text-3xl font-bold text-slate-900">$742.80</p>
              <p class="mt-3 text-xs text-teal-700">↓ 8% from September</p>
            </div>
          </div>

          <section
            class="overflow-hidden rounded-2xl border border-slate-200 bg-white"
          >
            <div
              class="flex items-center justify-between border-b border-slate-100 p-5"
            >
              <div>
                <h2 class="font-bold text-slate-900">
                  {{
                    activePage === "Dashboard"
                      ? "Recent expenses"
                      : "All transactions"
                  }}
                </h2>
                <p class="mt-1 text-xs text-slate-400">
                  {{ expenses.length }} expenses recorded
                </p>
              </div>
              <button
                v-if="activePage === 'Dashboard'"
                @click="activePage = 'Transactions'"
                class="text-sm font-medium text-teal-700"
              >
                View all ›
              </button>
            </div>

            <div
              v-for="expense in expenses"
              :key="expense.name"
              class="grid grid-cols-2 gap-3 border-b border-slate-100 p-4 text-sm sm:grid-cols-4"
            >
              <div class="font-semibold text-slate-800">{{ expense.name }}</div>
              <div class="text-slate-500">{{ expense.date }}</div>
              <div>
                <span
                  class="rounded-full bg-teal-50 px-3 py-1 text-xs text-teal-800"
                >
                  {{ expense.category }}
                </span>
              </div>
              <div class="text-right font-bold text-slate-900">
                ${{ expense.amount.toFixed(2) }}
              </div>
            </div>

            <div
              v-if="expenses.length === 0"
              class="p-8 text-center text-slate-500"
            >
              No expenses recorded yet.
            </div>
          </section>
        </div>

        <section
          v-else-if="activePage === 'About'"
          class="rounded-2xl border border-slate-200 bg-white p-8"
        >
          <h2 class="mb-3 text-xl font-bold text-slate-900">About Pennywise</h2>
          <p class="text-slate-600">
            Pennywise is a simple expense tracker designed to help students
            organize their everyday spending.
          </p>
        </section>

        <div
          class="mt-6 rounded-xl bg-white p-4 text-sm text-slate-500 md:hidden"
        >
          Monthly budget: $742.80 of $1,000
        </div>
      </div>
    </main>

    <div
      v-if="showForm"
      class="fixed inset-0 z-10 flex items-center justify-center bg-black/40 p-4"
    >
      <form
        @submit.prevent="addExpense"
        class="w-full max-w-md rounded-2xl bg-white p-6 shadow-xl"
      >
        <h2 class="mb-5 text-xl font-bold text-slate-900">Add an expense</h2>

        <label class="mb-2 block text-sm font-medium">Expense name</label>
        <input
          v-model="newExpense.name"
          required
          class="mb-4 w-full rounded-lg border border-slate-300 p-3"
          placeholder="e.g. Groceries"
        />

        <label class="mb-2 block text-sm font-medium">Amount</label>
        <input
          v-model="newExpense.amount"
          type="number"
          min="0.01"
          step="0.01"
          required
          class="mb-4 w-full rounded-lg border border-slate-300 p-3"
          placeholder="0.00"
        />

        <label class="mb-2 block text-sm font-medium">Date</label>
        <input
          v-model="newExpense.date"
          type="date"
          class="mb-4 w-full rounded-lg border border-slate-300 p-3"
        />

        <label class="mb-2 block text-sm font-medium">Category</label>
        <select
          v-model="newExpense.category"
          class="mb-6 w-full rounded-lg border border-slate-300 p-3"
        >
          <option>Food</option>
          <option>Education</option>
          <option>Transport</option>
          <option>Subscriptions</option>
          <option>Other</option>
        </select>

        <div class="flex justify-end gap-3">
          <button
            type="button"
            @click="showForm = false"
            class="rounded-lg border px-4 py-2"
          >
            Cancel
          </button>
          <button
            type="submit"
            class="rounded-lg bg-teal-700 px-4 py-2 text-white"
          >
            Save expense
          </button>
        </div>
      </form>
    </div>
  </div>
</template>
