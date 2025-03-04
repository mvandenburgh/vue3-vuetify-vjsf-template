<script setup lang="ts">
import { ref, onMounted } from 'vue';
import Vjsf from '@koumoul/vjsf'

const model = ref<Record<string, any> | undefined>();
const schema = ref<Record<string, any> | undefined>();
const options = {};

async function getSchema() {
  const res = await fetch('https://raw.githubusercontent.com/dandi/schema/master/releases/0.6.9/dandiset.json');
  const data = await res.json();

  // Schema transformations needed for VJSF to render it.
  delete data['$schema'];
  delete data['title'];
  delete data['description'];
  data['properties']['identifier']['pattern'] = '^DANDI:\\d{6}$';
  data['$defs']['Software']['properties']['identifier']['pattern'] = '^RRID:.*';

  schema.value = data;
}

async function getData() {
  const res = await fetch('https://api.dandiarchive.org/api/dandisets/000026/versions/draft/');
  const data = await res.json();
  model.value = data;  
}

onMounted(() => {
  getSchema();
  getData();
});

</script>

<template>
  <v-app v-if="schema && model">
    <v-form>
      <vjsf v-model="model" :schema="schema" :options="options" />
    </v-form>
  </v-app>
</template>
