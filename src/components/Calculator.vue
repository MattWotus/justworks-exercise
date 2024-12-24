<script setup>
import { onMounted } from "vue"
defineProps({
  header: String,
})
onMounted(() => {
  const dollarAmount = document.getElementById("dollar-amount");
  const resetButton = document.getElementById("reset-button");
  const dateContainer = document.getElementById("date-container");
  const btcAllocation = document.getElementById("btc-allocation");
  const ethAllocation = document.getElementById("eth-allocation");
  const btcRateContainer = document.getElementById("btc-rate-container");
  const ethRateContainer = document.getElementById("eth-rate-container");
  let btcRate;
  let ethRate;
  const url = "https://api.coinbase.com/v2/exchange-rates?currency=USD";
  function getRates() {
    fetch(url)
      .then(async response => {
        const data = await response.json();
        if (response.ok) {
          const rates = data.data["rates"];
          btcRate = rates.BTC;
          ethRate = rates.ETH;
          btcRateContainer.innerText = parseFloat(btcRate).toFixed(6);
          ethRateContainer.innerText = parseFloat(ethRate).toFixed(6);
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
  getRates();
  function getDate() {
    const date = new Date().toLocaleString();
    dateContainer.innerText = date;
  }
  getDate();
  dollarAmount.addEventListener("input", function () {
    const dollarAmountValue = dollarAmount.value;
    const btcSplit = dollarAmountValue * 0.7;
    const ethSplit = dollarAmountValue * 0.3;
    getRates();
    const btcAmount = (btcRate * btcSplit).toFixed(6);
    const ethAmount = (ethRate * ethSplit).toFixed(6);
    btcAllocation.value = btcAmount;
    ethAllocation.value = ethAmount;
  })
  resetButton.addEventListener("click", function () {
    dollarAmount.value = "";
    btcAllocation.value = "";
    ethAllocation.value = "";
    getRates();
    getDate();
    dollarAmount.focus();
  })
});
</script>

<template>
  <h1>
    {{ header }}</h1>
  <div class="outer-container">
    <div class="inner-container dollars">
      <div class="dollar-input">
        <label for="dollar-amount">Investable Assets (USD)</label>
        <input type="number" min="0" step="0.01" id="dollar-amount" name="dollar-amount" autofocus="autofocus"
          onkeydown="return event.keyCode !== 69 && event.keyCode !== 187 && event.keyCode !== 189">
      </div>
      <div id="reset">
        <button id="reset-button">Reset</button>
      </div>
      <div id="date">Conversion rates are as of <span id="date-container"></span> EST</div>
    </div>
    <div class="inner-container crypto">
      <div class="btc">
        <label for="btc-allocation">70% BTC Allocation</label>
        <input id="btc-allocation" name="btc-allocation" readonly>
        <div class="conversion-rate">1 USD &#8776; <span id="btc-rate-container"></span> BTC</div>
      </div>
      <div class="eth">
        <label for="eth-allocation">30% ETH Allocation</label>
        <input id="eth-allocation" name="eth-allocation" readonly>
        <div class="conversion-rate">1 USD &#8776; <span id="eth-rate-container"></span> ETH</div>
      </div>
    </div>
  </div>
</template>
