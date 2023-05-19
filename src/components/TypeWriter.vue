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
const interval = ref<NodeJS.Timeout>();

onMounted(() => {
  interval.value = setInterval(() => {
    writtenString.value = props.text.slice(0, index.value++);
    emit('typedChar');
    if (index.value > props.text.length) {
      cancelTypewriter();
    }
  }, 1000 / props.speed);
});

function cancelTypewriter() {
  clearInterval(interval.value);
  emit('doneTyping');
}

defineExpose({
  cancelTypewriter,
});
</script>

<style scoped></style>
