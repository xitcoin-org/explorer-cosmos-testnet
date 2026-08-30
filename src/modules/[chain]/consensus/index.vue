<script lang="ts" setup>
import fetch from 'cross-fetch';
import { onMounted, ref, computed, onUnmounted } from 'vue';
import { useBlockchain, useFormatter, useStakingStore, useBaseStore } from '@/stores';
import { consensusPubkeyToHexAddress } from '@/libs';

const format = useFormatter();
const chainStore = useBlockchain();
const stakingStore = useStakingStore();
const baseStore = useBaseStore();
const rpcList = ref(chainStore.current?.endpoints?.rpc || [{ address: '', provider: '' }]);
let rpc = ref('');
const validators = ref(stakingStore.validators);

let httpstatus = ref(200);
let httpStatusText = ref('');
let roundState = ref({} as any);
let rate = ref('');
let height = ref('');
let round = ref('');
let step = ref('');
let timer = null as any;
let loading = false;
let updatetime = ref(new Date());
let positions = ref([]);
let validatorsData = ref([] as any);
onMounted(async () => {
  // stakingStore.init();
  validatorsData.value = await stakingStore.fetchAcitveValdiators();
  rpc.value = rpcList.value[0].address + '/consensus_state';
  await fetchPosition();
  update();
  clearTime();
  timer = setInterval(() => {
    update();
  }, Math.round(baseStore.blocktime / 2));
});
onUnmounted(() => {
  clearTime();
  loading = false;
});

function clearTime() {
  clearInterval(timer);
  timer = null;
}
const newTime = computed(() => {
  return format.toDay(updatetime.value, 'time');
});

const vals = computed(() => {
  return validatorsData.value.map((x: any) => {
    const x2 = x;
    // @ts-ignore
    x2.hex = consensusPubkeyToHexAddress(x.consensus_pubkey);
    return x2;
  });
});

function showName(i: number, text: string) {
  if (text === 'nil-Vote') {
    // @ts-ignore
    if (positions.value?.[i]?.address) {
      const val = vals.value.find(
        // @ts-ignore
        (x) => x.hex === positions.value?.[i]?.address
      );
      return val?.description?.moniker || i;
    }
    return i;
  }
  // const txt = text.substring(text.indexOf(':') + 1, text.indexOf(' '));
  // const sig = text.split(' ');
  // // @ts-ignore
  // const val = validators.value.find((x) => x?.hex?.startsWith(txt));
  // return `${val?.description?.moniker || txt} - ${sig[2]}`;
}
function color(i: number, txt: string) {
  if (i === roundState.value?.proposer?.index) {
    return txt === 'nil-Vote' ? 'warning' : 'primary';
  }
  return txt === 'nil-Vote' ? 'gray-700' : 'success';
}
async function onChange() {
  if (loading) return;
  loading = true;
  httpstatus.value = 200;
  httpStatusText.value = '';
  roundState.value = {};
  clearTime();
  try {
    await fetchPosition();
    update();
    timer = setInterval(() => {
      update();
    }, Math.round(baseStore.blocktime / 2));
  } finally {
    loading = false;
  }
}

async function fetchPosition() {
  let dumpurl = rpc.value.replace('consensus_state', 'dump_consensus_state');
  try {
    const response = await fetch(dumpurl);
    if (!response.ok) {
      throw new Error(`HTTP error: ${response.status}`);
    }

    httpstatus.value = response.status;
    httpStatusText.value = response.statusText;

    const data = await response.json();
    positions.value = data.result.round_state.validators.validators;
  } catch (error) {
    // @ts-ignore
    httpstatus.value = error?.status || 500;
    // @ts-ignore
    httpStatusText.value = error?.message || 'Error';
  }
}

async function update() {
  rate.value = '0%';
  updatetime.value = new Date();
  if (httpstatus.value === 200) {
    fetch(rpc.value)
      .then((data) => {
        httpstatus.value = data.status;
        httpStatusText.value = data.statusText;
        return data.json();
      })
      .then((res) => {
        roundState.value = res.result.round_state;
        const raw = roundState?.value?.['height/round/step']?.split('/');
        // eslint-disable-next-line prefer-destructuring
        height.value = raw[0];
        // eslint-disable-next-line prefer-destructuring
        round.value = raw[1];
        // eslint-disable-next-line prefer-destructuring
        step.value = raw[2];

        // find the highest onboard rate
        roundState.value?.height_vote_set?.forEach((element: any) => {
          const rates = Number(element.prevotes_bit_array.substring(element.prevotes_bit_array.length - 4));
          if (rates > 0) {
            rate.value = `${(rates * 100).toFixed()}%`;
          }
        });
      })
      .catch((err) => {
        httpstatus.value = 500;
        httpStatusText.value = err;
      });
  }
}
</script>

<template>
  <div class="space-y-4">
    <section class="bg-base-100 border border-base-300 rounded-xl shadow-sm p-5">
      <div class="flex flex-col gap-4 md:flex-row md:items-center md:justify-between">
        <div>
          <h1 class="text-2xl font-semibold text-main">Live consensus</h1>
          <p class="mt-1 text-sm text-base-content/60">
            Current CometBFT consensus state for Xitcoin Testnet.
          </p>
        </div>
        <button class="btn !bg-blue-600 !border-blue-600 hover:!bg-blue-500 text-white" @click="onChange">
          Refresh
        </button>
      </div>
      <div class="mt-4 rounded-lg bg-base-200 px-4 py-3 text-sm break-all">
        {{ rpc }}
      </div>
      <div v-if="httpstatus !== 200" class="alert alert-error mt-4">
        Consensus endpoint unavailable: {{ httpstatusText }}
      </div>
    </section>

    <section v-if="roundState['height/round/step']" class="grid grid-cols-1 gap-4 sm:grid-cols-3">
      <div class="bg-base-100 border border-base-300 rounded-xl p-5 shadow-sm">
        <div class="text-sm text-base-content/60">Height</div>
        <div class="mt-2 text-3xl font-semibold text-main">{{ height }}</div>
      </div>
      <div class="bg-base-100 border border-base-300 rounded-xl p-5 shadow-sm">
        <div class="text-sm text-base-content/60">Round</div>
        <div class="mt-2 text-3xl font-semibold text-main">{{ round }}</div>
      </div>
      <div class="bg-base-100 border border-base-300 rounded-xl p-5 shadow-sm">
        <div class="text-sm text-base-content/60">Step</div>
        <div class="mt-2 text-3xl font-semibold text-main">{{ step }}</div>
      </div>
    </section>

    <section class="bg-base-100 border border-base-300 rounded-xl shadow-sm p-5">
      <div class="flex items-center justify-between gap-4">
        <div>
          <h2 class="text-lg font-semibold text-main">Official validators</h2>
          <p class="mt-1 text-sm text-base-content/60">Active validator set reported by the network.</p>
        </div>
        <div class="text-sm text-base-content/50">Updated {{ newTime || '—' }}</div>
      </div>
      <div class="mt-5 grid grid-cols-1 gap-3 sm:grid-cols-2 lg:grid-cols-4">
        <div
          v-for="validator in vals"
          :key="validator.operator_address"
          class="flex items-center gap-3 rounded-lg border border-base-300 bg-base-200 px-4 py-3"
        >
          <span class="h-2.5 w-2.5 rounded-full bg-emerald-500"></span>
          <span class="font-medium text-main truncate">{{ validator.description?.moniker }}</span>
        </div>
      </div>
    </section>
  </div>
</template>

<route>
  {
    meta: {
      i18n: 'consensus',
    }
  }
</route>
