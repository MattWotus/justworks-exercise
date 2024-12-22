<script setup>
import {
  ref
} from 'vue'

defineProps({
  header: String,
})

function convert(event) {
  console.log("success")
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
          <button @click="convert">Convert</button>
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
