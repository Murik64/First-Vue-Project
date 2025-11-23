<template>
  <div>
    <h1>Страница с постами</h1>
    <my-input v-model="SearchQuery" placeholder="Поиск..."/>
    <div class="app__btns">
      <div class="app__btn">
        <my-button @click="fetchPosts">Получить посты</my-button>
    <my-button style="margin: 15px 15px;" @click="showDialog">
      Созадать пост
    </my-button>
      </div>
      <div class="app__select">
        <my-select 
        v-model="selectedSort"
        :options="sortOptions"
        style="margin-top: 15px;"
        />
      </div>
    </div>
    <my-dialog v-model:show="dialogVisible">
      <post-form  @create="createPost" />
    </my-dialog>
  
    <post-list :posts="sortedAndSearchPosts" @remove="removePost" v-if="!isPostsLoading"/>
    <div v-else>Идет загрузка...</div>
  <div class="page__wrapper">
    <div 
    v-for="pageNumber in totalPages" 
    :key="pageNumber"
    class="page__btn"
    :class="{
        'current-page': page === pageNumber
    }"
    @click="changePage(pageNumber)">
        {{ pageNumber }}
    </div>
  </div>
  </div>



</template>
  
<script>
import PostList from "@/components/PostList.vue";
import PostForm from "@/components/PostForm.vue";
import MyDialog from '@/components/UI/MyDialog.vue';
import MyButton from '@/components/UI/MyButton.vue';
import axios from "axios";
import MySelect from "@/components/UI/MySelect.vue";
import MyInput from '@/components/UI/MyInput.vue';


export default {
  components: {
    PostList,
    PostForm,
    MyDialog,
    MyButton,
    MySelect,
    MyInput,
  },
  data() {
    return {
      posts: [],
      dialogVisible: false,
      isPostsLoading: false,
      selectedSort: '',
      SearchQuery: '',
      page: 1,
      limit: 10,
      totalPages: 0,
      sortOptions: [
        {value: 'title', name: 'По названию'},
        {value: 'body', name: 'По описанию'},
      ],
    };
  },
  methods: {
    createPost(post,) {
      this.posts.push(post);
      this.dialogVisible = false;
    },
    removePost(post) {
      this.posts = this.posts.filter(p => p.id !== post.id)
    },
    showDialog() {
      this.dialogVisible = true;
    },
    async fetchPosts() {
      try {
        this.isPostsLoading = true;
        setTimeout( async () => {
          const response = await axios.get("https://jsonplaceholder.typicode.com/posts", {
            params: {
              _page: this.page,
              _limit: this.limit,
            }
          });
          this.totalPages = Math.ceil(
            response.headers['x-total-count'] / this.limit
          );
          this.posts = response.data;   
          console.log(response);
          
          this.isPostsLoading = false;     
        }, 1000);
      } catch (e) {
        alert(`Ошибка ${e}` );
        console.log(e);
    } 
    },
    mounted() {
      this.fetchPosts();
    },
    changePage(pageNumber) {
      this.page = pageNumber;
      this.fetchPosts();
    },
  },
  computed: {
    sortedPosts() {
      return [...this.posts].sort((post1, post2) => 
            post1[this.selectedSort]?.localeCompare(post2[this.selectedSort]));
    },
    sortedAndSearchPosts() {
      return this.sortedPosts.filter(post => post.title.toLowerCase().includes(this.SearchQuery.toLowerCase()));
    },
  },
}
</script>

<style>

.app__btns{
  display: flex;
  justify-content: space-between;
}
.page__wrapper{
display: flex;
margin-top: 15px;
gap: 10px;
}
.page__btn{
    border: 1px solid black;
    padding: 10px;
    border-radius: 10px;
}
.current-page{
    border: 2px solid teal;
}
</style>
