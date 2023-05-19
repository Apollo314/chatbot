<template>
  <q-layout view="lHh Lpr lFf">
    <q-toggle
      style="position: absolute; right: 0; z-index: 100"
      @click="$q.dark.toggle"
      :model-value="$q.dark.isActive"
      checked-icon="dark_mode"
      unchecked-icon="light_mode"
      color="primary"
      class="q-pa-md"
    />

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script setup lang="ts">
import { useQuasar } from 'quasar';

const $q = useQuasar();
$q.dark.set('auto');
</script>
