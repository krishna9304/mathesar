<script lang="ts">
  import { ButtonMenuItem } from '@mathesar/component-library';
  import {
    getSortingLabelForColumn,
    type SortDirection,
  } from '@mathesar/components/sort-entry/utils';
  import {
    iconGrouping,
    iconSortAscending,
    iconSortDescending,
  } from '@mathesar/icons';
  import {
    getTabularDataStoreFromContext,
    type ProcessedColumn,
  } from '@mathesar/stores/table-data';

  export let processedColumn: ProcessedColumn;

  const tabularData = getTabularDataStoreFromContext();
  $: ({
    meta: { sorting, grouping },
  } = $tabularData);

  $: columnId = processedColumn.id;

  $: currentSorting = $sorting.get(processedColumn.id);
  $: sortingLabel = getSortingLabelForColumn(
    processedColumn.abstractType.cellInfo.type,
    !!processedColumn.linkFk,
  );

  $: hasGrouping = $grouping.hasColumn(columnId);

  function removeSorting() {
    sorting.update((s) => s.without(columnId));
  }

  function applySorting(sortDirection: SortDirection) {
    sorting.update((s) => s.with(columnId, sortDirection));
  }

  function addGrouping() {
    grouping.update((g) =>
      g.withEntry({
        columnId,
        preprocFnId: undefined,
      }),
    );
  }

  function removeGrouping() {
    grouping.update((g) => g.withoutColumns([columnId]));
  }
</script>

{#if currentSorting === 'ASCENDING'}
  <ButtonMenuItem icon={iconSortAscending} on:click={removeSorting}>
    Remove {sortingLabel.ASCENDING} Sorting
  </ButtonMenuItem>
{:else}
  <ButtonMenuItem
    icon={iconSortAscending}
    on:click={() => applySorting('ASCENDING')}
  >
    Sort {sortingLabel.ASCENDING}
  </ButtonMenuItem>
{/if}

{#if currentSorting === 'DESCENDING'}
  <ButtonMenuItem icon={iconSortDescending} on:click={removeSorting}>
    Remove {sortingLabel.DESCENDING} Sorting
  </ButtonMenuItem>
{:else}
  <ButtonMenuItem
    icon={iconSortDescending}
    on:click={() => applySorting('DESCENDING')}
  >
    Sort {sortingLabel.DESCENDING}
  </ButtonMenuItem>
{/if}

{#if hasGrouping}
  <ButtonMenuItem icon={iconGrouping} on:click={removeGrouping}>
    Remove Grouping
  </ButtonMenuItem>
{:else}
  <ButtonMenuItem icon={iconGrouping} on:click={addGrouping}>
    Group by Column
  </ButtonMenuItem>
{/if}
