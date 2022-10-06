<template>
  <div class="flex flex-row">
    <div>
      <label for="amount" class="block text-2xl font-semibold text-gray-700"
        >Amount</label
      >
      <input
        v-bind:class="amountClass"
        type="number"
        name="amount"
        id="amount-input"
        v-model="amount"
        class="border-2 pl-2 block w-full text-2xl rounded-md w-54 h-12"
        placeholder="0.00"
      />
    </div>

    <div>
      <label
        for="initial-currency"
        class="block text-2xl font-semibold text-gray-700 text-gray-700"
        >From</label
      >
      <select
        v-bind:class="initialSelectorClass"
        v-model="initialValue"
        id="initial-currency-selector"
        class="block text-2xl border-gray-300 border-2 rounded-md mx-1 w-54 h-12"
      >
        <option value="" selected disabled>Choose a currency</option>
        <option v-for="currency in totalCurrencies">
          {{ currency }}
        </option>
      </select>
    </div>
    <div>
      <label
        for="final-currency"
        class="block text-2xl font-semibold text-gray-700 text-gray-700 w-full"
        >To</label
      >
      <select
        v-bind:class="finalSelectorClass"
        id="final-currency-selector"
        v-model="finalValue"
        class="border-2 block text-2xl border-gray-300 rounded-md mx-1 w-54 h-12"
      >
        <option value="" selected disabled>Choose a currency</option>
        <option v-for="currency in totalCurrencies">
          {{ currency }}
        </option>
      </select>
    </div>
  </div>

  <div class="button-container flex justify-center">
    <button
      id="convert-button"
      @click="getData"
      class="convert-button btn my-4 self-center bg-violet-500 hover:bg-violet-700 rounded-lg text-white font-bold p-2"
    >
      CONVERT
    </button>
  </div>

  <div>
    <div class="result-container flex flex-col h-auto justify-center">
      <span
        class="p-3 rounded-lg flex flex-col h-auto justify-center shadow-lg self-center bg-blue-500 text-white font-bold"
        >{{ resultText }}</span
      >
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from "vue";
const amount = ref(0);
const totalCurrencies = ref("");
const initialValue = ref("");
const finalValue = ref("");
const resultText = ref("");
const hasAmountValue = computed(() => amount.value > 0);
const amountHasError = ref(false);
const initialSelectorHasError = ref(false);
const finalSelectorHasError = ref(false);
const amountClass = computed(() => ({
  "border-red-500": amountHasError.value, // si hasError -> true pega error en   inputClass
}));
const initialSelectorClass = computed(() => ({
  "border-red-500": initialSelectorHasError.value, // si hasError -> true pega error en   inputClass
}));
const finalSelectorClass = computed(() => ({
  "border-red-500": finalSelectorHasError.value, // si hasError -> true pega error en   inputClass
}));

const hasInitialValue = computed(() => initialValue.value.length > 0);
const hasFinalValue = computed(() => finalValue.value.length > 0);

function getCurrencies() {
  fetch(
    "https://v6.exchangerate-api.com/v6/517e16350fa29778db9d180f/latest/USD"
  )
    .then((response) => response.json())
    .then(
      (data) => (totalCurrencies.value = Object.keys(data.conversion_rates))
    );
}

function getData() {
  if (hasAmountValue.value == false) {
    amountHasError.value = true;
  } else {
    amountHasError.value = false;
  }
  if (hasInitialValue.value == false) {
    initialSelectorHasError.value = true;
  } else {
    initialSelectorHasError.value = false;
  }
  if (hasFinalValue.value == false) {
    finalSelectorHasError.value = false;
    finalSelectorHasError.value = true;
  } else {
    finalSelectorHasError.value = false;
    finalSelectorHasError.value = false;
  }
  if (amount.value > 0 && initialValue.value != "" && finalValue.value != "") {
    const baseCurrency = ref("");
    const targetCurrency = ref("");
    const conversionRate = ref("");
    fetch(
      `https://v6.exchangerate-api.com/v6/517e16350fa29778db9d180f/pair/${initialValue.value}/${finalValue.value}`
    )
      .then((response) => response.json())
      .then((data) => {
        baseCurrency.value = data.base_code;
        targetCurrency.value = data.target_code;
        conversionRate.value = data.conversion_rate;

        resultText.value = `${amount.value} ${baseCurrency.value} are ${
          conversionRate.value * amount.value
        } ${targetCurrency.value}`;
      });
  }
}

getCurrencies();
</script>
