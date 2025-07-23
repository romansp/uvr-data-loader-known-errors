<script lang="ts">
class KnownError extends Error {
  constructor(message: string) {
    super(message);
    this.name = "KnownError";
  }
}

export const useHomeViewDataLoader = defineBasicLoader(async to => {
  if (to.query.knownError) {
    throw new KnownError("This is a known error for demonstration purposes.");
  }

  if (to.query.unknownError) {
    throw new Error("This is an unknown error for demonstration purposes.");
  }

  // Simulate a data loading process
  await new Promise(resolve => setTimeout(resolve, 100));

  // Return some mock data
  return {
    message: "This is the home page data",
    timestamp: new Date().toISOString(),
  };
}, {
  errors: [
    KnownError
  ],
});
</script>

<script setup lang="ts">
import { defineBasicLoader } from 'unplugin-vue-router/data-loaders/basic';

const { data, error } = useHomeViewDataLoader();
</script>

<template>
  <div>
    <h1>Data Loader console warning</h1>
    <p>GitHub issue: <a href="https://github.com/posva/unplugin-vue-router/issues/675">#675</a></p>
    <b>Steps</b>
    <ol>
      <li>Open the browser console</li>
      <li>Click the links below to trigger navigation and load data</li>
      <li>Observe the console for warnings and errors</li>
    </ol>

    <b>Expected</b>
    <ul>
      <li>
        Clicking "Load data with known error" should display `KnownError` on the page
        <pre>KnownError: This is a known error for demonstration purposes.</pre>
      </li>
    </ul>

    <b>Actual</b>
    <ul>
      <li>Clicking "Load data with known error" logs a warning in the console</li>
      <pre>warn: Loader "undefined"'s "commit()" was called but there is no staged data.</pre>
    </ul>

    <div class="links">
      <router-link :to="{ name: 'home', force: true }">Load data</router-link>
      <router-link :to="{ name: 'home', query: { knownError: 'true' }, force: true }">Load data with known error</router-link>
      <router-link :to="{ name: 'home', query: { unknownError: 'true' }, force: true }">Load data with unknown error</router-link>
    </div>
    <div>
      Data: {{ data }}
    </div>
    <div>
      {{ error }}
    </div>
  </div>
</template>

<style scoped>
.links {
  margin-bottom: 20px;
  display: flex;
  gap: 10px;
}
</style>