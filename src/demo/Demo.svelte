<script lang="ts">
  import RecordStack from '../lib/RecordStack.svelte';
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
  main { height: 100%; }
</style>

<main>
  <RecordStack {components}/>
</main>

