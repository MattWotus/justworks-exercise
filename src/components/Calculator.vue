<script setup>
import { onMounted } from "vue"
defineProps({
  header: String,
})
onMounted(() => {
  const dollarAmount = document.getElementById("dollar-amount");
  const resetButton = document.getElementById("reset-button");
  const dateText = document.getElementById("date");
  const dateContainer = document.getElementById("date-container");
  const errorContainer = document.getElementById("error");
  const btcAllocation = document.getElementById("btc-allocation");
  const ethAllocation = document.getElementById("eth-allocation");
  const btcRateContainer = document.getElementById("btc-rate-container");
  const ethRateContainer = document.getElementById("eth-rate-container");
  let fetchCounter = 0;
  let btcRate;
  let ethRate;
  const url = "https://api.coinbase.com/v2/exchange-rates?currency=USD";
  function getRates() {
    fetch(url)
      .then(response => {
        if (!response.ok) {
          throw new Error('Network response was not ok');
        }
        return response.json();
      })
      .then(data => {
        const rates = data.data["rates"];
        btcRate = rates.BTC;
        ethRate = rates.ETH;
        btcRateContainer.innerText = parseFloat(btcRate).toFixed(10);
        ethRateContainer.innerText = parseFloat(ethRate).toFixed(10);
      })
      .catch(error => {
        errorContainer.style.visibility = "visible";
        console.error("There was an error!", error);
      });
  }
  // The getRates() function fetches the cryptocurrency exchange rates from the given API endpoint, parses the body text of the response as JSON, finds the exchange rates for BTC and ETH, and displays the exchange rates on the webpage. If there is an error fetching the cryptocurrency exchange rates, the function displays an error message on the webpage. //
  getRates();
  function getDate() {
    const date = new Date().toLocaleString();
    dateContainer.innerText = date;
  }
  // The getDate() function creates a new date object, converts the object to a string of the date in the local timezone, and displays the date on the webpage. //
  getDate();
  dollarAmount.addEventListener("input", function (e) {
    const dollarAmountValue = e.target.value;
    const btcSplit = dollarAmountValue * 0.7;
    const ethSplit = dollarAmountValue * 0.3;
    const btcAmount = (btcRate * btcSplit).toFixed(10);
    const ethAmount = (ethRate * ethSplit).toFixed(10);
    btcAllocation.value = btcAmount;
    ethAllocation.value = ethAmount;
  })
  // The event listener added to the dollar amount variable gets the value of the element where the event occurred (i.e. the dollar input field), calculates a 70/30 split, calculates the BTC and ETH equivalent of the respective dollar amounts, rounds those numbers to six decimal places, and displays them on the webpage. The event listener runs whenever the value of the dollar input field is changed. //
  function startTimer(duration, display) {
    let timer = duration, minutes, seconds;
    setInterval(function () {
      minutes = parseInt(timer / 60, 10);
      seconds = parseInt(timer % 60, 10);
      minutes = minutes < 10 ? "0" + minutes : minutes;
      seconds = seconds < 10 ? "0" + seconds : seconds;
      display.textContent = minutes + ":" + seconds;
      if (--timer < 0) {
        timer = duration;
      }
    }, 1000);
  }
  // The startTimer() function creates a countdown timer that is displayed in minutes and seconds. //
  resetButton.addEventListener("click", function () {
    dollarAmount.value = "";
    btcAllocation.value = "";
    ethAllocation.value = "";
    fetchCounter = fetchCounter + 1;
    if (fetchCounter > 4) {
      alert("You've hit the maximum number of conversions allowed. We have to limit the number of conversions to ensure everyone is able to use the calculator. Please refresh the page in three minutes to continue using the calculator.");
      dollarAmount.setAttribute("readonly", "");
      resetButton.setAttribute("disabled", "");
      dateText.innerText = "";
      let threeMinutes = 60 * 3;
      startTimer(threeMinutes, dateText);
    } else {
      getRates();
      getDate();
      dollarAmount.focus();
    }
  })
  // The event listener added to the reset button clears the values of the dollar input, the BTC allocation input, and the ETH allocation input. If the reset button has been clicked more than four times, it displays an alert with a message, sets the dollar input field to a read only input field, disables the reset button, clears the inner text of the div containing the date, and calls the startTimer() function. If the reset button has been clicked four times or fewer, the function calls the getRates() and getDate() functions and sets the focus of the webpage to the dollar input. The event listener runs whenever the reset button is clicked. //
});
</script>

<template>
  <h1>
    {{ header }}</h1>
  <div class="outer-container">
    <div class="inner-container dollars">
      <div class="dollar-input">
        <label for="dollar-amount">Investable Assets (USD)</label>
        <input type="number" min="0" id="dollar-amount" name="dollar-amount" autofocus="autofocus"
          onkeydown="return event.keyCode !== 69 && event.keyCode !== 187 && event.keyCode !== 189">
      </div>
      <div id="reset">
        <button id="reset-button">Reset</button>
      </div>
      <div id="date">Exchange rates are as of <span id="date-container"></span></div>
      <div id="error">There was an error getting the exchange rates. Please try again later.</div>
    </div>
    <div class="inner-container crypto">
      <div class="btc">
        <label for="btc-allocation">70% BTC Allocation</label>
        <input id="btc-allocation" name="btc-allocation" readonly>
        <div class="conversion-rate">1 USD = <span id="btc-rate-container"></span> BTC</div>
      </div>
      <div class="eth">
        <label for="eth-allocation">30% ETH Allocation</label>
        <input id="eth-allocation" name="eth-allocation" readonly>
        <div class="conversion-rate">1 USD = <span id="eth-rate-container"></span> ETH</div>
      </div>
    </div>
  </div>
</template>
