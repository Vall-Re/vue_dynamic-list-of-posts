<script setup>
import { ref, watch } from 'vue';
import AddPost from './AddPost.vue';
import EditPost from './EditPost.vue';
import PostPreview from './PostPreview.vue';
import Comment from './comments/Comment.vue';
import AddComment from './comments/AddComment.vue';

const props = defineProps({
  isSideBarOpen: Boolean,
  selectedPost: Object,
})

const emit = defineEmits(['created', 'delete', 'save']);
const isEditMode = ref(false);
const comments = ref([]);
const showCommentForm = ref(false);

watch(
  () => props.selectedPost, 
  async (newPost) => {
    if (newPost) {
      await fetchComments(newPost.id);
      showCommentForm.value = false;
    }
})

async function fetchComments(postId) {
  try {
    const res = await fetch(
      `https://mate.academy/students-api/comments?postId=${postId}`,
    );
    if (!res.ok) {
      throw new Error('Failed to fetch comments');
    }
    comments.value = await res.json();
  } catch (err) {
    console.error(err);
  }
}

function handlePostCreated(post) {
  emit('created', post);
}

function handleSave(post) {
  emit('save', post);
  isEditMode.value = false;
}

function handleDelete() {
  emit('delete', props.selectedPost.id);
}

function openCommentForm() {
  showCommentForm.value = true;
}

function cancelCommentForm() {
  showCommentForm.value = false;
}

function addComment(newComment) {
  comments.value.push(newComment);
}

function deleteComment(commentId) {
  comments.value = comments.value.filter(
    (comment) => comment.id !== commentId,
  );
}
</script>

<template>
  <div class="tile is-parent is-8-desktop Sidebar" :class="{ 'Sidebar--open': isSideBarOpen }" v-show="isSideBarOpen">
    <div class="tile is-child box is-success ">
      <div class="tile is-child box is-success ">
        <div class="content">
          <!-- Content here -->
          <h2 v-if="isEditMode">Post editing</h2>
          <AddPost v-if="!selectedPost" @created="handlePostCreated" />

          <EditPost v-else-if="isEditMode" :post="selectedPost" @save="handleSave" @cancel="isEditMode = false" />

          <PostPreview v-else :post="selectedPost" @edit="isEditMode = true" @delete="handleDelete" />
          
          <div v-if="comments.length === 0" class="block">
            <p class="title is-4">No comments yet</p>
          </div>

          <Comment 
          v-for="comment in comments" 
          :key="comment.id" 
          :comment="comment" 
          @deleteComment="deleteComment" 
          />

          <button
            v-if="!showCommentForm"
            type="button"
            class="button is-link mt-3"
            @click="openCommentForm"
          >
            Write a comment
          </button>

          <AddComment
            v-if="showCommentForm"
            @cancel="cancelCommentForm"
            @created="addComment"
            />
        </div>
      </div>
    </div>
  </div>
</template>