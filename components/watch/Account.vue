<script setup lang="ts">
import type { mastodon } from 'masto'

const { account, watch } = defineProps<{
  account: mastodon.v1.Account
  hoverCard?: boolean
  watch: string
}>()

cacheAccount(account)

const client = useMastoClient()

const isRemoved = ref(false)

async function edit() {
  try {
    isRemoved.value
      ? await client.v1.watches.addAccount(watch, { accountIds: [account.id] })
      : await client.v1.watches.removeAccount(watch, { accountIds: [account.id] })
    isRemoved.value = !isRemoved.value
  }
  catch (err) {
    console.error(err)
  }
}
</script>

<template>
  <div flex justify-between hover:bg-active transition-100 items-center>
    <AccountInfo
      :account="account" hover p1 as="router-link"
      :hover-card="hoverCard"
      shrink
      overflow-hidden
      :to="getAccountRoute(account)"
    />
    <div>
      <CommonTooltip
        :content="isRemoved ? $t('watch.add_account') : $t('watch.remove_account')"
        :hover="isRemoved ? 'text-green' : 'text-red'"
        no-auto-focus
      >
        <button
          text-sm p2 border-1 transition-colors
          border-dark
          btn-action-icon
          @click="edit"
        >
          <span :class="isRemoved ? 'i-ri:user-add-line' : 'i-ri:user-unfollow-line'" />
        </button>
      </CommonTooltip>
    </div>
  </div>
</template>
