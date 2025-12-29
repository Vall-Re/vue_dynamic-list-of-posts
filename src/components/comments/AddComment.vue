<script setup>
import { ref } from 'vue';

const props = defineProps({
  name: {
    type: String,
    default: 'new-comment'
  }
});

const emit = defineEmits(['created', 'cancel']);

const authorName = ref('');
const email = ref('');
const body = ref('');

const errors = ref({
  name: '',
  email: '',
  body: ''
});

function validate() {
  let valid = true;
  errors.value = { name: '', email: '', body: '' };

  if (!authorName.value.trim()) {
    errors.value.name = 'Name is required';
    valid = false;
  }
  if (!email.value.trim()) {
    errors.value.email = 'Email is required';
    valid = false;
  } else if (!/^\S+@\S+\.\S+$/.test(email.value)) {
    errors.value.email = 'Email is invalid';
    valid = false;
  }
  if (!body.value.trim()) {
    errors.value.body = 'Comment cannot be empty';
    valid = false;
  }

  return valid;
}

function submitComment() {
  if (!validate()) return;

  const newComment = {
    id: Date.now(),
    name: authorName.value,
    email: email.value,
    body: body.value
  };

  emit('created', newComment);

  body.value = '';
}

function cancelForm() {
  emit('cancel');
}
</script>

<template>
  <div class="box mt-3">
    <!-- Name -->
    <div class="field" data-cy="NameField">
      <label class="label" :for="`comment-author-name-${name}`">Name</label>
      <div class="control has-icons-left has-icons-right">
        <input
          type="text"
          :id="`comment-author-name-${name}`"
          v-model="authorName"
          placeholder="Your name"
          class="input"
          :class="{ 'is-danger': errors.name }"
        />
        <span class="icon is-small is-left">
          <i class="fas fa-user"></i>
        </span>
        <span v-if="errors.name" class="icon is-small is-right has-text-danger">
          <i class="fas fa-exclamation-triangle"></i>
        </span>
      </div>
      <p v-if="errors.name" class="help is-danger">{{ errors.name }}</p>
    </div>

    <!-- Email -->
    <div class="field" data-cy="EmailField">
      <label class="label" :for="`comment-email-${name}`">Email</label>
      <div class="control has-icons-left has-icons-right">
        <input
          type="email"
          :id="`comment-email-${name}`"
          v-model="email"
          placeholder="Your email"
          class="input"
          :class="{ 'is-danger': errors.email }"
        />
        <span class="icon is-small is-left">
          <i class="fas fa-envelope"></i>
        </span>
        <span v-if="errors.email" class="icon is-small is-right has-text-danger">
          <i class="fas fa-exclamation-triangle"></i>
        </span>
      </div>
      <p v-if="errors.email" class="help is-danger">{{ errors.email }}</p>
    </div>

    <!-- Comment Body -->
    <div class="field" data-cy="BodyField">
      <label class="label" :for="`comment-${name}`">Comment</label>
      <div class="control">
        <textarea
          :id="`comment-${name}`"
          v-model="body"
          placeholder="Write your comment..."
          class="textarea"
          :class="{ 'is-danger': errors.body }"
        ></textarea>
      </div>
      <p v-if="errors.body" class="help is-danger">{{ errors.body }}</p>
    </div>

    <!-- Buttons -->
    <div class="buttons mt-3">
      <button type="button" class="button is-primary" @click="submitComment">Submit</button>
      <button type="button" class="button" @click="cancelForm">Cancel</button>
    </div>
  </div>
</template>
