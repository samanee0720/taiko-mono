<script lang="ts">
  import { ResponsiveController } from '@taiko/ui-lib';
  import { getContext, onMount } from 'svelte';

  import { classNames } from '$lib/util/classNames';
  import type { ITaikoonDetail } from '$stores/taikoonDetail';

  import { NftRenderer } from '../NftRenderer';
  import { filterFormWrapperClasses, taikoonsWrapperClasses, titleClasses, wrapperClasses } from './classes';
  import { default as TaikoonDetail } from './TaikoonDetail.svelte';

  export let tokenIds: number[] = [];
  let windowSize: 'sm' | 'md' | 'lg' = 'md';

  export let disableClick = false;

  export let title: string = 'The Collection';

  export let isLoading = false;
  const taikoonDetailState = getContext<ITaikoonDetail>('taikoonDetail');

  $: selectedTaikoonId = -1;

  onMount(() => {
    onRouteChange();
  });

  async function onRouteChange() {
    const hash = location.hash;
    const taikoonId = parseInt(hash.replace('#', ''));
    selectedTaikoonId = isNaN(taikoonId) ? -1 : taikoonId;

    taikoonDetailState.set({
      ...$taikoonDetailState,
      tokenId: taikoonId,
      isModalOpen: taikoonId > 0,
    });
  }
</script>

<svelte:window on:hashchange={onRouteChange} />

<div class={wrapperClasses}>
  {#if windowSize !== 'sm'}
    <TaikoonDetail {isLoading} taikoonId={selectedTaikoonId} />
  {/if}
  <div class="flex flex-col w-full h-full">
    <div class={filterFormWrapperClasses}>
      <div class={titleClasses}>{title}</div>
    </div>
    <div class={taikoonsWrapperClasses}>
      {#each tokenIds as tokenId}
        <a
          href={disableClick ? '#' : `#${tokenId}`}
          class={classNames('w-full', 'rounded-xl', 'lg:rounded-3xl', 'md:rounded-2xl', 'overflow-hidden')}>
          <NftRenderer size="full" {tokenId} />
        </a>
      {/each}
    </div>
  </div>
</div>

<ResponsiveController bind:windowSize />
