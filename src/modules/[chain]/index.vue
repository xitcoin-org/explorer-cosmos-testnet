<script lang="ts" setup>
import { useBlockchain, useFormatter, useTxDialog, useWalletStore, useStakingStore, useParamStore, useGovStore } from '@/stores';
import { LoadingStatus } from '@/stores/useDashboard';
import { onMounted, ref } from 'vue';
import { useIndexModule } from './indexStore';
import { computed } from '@vue/reactivity';

import CardStatisticsVertical from '@/components/CardStatisticsVertical.vue';
import ProposalListItem from '@/components/ProposalListItem.vue';
import ArrayObjectElement from '@/components/dynamic/ArrayObjectElement.vue';
import Loading from '@/components/Loading.vue';

const props = defineProps(['chain']);

const blockchain = useBlockchain();
const store = useIndexModule();
const walletStore = useWalletStore();
const format = useFormatter();
const dialog = useTxDialog();
const stakingStore = useStakingStore();
const paramStore = useParamStore();
const govStore = useGovStore();
const isAppVersionLoading = computed(
  () => !Array.isArray(paramStore.appVersion?.items) || paramStore.appVersion.items.length === 0
);
const isNodeVersionLoading = computed(
  () => !Array.isArray(paramStore.nodeVersion?.items) || paramStore.nodeVersion.items.length === 0
);
const publicAppVersion = computed(() => {
  const labels: Record<string, string> = {
    app_name: 'Application',
    version: 'Version',
    git_commit: 'Git commit',
    cosmos_sdk_version: 'Cosmos SDK',
    go_version: 'Go',
  };

  return ((paramStore.appVersion?.items as any[]) || [])
    .filter((item) => labels[item.subtitle])
    .map((item) => ({
      subtitle: labels[item.subtitle],
      value: item.value,
    }));
});
const chainAssets = computed(() => blockchain.current?.assets || []);
const assetDecimals = (asset: any) => Math.max(...(asset.denom_units || []).map((unit: any) => Number(unit.exponent || 0)));
const isProposalsLoading = computed(() => govStore.loading['2'] !== LoadingStatus.Loaded);

onMounted(() => {
  store.loadDashboard();
  walletStore.loadMyAsset();
  paramStore.handleAbciInfo();
  // if(!(coinInfo.value && coinInfo.value.name)) {
  // }
});
const currName = ref('');
blockchain.$subscribe((m, s) => {
  if (s.chainName !== currName.value) {
    currName.value = s.chainName;
    store.loadDashboard();
    walletStore.loadMyAsset();
    paramStore.handleAbciInfo();
  }
});
// wallet box
const change = computed(() => {
  const token = walletStore.balanceOfStakingToken;
  return token ? format.priceChanges(token.denom) : 0;
});
const color = computed(() => {
  switch (true) {
    case change.value > 0:
      return 'text-green-600';
    case change.value === 0:
      return 'text-grey-500';
    case change.value < 0:
      return 'text-red-600';
  }
});

function updateState() {
  walletStore.loadMyAsset();
}

</script>

