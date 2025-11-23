<template>
  <div class="navbar">
    <h1 @click="$router.push('/')"> Vue 3</h1>
    <div class="navbar__btn">
        <my-button @click="$router.push('/posts')">Посты</my-button>
        <my-button 
        style="margin-left: 20px;"
         @click="$router.push('/about')">
         О сайте
         </my-button>
    </div>
  </div>
</template>

<script>
export default {

}
</script>

<style>
.navbar{
    height: 100px;
    background-color: #aaaaaa;      
    box-shadow: 4px 4px 10px rgba(0, 0, 0, 0.2);
    display: flex;
align-items: center;
    padding: 0 20px;
}
h1{
    color: #000;
    flex-grow: 1;
}
</style>