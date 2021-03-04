<script lang="ts">
  import RecordStack from './RecordStack.svelte';
  import DemoRecord from './DemoRecord.svelte';

  const components = [
    DemoRecord,
    DemoRecord,
    DemoRecord,
    DemoRecord,
    DemoRecord,
    DemoRecord,
    DemoRecord,
    DemoRecord,
  ];
</script>

<style>
  .wrapper {
    padding: 0px;
    margin: 0px;
  }
</style>

<main>
  <div class="wrapper">
    <RecordStack {components}/>
  </div>
</main>

