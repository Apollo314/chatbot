<template>
  <div
    class="promp-container column justify-center items-center q-col-gutter-sm q-pb-lg"
    :class="{ dark: dark, light: !dark }"
  >
    <div v-if="isGenerating">
      <q-btn
        class="rounded"
        icon="stop"
        color="primary"
        no-caps
        label="Stop generating"
        @click="$emit('stopGeneration')"
      />
    </div>
    <div v-else-if="generated">
      <q-btn
        class="rounded"
        icon="refresh"
        color="primary"
        no-caps
        label="Regenerate response"
        @click="$emit('prompt', lastPrompt)"
      />
    </div>
    <div class="row full-width justify-center">
      <div
        class="prompt-parent compact-scrollbar col col-xs-12 col-sm-10 q-px-sm"
      >
        <q-input
          class="shadowed-input"
          v-model="prompt"
          outlined
          autogrow
          type="text"
          placeholder="Send a message"
          v-on:keyup.enter="request"
        >
          <div
            class="absolute column justify-center q-pr-sm"
            style="right: 0; bottom: 0; height: 52px"
          >
            <q-btn
              :disabled="!prompt"
              dense
              class="rounded"
              flat
              @click="request"
            >
              <q-icon class="rotate-315" name="send" />
            </q-btn>
          </div>
        </q-input>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue';

const props = defineProps<{
  isGenerating: boolean;
  generated: boolean;
  dark?: boolean;
  lastPrompt?: string;
}>();

const emit = defineEmits<{
  (e: 'prompt', value: string): void;
  (e: 'stopGeneration'): void;
}>();

const prompt = ref<string>('');
const lastPrompt = ref<string>(props.lastPrompt || '');

const request = () => {
  emit('prompt', prompt.value);
  lastPrompt.value = prompt.value;
  prompt.value = '';
};
</script>

<style lang="scss">
$light-gradient: linear-gradient(180deg, #ffffff00 10%, $light-page-bg-1 50%);
$dark-gradient: linear-gradient(180deg, #ffffff00 10%, $dark-page-bg-1 50%);

.promp-container {
  &.light {
    background-image: $light-gradient;
  }
  &.dark {
    background-image: $dark-gradient;
  }
}
.prompt-parent {
  max-width: 48rem !important;
}

.q-btn {
  &.rounded {
    border-radius: $generic-border-radius;
  }
}

.shadowed-input {
  .q-field__control {
    box-shadow: 0 0 10px #00000030;
    padding-right: 0;
    contain: paint;

    .light & {
      background: $light-page-bg-1;
    }
    .dark & {
      background: $dark-page-bg-1;
    }

    textarea {
      max-height: 200px;
      padding-right: 46px;
      margin-right: -46px;
    }
  }
}
</style>
