<script setup>
import { ref } from 'vue'
import { useFetch } from '@vueuse/core'
defineProps({
  header: String,
})
function convertDollarAmount(event) {
  fetch("https://api.coinbase.com/v2/exchange-rates?currency=USD")
    .then(async response => {
      const data = await response.json();
      if (response.ok) {
        const rates = data.data["rates"];
        const btcRate = rates.BTC;
        const ethRate = rates.ETH;
        const dollarAmount = document.getElementById("dollar-amount").value;
        const btcSplit = dollarAmount * 0.7;
        const ethSplit = dollarAmount * 0.3;
        const btcAmount = (btcRate * btcSplit).toFixed(5);
        const ethAmount = (ethRate * ethSplit).toFixed(5);
        document.getElementById("btc-allocation").value = btcAmount;
        document.getElementById("eth-allocation").value = ethAmount;
      } else {
        const error = (data && data.message) || response.statusText;
        return Promise.reject(error);
      }
    })
    .catch(error => {
      this.errorMessage = error;
      console.error("There was an error!", error);
    });
}
</script>

<template>
  <h1>
    {{ header }}</h1>
  <div class="outer-container">
    <div class="inner-container">
      <div>
        <div>
          <label for="dollar-amount" value="" required>Investable assets</label>
        </div>
        <div>
          <input type="number" id="dollar-amount" name="dollar-amount">
        </div>
        <div>
          <button @click="convertDollarAmount">Convert</button>
        </div>
      </div>
    </div>
    <div class="inner-container">
      <div class="btc">
        <div>
          <label for="btc-allocation">70% BTC Allocation</label>
        </div>
        <div>
          <input id="btc-allocation" name="btc-allocation" value="" readonly>
        </div>
      </div>
      <div class="eth">
        <div>
          <label for="eth-allocation">30% ETH Allocation</label>
        </div>
        <div>
          <input id="eth-allocation" name="eth-allocation" value="" readonly>
        </div>
      </div>
    </div>
  </div>
</template>
