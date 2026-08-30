<template>
  <footer class="flex items-center h-12 mt-5 text-sm bg-gray-100 dark:bg-[#171d30] py-2 z-10 w-full">
    <div class="flex flex-1 items-center">
      &copy;&nbsp;{{ new Date().getFullYear() }}&nbsp;Xitcoin Testnet
    </div>
    <div class="hidden md:!flex md:items-center md:gap-4">
      <a
        class="link link-primary no-underline"
        href="https://github.com/xitcoin-org/pos-chain"
        target="_blank"
        rel="noopener noreferrer"
      >PoS chain</a>
      <a
        class="link link-primary no-underline"
        href="https://github.com/xitcoin-org/explorer-cosmos-testnet"
        target="_blank"
        rel="noopener noreferrer"
      >Explorer source</a>
      <a
        class="link link-primary no-underline"
        href="https://github.com/xitcoin-org/explorer-cosmos-testnet/blob/main/LICENSE"
        target="_blank"
        rel="noopener noreferrer"
      >License</a>
    </div>
  </footer>
</template>
