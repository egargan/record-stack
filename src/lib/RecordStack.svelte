<script context="module" lang="ts">
  enum TransitionSpeed {
    Slow,
    Fast,
  }

  class Record {
    container: HTMLElement;
    restTop: number;
    titleHeight: number;
    previewHeight: number;
    bodyHeight: number;
    totalHeight: number;

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

      this.totalHeight = container.clientHeight;

      this.container.scrollIntoView({ behavior: "smooth" });

      this.enableTransition();
      this.setTransitionSpeed(TransitionSpeed.Fast);
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

    moveTo(scrollPos: number) {
      this.container.style.top = `${scrollPos}px`;
    }

    toTop() {
      this.container.style.top = '0px';
    }

    enableTransition(reflow: boolean = true) {
      // TODO: is this the cleanest we can do?
      this.container.classList.add('transition-top');
      this.container.classList.remove('transition-none');
    }

    setTransitionSpeed(speed: TransitionSpeed) {
      switch (speed) {
        case TransitionSpeed.Slow:
          this.container.style.transitionDuration = '0.32s';
          break;
        case TransitionSpeed.Fast:
        default:
          this.container.style.transitionDuration = '0.15s';
          break;
      }
    }

    disableTransition() {
      this.container.classList.add('transition-none');
    }

    // Forces a reflow to flush any pending style changes
    reflow() {
      this.container.offsetHeight;
    }

    addTransitionEndCallback(callback) {
      this.container.addEventListener( 'transitionend', callback, { once: 'true' },);
    }
  }
</script>

<script lang="ts">
  import { onMount } from 'svelte';

  export let components = [];

  let containers: HTMLElement[] = [];
  let records: Record[] = [];

  let stackContainer: HTMLElement;
  let scrollContainer: HTMLElement;

  let selectedRecord: Record;
  let lastSelectedRecord: Record;

  let lastScrollTop: number = 0;

  let restContainerHeight: number = 0;

  onMount(() => {
    let cumulativeTitleHeight = 0;
    let maxPreviewHeight = 0;

    containers.forEach((container, index) => {
      const record = new Record(container, cumulativeTitleHeight);
      record.rest();

      records.push(record);

      cumulativeTitleHeight += record.titleHeight;

      if (record.previewHeight > maxPreviewHeight) {
        maxPreviewHeight = record.previewHeight;
      }
    });

    restContainerHeight = cumulativeTitleHeight + maxPreviewHeight;
    stackContainer.style.height = `${restContainerHeight}px`;
  });

  function onMouseEnter(index: number) {
    if (selectedRecord) {
      return;
    }

    const mouseOverRecord = records[index];
    mouseOverRecord.moveUp(5);

    for (let i = index + 1; i < records.length; i++) {
      // TODO: magic number
      records[i].moveDown(mouseOverRecord.previewHeight + 10);
    }
  }

  function onMouseLeave(index: number) {
    if (selectedRecord) {
      return;
    }

    const mouseOverRecord = records[index];
    mouseOverRecord.rest();

    for (let i = index + 1; i < records.length; i++) {
      records[i].rest();
    }
  }

  function onClick(index: number) {
    if (selectedRecord) {
      stackContainer.style.height = `${restContainerHeight}px`;

      selectedRecord.disableTransition();

      // These two statements must be paired between the disable/enableTransition
      // calls to achieve a smooth result.. for some reason
      selectedRecord.moveTo(lastScrollTop);
      scrollContainer.scrollTop = lastScrollTop;

      selectedRecord.reflow();
      selectedRecord.enableTransition();

      for (let i = 0; i < records.length; i++) {
        records[i].rest();
      }

      selectedRecord.reflow();

      selectedRecord.setTransitionSpeed(TransitionSpeed.Fast);
      selectedRecord = undefined;
    } else {
      const clickedRecord = records[index];

      // TEMP: Used for debugging
      if (lastSelectedRecord) {
        lastSelectedRecord.container.classList.remove('last-selected');
      }
      lastSelectedRecord = clickedRecord;
      lastSelectedRecord.container.classList.add('last-selected');

      lastScrollTop = scrollContainer.scrollTop;

      for (let i = index + 1; i < records.length; i++) {
        records[i].moveDown(clickedRecord.bodyHeight + clickedRecord.previewHeight);
      }

      selectedRecord = clickedRecord;

      clickedRecord.setTransitionSpeed(TransitionSpeed.Slow);
      clickedRecord.moveTo(scrollContainer.scrollTop);

      clickedRecord.addTransitionEndCallback(() => {
        stackContainer.style.height = `${selectedRecord.totalHeight}px`;

        clickedRecord.disableTransition();

        clickedRecord.toTop();
        scrollContainer.scrollTo(0, 0);

        selectedRecord.reflow();
        clickedRecord.enableTransition();
      });
    }
  }
</script>

<style>
  .scroll-container {
    position: relative;
    overflow-y: auto;
    height: 100%;
  }

  .record-container {
    position: absolute;
    width: 100%;
    cursor: pointer;
    box-shadow: 0 -10px 10px #00000014;
  }

  :global(.transition-top) {
    transition-property: top;
    transition-timing-function: ease;
  }

  :global(.transition-none) {
    transition-property: none;
  }

  .stack-container {
    position: absolute;
    overflow: hidden;
    width: 100%;
  }
</style>

<div
  class="scroll-container"
  bind:this={scrollContainer}>
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
</div>