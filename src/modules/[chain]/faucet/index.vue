<script lang="ts" setup>
const faucetUrl = 'https://faucet-testnet.xitcoin.org/';
</script>

<template>
  <div class="max-w-2xl mx-auto mt-14">
    <div class="bg-base-100 px-6 py-8 rounded shadow text-center">
      <h1 class="text-main text-3xl font-semibold tracking-tight">Xitcoin Testnet Faucet</h1>
      <p class="mt-4 text-base-content/70">
        Receive 10 XTC testnet for development and testing.
        Testnet tokens have no monetary value.
      </p>
      <a :href="faucetUrl" class="btn !bg-blue-600 !border-blue-600 hover:!bg-blue-500 text-white mt-6" target="_blank" rel="noopener">
        Open Xitcoin Faucet
      </a>
    </div>
  </div>
</template>

<route>
{
  meta: {
    i18n: 'faucet',
    order: 18
  }
}
</route>
