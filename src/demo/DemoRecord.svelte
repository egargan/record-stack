<script context="module" lang="ts">
  const titles = [
    "Frontend Dissatisfaction (and backing off)",
    "To the brain, reading computer code is not the same as reading language",
    "How to Create a Shrinking Header on Scroll Without JavaScript",
    "Dynamically Switching From One HTML Element to Another in Vue",
    "A DRY Approach to Color Themes in CSS",
    "WordPress 5.7: Big ol’ jQuery Update",
    "Exploring the Complexities of Width and Height in CSS",
  ];

  export function getRandomColor(): string {
    switch (Math.floor(Math.random() * 4)) {
      case 0:
        return "forest";
      case 1:
        return "sage";
      case 2:
        return "grey";
      case 3:
        return "navy";
    }
  }
</script>

<script lang="ts">
  let chosenTitle = titles[Math.floor(Math.random() * titles.length)];
  let previewLines = Math.floor(Math.random() * 2 + 2);
  let bodyLines = Math.floor(Math.random() * 10 + 25);


</script>

<div class="{getRandomColor()}">
  <heading>
    <h1 class="title" data-title>
      {chosenTitle}
    </h1>
    <p class="preview" data-preview>
      {#each [...Array(previewLines).keys()] as line}
        <div class="line" />
      {/each}
    </p>
  </heading>
  <div class="body" data-body>
    {#each [...Array(bodyLines).keys()] as line, i}
      {#if i % 5 == Math.floor(Math.random() * 5)}
        <div class="line paragraph-{Math.ceil(Math.random() * 3)}" />
      {:else}
        <div class="line" />
      {/if}
    {/each}
  </div>
</div>

<style>
  h1,
  p {
    margin: 0;
  }

  p {
    padding: 16px 8px;
  }

  .title {
    padding: 32px 14px;
  }

  .preview,
  .body {
    padding: 14px;
  }

  .line {
    height: 11px;
    margin-bottom: 12px;
  }

  .paragraph-1 {
    width: 25%;
    margin-bottom: 28px;
  }

  .paragraph-2 {
    width: 50%;
    margin-bottom: 28px;
  }

  .paragraph-3 {
    width: 75%;
    margin-bottom: 28px;
  }

  .line:last-child {
    width: 50%;
  }

  .forest {
    background-color: #2e8a70;
  }
  .forest h1 {
    color: #d5f5ec;
  }
  .forest .line {
    background-color: #a0cabe;
  }

  .sage {
    background: #70b087;
  }
  .sage h1 {
    color: #e4f6eb;
  }
  .sage .line {
    background: #bbdec8;
  }

  .grey {
    background: #d2d7df;
  }
  .grey h1 {
    color: #384963;
  }
  .grey .line {
    background: #7e8ca0;
  }

  .navy {
    background: #3c4f76;
  }
  .navy h1 {
    color: #e4e8f1;
  }
  .navy .line {
    background: #acb7cf;
  }
</style>
