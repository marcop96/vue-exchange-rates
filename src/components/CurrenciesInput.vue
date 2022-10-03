<template>
    <div class="flex flex-row">

        <div>
            <label for="amount" class="block text-2xl font-semibold text-gray-700">Amount</label>
            <input type='number' name="amount" id="amount-input" v-model="amount"
                class="outline-blue-500 focus:border-indigo-500 pl-2 block w-full text-2xl border-gray-300 rounded-md w-54 h-12"
                placeholder="0.00" />
        </div>
        <!-- <option v-for="currency in totalCurrencies">
                {{currency}}

            </option> -->
        <div>
            <label for="initial-currency" class="block text-2xl font-semibold text-gray-700 text-gray-700">From</label>
            <select v-model="initialValue" id="initial-currency-selector"
                class="outline-blue-500 focus:border-indigo-500 block text-2xl	 border-gray-300 rounded-md mx-1 w-54 h-12">
                <option value="" selected disabled>Choose a currency</option>
                <option v-for="currency in totalCurrencies">
                    {{currency}}
                </option>

            </select>
        </div>
        <div>
            <label for="final-currency"
                class="block text-2xl font-semibold text-gray-700 text-gray-700 w-full">To</label>
            <select id="final-currency-selector" v-model='finalValue'
                class=" focus:outline-blue-500 block text-2xl border-gray-300 rounded-md mx-1 w-54 h-12">
                <option value="" selected disabled>Choose a currency</option>
                <option v-for="currency in totalCurrencies">
                    {{currency}}
                </option>
            </select>
        </div>
    </div>

    <div class="button-container flex justify-center">
        <button id="convert-button" @click='getData'
            class="convert-button btn my-4 self-center bg-violet-500 hover:bg-violet-700 rounded-lg text-white font-bold	 p-2">
            CONVERT
        </button>
    </div>

    <div>
        <div class=" result-container flex flex-col h-auto justify-center">
            <span
                class="p-3 rounded-lg  flex flex-col h-auto justify-center shadow-lg self-center bg-blue-500 text-white font-bold">{{resultText}}</span>


        </div>
    </div>
</template>

<script setup>

import { ref } from 'vue'
const amount = ref(0)
const totalCurrencies = ref('')
const initialValue = ref('')
const finalValue = ref('')
const resultText = ref('')
function getCurrencies() {
    fetch(
        "https://v6.exchangerate-api.com/v6/517e16350fa29778db9d180f/latest/USD"
    )
        .then((response) => response.json())
        .then((data) => (totalCurrencies.value = Object.keys(data.conversion_rates)))

}

function getData() {
    const baseCurrency = ref('')
    const targetCurrency = ref('')
    const conversionRate = ref('')
    fetch(`https://v6.exchangerate-api.com/v6/517e16350fa29778db9d180f/pair/${initialValue.value}/${finalValue.value}`)
        .then((response) => response.json())
        .then((data) => {
            baseCurrency.value = data.base_code;
            targetCurrency.value = data.target_code;
            conversionRate.value = data.conversion_rate;

            resultText.value = `${amount.value} ${baseCurrency.value} are ${conversionRate.value * amount.value
                } ${targetCurrency.value}`
        });

}


getCurrencies()

</script>
