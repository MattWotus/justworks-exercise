<script setup>
import { onMounted } from "vue"
defineProps({
  header: String,
})
onMounted(() => {
  const dollarAmount = document.getElementById("dollar-amount");
  const btcAllocation = document.getElementById("btc-allocation");
  const ethAllocation = document.getElementById("eth-allocation");
  const resetButton = document.getElementById("reset");
  const url = "https://api.coinbase.com/v2/exchange-rates?currency=USD";
  dollarAmount.addEventListener("input", function () {
    fetch(url)
      .then(async response => {
        const data = await response.json();
        if (response.ok) {
          const rates = data.data["rates"];
          const btcRate = rates.BTC;
          const ethRate = rates.ETH;
          const dollarAmountValue = dollarAmount.value;
          const btcSplit = dollarAmountValue * 0.7;
          const ethSplit = dollarAmountValue * 0.3;
          const btcAmount = (btcRate * btcSplit).toFixed(5);
          const ethAmount = (ethRate * ethSplit).toFixed(5);
          btcAllocation.value = btcAmount;
          ethAllocation.value = ethAmount;
        } else {
          const error = (data && data.message) || response.statusText;
          return Promise.reject(error);
        }
      })
      .catch(error => {
        this.errorMessage = error;
        console.error("There was an error!", error);
      });
  });
  resetButton.addEventListener("click", function () {
    dollarAmount.value = "";
    btcAllocation.value = "";
    ethAllocation.value = "";
  })
})
</script>

<template>
  <h1>
    {{ header }}</h1>
  <div class="outer-container">
    <div class="inner-container">
      <div>
        <div>
          <label for="dollar-amount">Investable Assets (USD)</label>
        </div>
        <div>
          <input type="number" min="0" id="dollar-amount" name="dollar-amount" autofocus="autofocus">
        </div>
        <div>
          <button id="reset">Reset</button>
        </div>
      </div>
    </div>
    <div class="inner-container">
      <div class="btc">
        <div>
          <label for="btc-allocation">70% BTC Allocation</label>
        </div>
        <div>
          <input id="btc-allocation" name="btc-allocation" readonly>
        </div>
      </div>
      <div class="eth">
        <div>
          <label for="eth-allocation">30% ETH Allocation</label>
        </div>
        <div>
          <input id="eth-allocation" name="eth-allocation" readonly>
        </div>
      </div>
    </div>
  </div>
</template>
