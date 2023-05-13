<template>
  <div class="app">
    <h1>Страница с постами</h1>
    <my-input v-model="searchQuerry" />
    <div class="app__btns">
      <my-button @click="showDialog" style="margin: 15px"
        >Создать пост</my-button
      >
      <my-select v-model="selectedSort" :options="sortOptions" />
    </div>
    <my-dialog v-model:show="dialogVisible"
      ><post-form @create="createPost"
    /></my-dialog>
    <post-list
      :posts="sortedPosts"
      @remove="removePost"
      v-if="!isPostLoading"
    />
    <div v-else>Идёт загрузка...</div>
  </div>
</template>

<script>
import PostForm from "@/components/PostForm.vue";
import PostList from "@/components/PostList.vue";
import axios from "axios";

export default {
  components: { PostForm, PostList },
  data() {
    return {
      posts: [],
      dialogVisible: false,
      isPostLoading: false,
      selectedSort: "",
      searchQuerry: "",
      sortOptions: [
        { value: "title", name: "по названию" },
        { value: "body", name: "по содержимому" },
      ],
    };
  },
  methods: {
    createPost(post) {
      this.posts.push(post);
      this.dialogVisible = false;
    },
    removePost(post) {
      this.posts = this.posts.filter((p) => p.id !== post.id);
    },
    showDialog() {
      this.dialogVisible = true;
    },
    async fetchPosts() {
      try {
        this.isPostLoading = true;
        const response = await axios.get(
          "https://jsonplaceholder.typicode.com/posts?_limit=10"
        );
        this.posts = response.data;
      } catch (e) {
        alert("Ошибка");
      } finally {
        this.isPostLoading = false;
      }
    },
  },
  mounted() {
    this.fetchPosts();
  },
  computed: {
    sortedPosts() {
      return [...this.posts].sort((post1, post2) =>
        post1[this.selectedSort]?.localeCompare(post2[this.selectedSort])
      );
    },
  },
};
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

.app {
  padding: 20px;
}

.app__btns {
  margin: 15px;
  display: flex;
  justify-content: space-between;
}
</style>
