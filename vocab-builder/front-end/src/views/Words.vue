<template>
  <div>
    <h1>Dictionary</h1>
    <table id="words" class="ui celled compact table">
      <thead>
        <tr>
          <th>English</th>
          <th>German</th>
          <th>France</th>
          <th>Vietnamse</th>
          <th colspan="3"></th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(word, i) in paginatedWords" :key="i">
          <td>{{ word.english }}</td>
          <td>{{ word.german }}</td>
          <td>{{ word.france }}</td>
          <td>{{ word.vietnam }}</td>

          <td width="75" class="center aligned">
            <router-link :to="{ name:'show', params: { id: word._id }}">Show</router-link>
          </td>
          <td width="75" class="center aligned">
            <router-link :to="{ name:'edit', params: { id: word._id }}">Edit</router-link>
          </td>
          <td width="75" class="center aligned" @click.prevent="onDestroy(word._id)">
            <a :href="`/words/${word._id}`">Delete</a>
          </td>
        </tr>
      </tbody>
    </table>
    
    <div class="pagination">
      <button 
        class="page-button" 
        @click="changePage(currentPage - 1)" 
        :disabled="currentPage === 1"
      >
        Previous
      </button>
      <span>Page {{ currentPage }} of {{ totalPages }}</span>
      <button 
        class="page-button" 
        @click="changePage(currentPage + 1)" 
        :disabled="currentPage === totalPages"
      >
        Next
      </button>
    </div>
  </div>
</template>

<script>
import { api } from '../helpers/helpers';

export default {
  name: 'words',
  data() {
    return {
      words: [],              // List of all words
      currentPage: 1,         // Current page number
      pageSize: 2,           // Number of items per page
    };
  },
  computed: {
    totalPages() {
      return Math.ceil(this.words.length / this.pageSize);
    },
    paginatedWords() {
      const start = (this.currentPage - 1) * this.pageSize;
      const end = start + this.pageSize;
      return this.words.slice(start, end);
    }
  },
  methods: {
    async onDestroy(id) {
      const sure = window.confirm('Are you sure?');
      if (!sure) return;
      await api.deleteWord(id);
      this.flash('Word deleted successfully!', 'success');
      this.words = this.words.filter(word => word._id !== id);
      // Ensure current page is still valid
      if (this.currentPage > this.totalPages) {
        this.currentPage = this.totalPages;
      }
    },
    changePage(page) {
      if (page >= 1 && page <= this.totalPages) {
        this.currentPage = page;
      }
    }
  },
  async mounted() {
    this.words = await api.getWords();
  }
};
</script>

<style scoped>
.page-button {
  background-color: green;
  color: white;
  border: none;
  padding: 8px 16px;
  margin: 0 4px;
  cursor: pointer;
  border-radius: 4px;
  font-size: 14px;
}

.page-button:disabled {
  background-color: lightgray;
  cursor: not-allowed;
}
</style>


