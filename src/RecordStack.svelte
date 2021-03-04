<script context="module" lang="ts">
  class Record {
    container: HTMLElement;
    restTop: number;
    titleHeight: number;
    previewHeight: number;

    constructor(container: HTMLElement, restTop: number) {
      this.container = container;

      container.style.top = restTop;
      this.restTop = restTop;

      // TODO: fallbacks for title, preview, body height
      const title = container.querySelectorAll('[data-title]')[0];
      this.titleHeight = title.clientHeight;

      const preview = container.querySelectorAll('[data-preview]')[0];
      this.previewHeight = preview.clientHeight;

      const body = container.querySelectorAll('[data-body]')[0];
      this.bodyHeight = body.clientHeight;

      this.containerHeight = container.clientHeight;
    }

    rest(): void {
      this.container.style.top = `${this.restTop}px`;
    }

    moveUp(height: number) {
      this.container.style.top = `${this.restTop - height}px`;
    }

    moveDown(height: number) {
      this.container.style.top = `${this.restTop + height}px`;
    }

    toTop() {
      this.container.style.top = `${this.restTop + height}px`;
    }
  }
</script>

<script lang="ts">
  import { onMount } from 'svelte';

  export let components = [];

  let containers: HTMLElement = [];
  let records: Record[] = [];

  let stackContainer;

  onMount(() => {
    let cumulativeTitleHeight = 0;

    containers.forEach((container, index) => {
      const record = new Record(container, cumulativeTitleHeight);
      record.rest();

      records.push(record);
      cumulativeTitleHeight += record.titleHeight;
    });
  });

  function onMouseEnter(index: number) {
    const mouseOverRecord = records[index];
    mouseOverRecord.moveUp(5);

    for (let i = index + 1; i < records.length; i++) {
      // TODO: magic number
      records[i].moveDown(mouseOverRecord.previewHeight + 10);
    }
  }

  function onMouseLeave(index: number) {
    const mouseOverRecord = records[index];
    mouseOverRecord.rest();

    for (let i = index + 1; i < records.length; i++) {
      records[i].rest();
    }
  }

  function onClick(index: number) {
    const mouseOverRecord = records[index];

    for (let i = index + 1; i < records.length; i++) {
      // TODO: magic number
      records[i].moveDown(mouseOverRecord.bodyHeight + mouseOverRecord.previewHeight);
    }

    stackContainer.style.height = `
    stackContainer.style.height = `

  }
</script>

<style>
  .record-container {
    position: absolute;
    width: 100%;
    cursor: pointer;

    transition: top 0.15s ease;
    box-shadow: 0 -10px 10px #00000014;
  }

  .stack-container {
    width: 100%;
  }
</style>

<div
  class="stack-container"
  bind:this={stackContainer}>
  {#each components as component, index}
    <article
      class="record-container"
      bind:this={containers[index]}
      on:mouseenter={() => onMouseEnter(index)}
      on:mouseleave={() => onMouseLeave(index)}
      on:click={() => onClick(index)}>
      <svelte:component this={component}/>
    </article>
  {/each}
</div>
