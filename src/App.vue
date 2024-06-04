<template>
  <div class="container">
    <form @submit.prevent="save" class="form">
      <input type="text" v-model="form.title" placeholder="Title" class="input"/><br />
      <textarea v-model="form.content" placeholder="Content" class="textarea"></textarea><br />
      <button type="submit" class="button">Save</button>
    </form>
    <ul class="article-list">
      <li v-for="article in articles" :key="article.id" class="article">
        <h3>{{ article.title }}</h3>
        <p>{{ article.content }}</p>
        <button @click="edit(article)" class="button">Edit</button>
        <button @click="remove(article.id)" class="button delete">Delete</button>
      </li>
    </ul>
    <button @click="load" class="button">Load</button>
  </div>
</template>

<script>
import { ref, onMounted, reactive } from 'vue';
import axios from 'axios';

export default {
  setup() {
    const form = reactive({
      id: null,
      title: '',
      content: ''
    });

    const articles = ref([]);

    async function load() {
      try {
        const response = await axios.get('http://localhost:3000/articles');
        articles.value = response.data;
      } catch (error) {
        console.error('Error loading articles:', error);
      }
    }

    async function save() {
      try {
        const url = form.id
          ? `http://localhost:3000/articles/${form.id}`
          : 'http://localhost:3000/articles';
        const method = form.id ? 'put' : 'post';
        const response = await axios[method](url, form);

        if (form.id) {
          // Update the existing article in the list
          articles.value = articles.value.map(article =>
            article.id === response.data.id ? response.data : article
          );
        } else {
          // Add the new article to the list
          articles.value.push(response.data);
        }

        // Reset form
        form.id = null;
        form.title = '';
        form.content = '';
      } catch (error) {
        console.error('Error saving article:', error);
      }
    }

    async function remove(id) {
      try {
        await axios.delete(`http://localhost:3000/articles/${id}`);
        articles.value = articles.value.filter(article => article.id !== id);
      } catch (error) {
        console.error('Error deleting article:', error);
      }
    }

    function edit(article) {
      form.id = article.id;
      form.title = article.title;
      form.content = article.content;
    }
    function deleteArticle(id) {
     var article = document.getElementById('article-' + id);
      article.remove();
    }

    onMounted(load);

    return { form, articles, save, edit, remove, load };
  }
};
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Roboto:wght@400;700&display=swap');

body {
  margin: 0;
  font-family: 'Roboto', sans-serif;
  background-image: url('@/assets/bgbru.jpg') no-repeat center center fixed;
  background-size: cover;
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
}

.container {
  width: 90%;
  max-width: 800px;
  background: rgba(255, 255, 255, 0.9);
  padding: 30px;
  border-radius: 10px;
  box-shadow: 0 10px 25px rgba(0, 0, 0, 0.2);
  backdrop-filter: blur(10px);
  margin: 20px;
}

.form {
  margin-bottom: 20px;
}

.input, .textarea {
  width: 100%;
  padding: 15px;
  margin-bottom: 15px;
  border: 1px solid #ccc;
  border-radius: 5px;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1) inset;
  transition: border-color 0.3s, box-shadow 0.3s;
}

.input:focus, .textarea:focus {
  border-color: #3498db;
  box-shadow: 0 0 5px rgba(52, 152, 219, 0.5);
  outline: none;
}

.button {
  padding: 10px 20px;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  transition: background-color 0.3s, transform 0.3s;
  font-weight: bold;
  font-size: 14px;
}

.button.save {
  background: linear-gradient(90deg, #2ecc71, #27ae60);
  color: #fff;
}

.button.save:hover {
  background: linear-gradient(90deg, #27ae60, #2ecc71);
  transform: translateY(-2px);
}

.button.edit {
  background: linear-gradient(90deg, #3498db, #2980b9);
  color: #fff;
}

.button.edit:hover {
  background: linear-gradient(90deg, #2980b9, #3498db);
  transform: translateY(-2px);
}

.button.delete {
  background: linear-gradient(90deg, #e74c3c, #c0392b);
  color: #fff;
}

.button.delete:hover {
  background: linear-gradient(90deg, #c0392b, #e74c3c);
  transform: translateY(-2px);
}

.button.load {
  background: linear-gradient(90deg, #95a5a6, #7f8c8d);
  color: #fff;
}

.button.load:hover {
  background: linear-gradient(90deg, #7f8c8d, #95a5a6);
  transform: translateY(-2px);
}

.article-list {
  list-style-type: none;
  padding: 0;
}

.article {
  padding: 20px;
  margin-bottom: 15px;
  background: rgba(255, 255, 255, 0.95);
  border: 1px solid #ddd;
  border-radius: 5px;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
  transition: transform 0.3s, box-shadow 0.3s;
}

.article:hover {
  transform: translateY(-2px);
  box-shadow: 0 5px 15px rgba(0, 0, 0, 0.2);
}

.article h3 {
  margin: 0 0 10px;
  font-size: 24px;
  color: #333;
}

.article p {
  margin: 0 0 10px;
  font-size: 18px;
  color: #666;
}

.button-group {
  display: flex;
  justify-content: flex-end;
}

.button-group .button {
  margin-left: 10px;
}
</style>
