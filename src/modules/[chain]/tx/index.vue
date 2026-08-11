<script lang="ts" setup>
import { ref, onMounted } from 'vue';
import { useBlockchain, useFormatter } from '@/stores';
import { useRouter } from 'vue-router';

const props = defineProps(['chain']);
const router = useRouter();
const blockchain = useBlockchain();
const format = useFormatter();

const tab = ref('recent');
const hash = ref('');
const loading = ref(true);
const error = ref('');
const txs = ref<any>({ tx_responses: [] });

const hashReg = /^[A-Z\d]{64}$/;

async function loadTransactions() {
  loading.value = true;
  error.value = '';
  try {
    const response = await blockchain.rpc?.getTxs('?query=tx.height>0', {}, undefined);
    txs.value = response || { tx_responses: [] };
  } catch {
    txs.value = { tx_responses: [] };
    error.value = 'Unable to load indexed transactions.';
  } finally {
    loading.value = false;
  }
}

function transactionHash(item: any) {
  return item?.txhash || item?.hash || '';
}

function transactionMessages(item: any) {
  return item?.tx?.body?.messages || [];
}

function transactionFees(item: any) {
  return item?.tx?.authInfo?.fee?.amount || item?.tx?.auth_info?.fee?.amount || [];
}

function search() {
  const value = hash.value.trim().toUpperCase();
  if (hashReg.test(value)) {
    router.push({ path: `/${props.chain}/tx/${value}` });
  }
}

onMounted(() => {
  tab.value = String(router.currentRoute.value.query.tab || 'recent');
  loadTransactions();
});
</script>

<template>
  <div>
    <div role="tablist" class="tabs tabs-bordered mb-4">
      <a class="tab text-gray-400 uppercase" :class="{ 'tab-active': tab === 'recent' }" @click="tab = 'recent'">
        Transactions
      </a>
      <a class="tab text-gray-400 uppercase" :class="{ 'tab-active': tab === 'search' }" @click="tab = 'search'">
        Search
      </a>
    </div>

    <div v-show="tab === 'recent'" class="bg-base-100 rounded overflow-x-auto">
      <table class="table w-full table-compact">
        <thead class="bg-base-200">
          <tr>
            <th>Height</th>
            <th>Hash</th>
            <th>Messages</th>
            <th>Fees</th>
          </tr>
        </thead>
        <tbody>
          <tr v-if="loading">
            <td colspan="4" class="text-center py-8">Loading indexed transactions…</td>
          </tr>
          <tr v-else-if="error">
            <td colspan="4" class="text-center py-8 text-error">{{ error }}</td>
          </tr>
          <tr v-else-if="!(txs.tx_responses || []).length">
            <td colspan="4" class="text-center py-8">No indexed transactions yet.</td>
          </tr>
          <tr v-for="item in txs.tx_responses || []" :key="transactionHash(item)" class="hover">
            <td class="text-sm text-primary">
              <RouterLink :to="`/${props.chain}/block/${item.height}`">{{ item.height }}</RouterLink>
            </td>
            <td class="truncate text-primary" width="50%">
              <RouterLink :to="`/${props.chain}/tx/${transactionHash(item)}`">
                {{ transactionHash(item) }}
              </RouterLink>
            </td>
            <td>{{ format.messages(transactionMessages(item)) }}</td>
            <td>{{ format.formatTokens(transactionFees(item)) }}</td>
          </tr>
        </tbody>
      </table>
    </div>

    <div v-show="tab === 'search'" class="bg-base-100 rounded">
      <div class="p-4">
        <div class="form-control">
          <input
            v-model="hash"
            type="text"
            class="input input-bordered"
            placeholder="Search by transaction hash"
            @keyup.enter="search"
          />
        </div>
      </div>
    </div>
  </div>
</template>

<route>
{
  meta: {
    i18n: 'tx',
    order: 5
  }
}
</route>