<template>
  <div>
    <div class="grid grid-cols-1 gap-4 md:!grid-cols-3 lg:!grid-cols-6 mt-4">
      <div v-for="(item, key) in store.stats" :key="key">
        <CardStatisticsVertical v-bind="item" />
      </div>
    </div>

    <div
      v-if="blockchain.supportModule('governance')"
      class="bg-base-100 rounded mt-4 shadow"
    >
      <div class="px-4 pt-4 pb-2 text-lg font-semibold text-main">
        {{ $t('index.active_proposals') }}
      </div>
      <Loading v-if="isProposalsLoading" :bordered="false" />
      <template v-else>
        <div class="px-4 pb-4">
          <ProposalListItem :proposals="store?.proposals" />
        </div>
        <div
          class="pb-8 text-center"
          v-if="store.proposals?.proposals?.length === 0"
        >
          {{ $t('index.no_active_proposals') }}
        </div>
      </template>
    </div>

    <div class="bg-base-100 rounded mt-4 shadow">
      <div class="flex justify-between px-4 pt-4 pb-2 text-lg font-semibold text-main">
        <span class="truncate">{{ walletStore.currentAddress || 'Not Connected' }}</span>
        <RouterLink
          v-if="walletStore.currentAddress"
          class="float-right text-sm cursor-pointert link link-primary no-underline font-medium"
          :to="`/${chain}/account/${walletStore.currentAddress}`"
          >{{ $t('index.more') }}</RouterLink
        >
      </div>
      <div
        class="grid grid-cols-1 md:!grid-cols-4 auto-cols-auto gap-4 px-4 pb-6"
      >
        <div class="bg-gray-100 dark:bg-[#373f59] rounded-sm px-4 py-3">
          <div class="text-sm mb-1">{{ $t('account.balance') }}</div>
          <div class="text-lg font-semibold text-main">
            {{ format.formatToken(walletStore.balanceOfStakingToken) }}
          </div>
          <div class="text-sm" :class="color">${{ format.tokenValue(walletStore.balanceOfStakingToken) }}</div>
        </div>
        <div class="bg-gray-100 dark:bg-[#373f59] rounded-sm px-4 py-3">
          <div class="text-sm mb-1">{{ $t('module.staking') }}</div>
          <div class="text-lg font-semibold text-main">
            {{ format.formatToken(walletStore.stakingAmount) }}
          </div>
          <div class="text-sm" :class="color">${{ format.tokenValue(walletStore.stakingAmount) }}</div>
        </div>
        <div class="bg-gray-100 dark:bg-[#373f59] rounded-sm px-4 py-3">
          <div class="text-sm mb-1">{{ $t('index.reward') }}</div>
          <div class="text-lg font-semibold text-main">
            {{ format.formatToken(walletStore.rewardAmount) }}
          </div>
          <div class="text-sm" :class="color">${{ format.tokenValue(walletStore.rewardAmount) }}</div>
        </div>
        <div class="bg-gray-100 dark:bg-[#373f59] rounded-sm px-4 py-3">
          <div class="text-sm mb-1">{{ $t('index.unbonding') }}</div>
          <div class="text-lg font-semibold text-main">
            {{ format.formatToken(walletStore.unbondingAmount) }}
          </div>
          <div class="text-sm" :class="color">${{ format.tokenValue(walletStore.unbondingAmount) }}</div>
        </div>
      </div>

      <div
        v-if="walletStore.delegations.length > 0"
        class="px-4 pb-4 overflow-auto"
      >
        <table class="table table-compact w-full table-zebra">
          <thead>
            <tr>
              <th>{{ $t('account.validator') }}</th>
              <th>{{ $t('account.delegations') }}</th>
              <th>{{ $t('account.rewards') }}</th>
              <th>{{ $t('staking.actions') }}</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(item, index) in walletStore.delegations" :key="index">
              <td>
                <RouterLink
                  class="link link-primary no-underline"
                  :to="`/${chain}/staking/${item?.delegation?.validator_address}`"
                >
                  {{ format.validatorFromBech32(item?.delegation?.validator_address) }}
                </RouterLink>
              </td>
              <td>{{ format.formatToken(item?.balance) }}</td>
              <td>
                {{
                  format.formatTokens(
                    walletStore?.rewards?.rewards?.find(
                      (el) => el?.validator_address === item?.delegation?.validator_address
                    )?.reward
                  )
                }}
              </td>
              <td>
                <div>
                  <label
                    for="delegate"
                    class="btn !btn-xs !btn-primary btn-ghost rounded-sm mr-2"
                    @click="
                      dialog.open('delegate', { validator_address: item.delegation.validator_address }, updateState)
                    "
                  >
                    {{ $t('account.btn_delegate') }}
                  </label>
                  <label
                    for="withdraw"
                    class="btn !btn-xs !btn-primary btn-ghost rounded-sm"
                    @click="
                      dialog.open('withdraw', { validator_address: item.delegation.validator_address }, updateState)
                    "
                  >
                    {{ $t('index.btn_withdraw_reward') }}
                  </label>
                </div>
              </td>
            </tr>
          </tbody>
        </table>
      </div>

      <div class="grid grid-cols-2 gap-4 px-4 pb-6 mt-4">        <label for="send" class="btn !bg-yes !border-yes text-white" @click="dialog.open('send', {}, updateState)">{{
          $t('account.btn_send')
        }}</label>
        <label
          for="delegate"
          class="btn !bg-info !border-info text-white"
          @click="dialog.open('delegate', {}, updateState)"
          >{{ $t('account.btn_delegate') }}</label
        >
        <RouterLink to="/wallet/receive" class="btn !bg-info !border-info text-white hidden">{{
          $t('index.receive')
        }}</RouterLink>
      </div>    </div>

    <div class="bg-base-100 rounded mt-4 shadow">
      <div class="px-4 pt-4 pb-2 text-lg font-semibold text-main">
        Native assets
      </div>
      <div class="grid grid-cols-1 md:!grid-cols-2 xl:!grid-cols-3 gap-4 px-4 pb-4">
        <RouterLink
          v-for="asset in chainAssets"
          :key="asset.base"
          :to="`/${chain}/supply`"
          class="flex items-center gap-4 rounded bg-gray-100 dark:bg-[#373f59] px-4 py-3 hover:ring-1 hover:ring-primary"
        >
          <img
            v-if="asset.logo_URIs?.svg || asset.logo_URIs?.png"
            :src="asset.logo_URIs?.svg || asset.logo_URIs?.png"
            :alt="asset.symbol"
            class="h-10 w-10 object-contain"
          />
          <div class="min-w-0">
            <div class="text-lg font-semibold text-main">{{ asset.symbol }}</div>
            <div class="text-sm text-base-content/70">Base denomination: {{ asset.base }}</div>
            <div class="text-sm text-base-content/70">Decimals: {{ assetDecimals(asset) }}</div>
          </div>
        </RouterLink>
      </div>
    </div>

    <div class="bg-base-100 rounded mt-4 shadow">
      <div class="px-4 pt-4 pb-2 text-lg font-semibold text-main">
        Network software
      </div>
      <Loading v-if="isAppVersionLoading" :bordered="false" />
      <ArrayObjectElement v-else :value="publicAppVersion" :thead="false" />
      <div class="h-4"></div>
    </div>
  </div>
</template>

<route>
  {
    meta: {
      i18n: 'dashboard',
      order: 1,
    }
  }
</route>
