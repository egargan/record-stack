<script context="module" lang="ts">
  const titles = [
    'Frontend Dissatisfaction (and backing off)',
    'To the brain, reading computer code is not the same as reading language',
    'How to Create a Shrinking Header on Scroll Without JavaScript',
    'Dynamically Switching From One HTML Element to Another in Vue',
    'A DRY Approach to Color Themes in CSS',
    'WordPress 5.7: Big ol’ jQuery Update',
    'Exploring the Complexities of Width and Height in CSS',
  ]
</script>

<script lang="ts">
  let chosenTitle = titles[Math.floor(Math.random() * titles.length)];
  let previewLines = Math.floor(Math.random() * 2 + 2);
  let bodyLines = Math.floor(Math.random() * 10 + 25);
</script>

<style>

  h1, p {
    margin: 0;
  }

  p {
    padding: 16px 8px;
  }

  .title {
    padding: 32px 14px;
    background-color: #fff;
  }

  .preview, .body {
    padding: 14px;
    background-color: #fff;
  }

  .line {
    height: 11px;
    margin-bottom: 12px;
  }

  .preview .line {
    background-color: #bbb;
  }

  .body .line {
    background-color: #ccc;
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
</style>

<heading>
  <h1
    class="title"
    data-title>
    {chosenTitle}
  </h1>
  <p
    class="preview"
    data-preview>
    {#each [...Array(previewLines).keys()] as line}
      <div class="line"/>
    {/each}
  </p>
</heading>
<div
  class="body"
  data-body>
  {#each [...Array(bodyLines).keys()] as line, i}
    {#if i % 5 == Math.floor(Math.random() * 5)}
      <div class="line paragraph-{Math.ceil(Math.random() * 3)}"/>
    {:else}
      <div class="line"/>
    {/if}
  {/each}
</div>
