<template>
  <!-- Use a more semantic HTML structure -->
  <div class="iphone-cell">
    <header>
      <nav>
        <ul>
          <li>
            <a href="#" @click.prevent="activeFeature = 'todos'">Todos</a>
          </li>
          <li>
            <a href="#" @click.prevent="activeFeature = 'posts'">Posts</a>
          </li>
        </ul>
      </nav>
    </header>
    <!-- Use a more descriptive class name -->
    <div v-if="activeFeature === 'todos'" class="feature-todos">
      <!-- Todos feature -->
      <h1>Iphone Cell</h1>
      <div class="input-group">
        <input
          type="text"
          v-model="newIphoneType"
          placeholder="Tambahkan Tipe Iphone"
          @keyup.enter="addIphoneType"
        />
        <button @click="addIphoneType">Tambah</button>
      </div>
      <div class="iphone-types-list">
        <div
          v-for="(iphoneType, index) in filteredIphoneTypes"
          :key="index"
          :class="{ 'completed': iphoneType.completed }"
        >
          <input type="checkbox" v-model="iphoneType.completed" class="checkbox" />
          <span>{{ iphoneType.text }}</span>
          <button @click="deleteIphoneType(index)">Batal</button>
        </div>
      </div>
      <div class="filter-group">
        <label>
          <input type="checkbox" v-model="showOnlyIncomplete" />
          Tampilkan yang masih ada
        </label>
      </div>
    </div>
    <div v-if="activeFeature === 'posts'" class="feature-posts">
      <!-- Posts feature -->
      <h1>Posts</h1>
      <select v-model="selectedUser">
        <option v-for="user in users" :key="user.id" :value="user.id">{{ user.name }}</option>
      </select>
      <ul>
        <li v-for="post in posts" :key="post.id">
          <h2>{{ post.title }}</h2>
          <p>{{ post.body }}</p>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  name: 'IphoneCell',
  data() {
    return {
      activeFeature: 'todos',
      newIphoneType: '',
      iphoneTypes: [],
      showOnlyIncomplete: false,
      users: [],
      selectedUser: null,
      posts: []
    };
  },
  mounted() {
    this.fetchUsers();
  },
  methods: {
    addIphoneType() {
      if (this.newIphoneType.trim() !== '') {
        this.iphoneTypes.push({ text: this.newIphoneType.trim(), completed: false });
        this.newIphoneType = '';
      }
    },
    deleteIphoneType(index) {
      this.iphoneTypes.splice(index, 1);
    },
    fetchUsers() {
      fetch('https://jsonplaceholder.typicode.com/users')
        .then(response => response.json())
        .then(users => {
          this.users = users;
        });
    },
    fetchPosts() {
      if (this.selectedUser) {
        fetch(`https://jsonplaceholder.typicode.com/posts?userId=${this.selectedUser}`)
          .then(response => response.json())
          .then(posts => {
            this.posts = posts;
          });
      }
    }
  },
  computed: {
    filteredIphoneTypes() {
      return this.showOnlyIncomplete ? this.iphoneTypes.filter(iphoneType => !iphoneType.completed) : this.iphoneTypes;
    }
  },
  watch: {
    selectedUser() {
      this.fetchPosts();
    }
  }
};
</script>

<style scoped>
.iphone-cell {
  max-width: 800px;
  margin: 40px auto;
  padding: 20px;
  background-color: #f9f9f9;
  border: 1px solid #ddd;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}

.feature-todos, .feature-posts {
  padding: 20px;
}

.input-group {
  margin-bottom: 20px;
}

.input-group input[type="text"] {
  width: 50%;
  padding: 10px;
  font-size: 16px;
  border: 1px solid #ccc;
}

.input-group button {
  padding: 10px 20px;
  font-size: 16px;
  background-color: #4CAF50;
  color: #fff;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

.iphone-types-list {
  margin-bottom: 20px;
}

.iphone-types-list div {
  display: flex;
  align-items: center;
  margin-bottom: 10px;
}

.iphone-types-list div span {
  flex: 1;
  margin-left: 10px;
}

.filter-group {
  display: flex;
  align-items: center;
}

.filter-group label {
  margin-right: 10px;
}
</style>
