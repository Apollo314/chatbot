<template>
  <div
    ref="chatPage"
    class="chat-page q-py-lg"
    :class="{ dark: dark, light: !dark }"
  >
    <div v-for="(conversation, i) in conversations" :key="i">
      <div class="prompt row full-width justify-center">
        <div class="chat-parent col col-xs-12 col-sm-10 q-px-sm">
          <q-item class="q-py-lg">
            <q-item-section avatar>
              <q-icon color="primary" name="person" />
            </q-item-section>
            <q-item-section>{{ conversation.prompt }}</q-item-section>
          </q-item>
        </div>
      </div>
      <div class="response row full-width justify-center">
        <div class="chat-parent col col-xs-12 col-sm-10 q-px-sm">
          <q-item class="q-py-lg">
            <q-item-section avatar>
              <q-icon color="green" name="android" />
            </q-item-section>
            <q-item-section>
              <TypeWriter
                :text="conversation.response"
                @typed-char="scrollBottom"
                @done-typing="isGenerating = false"
                v-if="conversation.animate"
              ></TypeWriter>
              <div v-else>
                {{ conversation.response }}
              </div>
            </q-item-section>
          </q-item>
        </div>
      </div>
    </div>
    <q-page-sticky
      position="bottom-right"
      style="z-index: 4000"
      :offset="[18, 18]"
    >
      <q-btn
        round
        dense
        icon="arrow_downward"
        color="primary"
        style="z-index: 4001"
        v-if="!stickToBottom"
        @click="
          stickToBottom = true;
          scrollBottom();
        "
      />
    </q-page-sticky>
  </div>
</template>

<script setup lang="ts">
import { ref, watch, onMounted } from 'vue';
import TypeWriter from './TypeWriter.vue';
import { useScroll } from '@vueuse/core';

export type Conversation = {
  prompt: string;
  response: string;
  animate?: boolean;
};

const props = defineProps<{
  /**
   * ex: history that doesn't need to be typewritten.
   */
  initial_conversations: Conversation[];
  dark?: boolean;
}>();

defineEmits<{
  (e: 'update:conversations', value: Conversation[]): void;
}>();

const isGenerating = ref(false);
const conversations = ref([...props.initial_conversations]);
const chatPage = ref<HTMLElement>();
const stickToBottom = ref(true);

function scrollBottom() {
  if (stickToBottom.value) {
    chatPage.value?.scrollTo(0, chatPage.value.scrollHeight);
  }
}

const { directions, arrivedState } = useScroll(chatPage);

watch(
  () => directions.top,
  () => {
    if (directions.top) {
      stickToBottom.value = false;
    }
  }
);

watch(
  () => arrivedState.bottom,
  () => {
    if (arrivedState.bottom) {
      stickToBottom.value = true;
    }
  }
);

onMounted(() => {
  scrollBottom();
});

const addConversation = (conversation: Conversation, animate = true) => {
  stickToBottom.value = true;
  conversation.animate = animate;
  conversations.value.push(conversation);
  isGenerating.value = true;
};

defineExpose({
  addConversation,
  isGenerating,
  conversations,
});
</script>

<style lang="scss">
.chat-page {
  border-bottom: 1px solid #777777;
  overflow-y: auto;
  &.dark {
    background: $dark-page-bg-1;
    .q-item__section {
      color: #d1d5db !important;
    }
    .prompt {
      background: $dark-page-bg-1;
    }
    .response {
      background: $dark-page-bg-2;
    }
    .response,
    .prompt {
      border-bottom: 1px solid #222222;
    }
  }
  &.light {
    background: $light-page-bg-1;
    .q-item__section {
      color: #222222 !important;
    }
    .prompt {
      background: $light-page-bg-1;
    }
    .response {
      background: $light-page-bg-2;
    }
    .response,
    .prompt {
      border-bottom: 1px solid #aaaaaa;
    }
  }
}
.chat-parent {
  max-width: 42rem !important;
  overflow-y: auto;
}
</style>
