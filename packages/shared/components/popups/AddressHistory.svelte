<script lang="typescript">
    import { Text } from 'shared/components'
    import type { Address } from 'shared/lib/typings/address'
    import { formatUnit } from 'shared/lib/units'
    import type { WalletAccount } from 'shared/lib/wallet'
    import type { Readable } from 'svelte/store'
    import { setClipboard } from 'shared/lib/utils'

    export let locale
    export let account: Readable<WalletAccount>

    let addresses: Address[]
    $: addresses = $account?.addresses.slice().sort((a, b) => {
        if (a.keyIndex === b.keyIndex) {
            return a.internal ? 1 : -1
        }

        return b.keyIndex - a.keyIndex ;
    }) ?? []
</script>

<style>
    .history {
        max-height: 30vh;
    }
</style>

<div class="mb-5">
    <Text type="h4">{locale('popups.addressHistory.title', { values: { name: $account.alias } })}</Text>
</div>
<div class="history scrollable-y flex flex-row flex-wrap space-y-7">
    {#each addresses as _addr}
        <div class="flex flex-row flex-wrap space-y-1">
            <button class="text-left" on:click={() => setClipboard(_addr.address.toLowerCase())}>
                <Text type="pre">{_addr.address}</Text>
            </button>
            <Text type="p">
                {locale('popups.addressHistory.currentBalance', { values: { balance: formatUnit(_addr.balance) } })}
            </Text>
        </div>
    {/each}
</div>
