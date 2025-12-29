<script setup>
import { ref, watch } from 'vue';

const props = defineProps({
  post: Object,
})

const emit = defineEmits(['save', 'cancel']);

const title = ref('');
const body = ref('');

watch(
  () => props.post,
  (newPost) => {
    title.value = newPost.title;
    body.value = newPost.body;
  },
  { immediate: true },
);

function saveEdits() {
  emit('save', {
    ...props.post,
    title: title.value,
    body: body.value,
  });
}

function cancelEdit() {
  emit('cancel');
}
</script>

<template>
<div class="block">
    <div class="field">
      <label class="label">Title</label>
      <div class="control">
        <input class="input" v-model="title" />
      </div>
    </div>

    <div class="field">
      <label class="label">Write Post body</label>
      <div class="control">
        <textarea class="textarea" v-model="body"></textarea>
      </div>
    </div>

    <div class="buttons mt-3">
      <button class="button is-primary" @click="saveEdits">Save</button>
      <button class="button" @click="cancelEdit">Cancel</button>
    </div>
  </div>
</template>