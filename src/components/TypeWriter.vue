<template>
  <div>{{ writtenString }}</div>
</template>

<script setup lang="ts">
// import { v4 } from 'uuid';

import { onMounted, ref } from 'vue';

const props = withDefaults(
  defineProps<{
    text: string;
    /**
     * chars per second
     */
    speed?: number;
  }>(),
  {
    speed: 60,
  }
);

const emit = defineEmits<{
  (e: 'doneTyping'): void;
  (e: 'typedChar'): void;
}>();

const writtenString = ref('');
const index = ref(0);

onMounted(() => {
  const interval = setInterval(() => {
    writtenString.value = props.text.slice(0, index.value++);
    emit('typedChar');
    if (index.value > props.text.length) {
      clearInterval(interval);
      emit('doneTyping');
    }
  }, 1000 / props.speed);
});
</script>

<style scoped></style>
