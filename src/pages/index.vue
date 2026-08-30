<script lang="ts" setup>
const explorerPath = '/xitcoin-testnet';
const faucetUrl = 'https://faucet-testnet.xitcoin.org/';
const apiUrl = 'https://api-testnet.xitcoin.org/';
const rpcUrl = 'https://rpc-testnet.xitcoin.org/';
</script>

<template>
  <section class="mx-auto max-w-5xl py-10 md:py-16">
    <div class="rounded-2xl border border-gray-200 bg-base-100 p-7 text-center shadow-sm dark:border-gray-700 md:p-12">
      <img
        src="/assets/xitcoin-logo.png"
        alt="Xitcoin"
        class="mx-auto mb-6 h-20 w-20 object-contain"
      />
      <p class="mb-3 text-sm font-semibold uppercase tracking-[0.2em] text-primary">Public Cosmos testnet</p>
      <h1 class="text-4xl font-bold md:text-6xl">Xitcoin Testnet</h1>
      <p class="mx-auto mt-5 max-w-2xl text-lg text-gray-500 dark:text-gray-300">
        Réseau public Xitcoin pour le développement, les tests et la validation.
        Les XTC de testnet n’ont aucune valeur monétaire.
      </p>

      <div class="mt-8 flex flex-col justify-center gap-3 sm:flex-row">
        <RouterLink :to="explorerPath" class="btn btn-primary">
          Ouvrir l’explorer
        </RouterLink>
        <a :href="faucetUrl" target="_blank" rel="noopener" class="btn btn-outline">
          Obtenir 10 XTC testnet
        </a>
      </div>
    </div>

    <div class="mt-6 grid gap-4 md:grid-cols-3">
      <a :href="apiUrl" target="_blank" rel="noopener" class="rounded-xl border border-gray-200 bg-base-100 p-5 hover:border-primary dark:border-gray-700">
        <div class="font-semibold">API REST</div>
        <div class="mt-2 break-all text-sm text-gray-500">{{ apiUrl }}</div>
      </a>
      <a :href="rpcUrl" target="_blank" rel="noopener" class="rounded-xl border border-gray-200 bg-base-100 p-5 hover:border-primary dark:border-gray-700">
        <div class="font-semibold">RPC CometBFT</div>
        <div class="mt-2 break-all text-sm text-gray-500">{{ rpcUrl }}</div>
      </a>
      <div class="rounded-xl border border-gray-200 bg-base-100 p-5 dark:border-gray-700">
        <div class="font-semibold">gRPC</div>
        <div class="mt-2 break-all text-sm text-gray-500">grpc-testnet.xitcoin.org:443</div>
      </div>
    </div>
  </section>
</template>
