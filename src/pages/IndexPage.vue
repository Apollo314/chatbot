<template>
  <q-page
    :style-fn="
      (offset, height) => ({
        height: `${height - offset}px`,
        minHeight: `${height - offset}px`,
      })
    "
    class="column relative no-wrap compact-scrollbar"
  >
    <chat-page
      :conversations="conversations"
      @generationStopped="isGeneratingResponse = false"
      style="flex-grow: 1; padding-bottom: 12rem"
      :dark="$q.dark.isActive"
    ></chat-page>
    <user-prompt
      style="position: absolute; bottom: 0; left: 0; right: 0"
      :is-generating="isGeneratingResponse"
      :generated="conversations.length > 0"
      :dark="$q.dark.isActive"
      @prompt="request"
    ></user-prompt>
  </q-page>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import ChatPage from '../components/ChatPage.vue';
import UserPrompt from '../components/UserPrompt.vue';

import type { Conversation } from '../components/ChatPage.vue';

const isGeneratingResponse = ref(false);

const conversations = ref<Conversation[]>([]);

const lorem =
  'Lorem ipsum dolor sit amet consectetur adipisicing elit. Aperiam ducimus facere quam unde sit quae eveniet fugiat deleniti quisquam obcaecati nemo quod, rerum, harum consequuntur culpa, id neque! Accusamus, nobis.';

const request = (prompt: string) => {
  conversations.value.push({
    prompt: prompt,
    response: lorem,
  });
  isGeneratingResponse.value = true;
};

for (let i = 0; i < 5; i++) {
  request('hello');
}
</script>
