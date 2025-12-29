<script setup>
import { ref, onMounted } from 'vue';
import PostList from './components/PostList.vue';
import SideBar from './components/SideBar.vue';
import Login from './components/Login.vue';

const user = ref(null);
const isSideBarOpen = ref(false);
const selectedPost = ref(null);
const posts = ref([]);
const isLoading = ref(true);
const error = ref(null);

const BASE_URL = 'https://mate.academy/students-api'

function handleLogin(newUser) {
  user.value = newUser;
  posts.value = [];
  isLoading.value = false;
  error.value = null;
}

function handleLogout() {
  user.value = null;
}

function openNewSideBar() {
  selectedPost.value = null;
  isSideBarOpen.value = true;
}

function openPostSideBar(post) {
  selectedPost.value = post;
  isSideBarOpen.value = true;
}

function addPost(post) {
  posts.value.push(post);
  isSideBarOpen.value = false;
}

function updatePost(updatedPost) {
  const index = posts.value.findIndex(p => p.id === updatedPost.id);
  if (index !== -1) {
    posts.value[index] = updatedPost;
  }
}

function deletePost(postId) {
  posts.value = posts.value.filter(p => p.id !== postId);
  isSideBarOpen.value = false;
}

</script>

<template>
  <nav class="navbar" 
    role="navigation"
    aria-label="main navigation"
    >
    <div class="navbar-end" v-if="user">
      <div class="navbar-item">
        <div class="buttons">
          <div class="mr-5 mb-2">
            <p>User: {{ user.name }}</p>
          </div>
            <a class="button is-light" @click="handleLogout">Logout</a>          
        </div>
      </div>

    </div>
  </nav>

  <Login v-if="!user" @login="handleLogin" />
  <div v-else>
    <PostList 
      :posts="posts" 
      :isLoading="isLoading"
      :error="error"
      :selectedPost="selectedPost"
      @openNew = "openNewSideBar"
      @selectedPost="openPostSideBar" 
    />
    <SideBar 
      :isSideBarOpen="isSideBarOpen" 
      :selectedPost="selectedPost"
      @created="addPost"
      @save="updatePost"
      @delete="deletePost"
    />
  </div>
</template>

