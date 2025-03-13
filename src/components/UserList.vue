<!-- src/views/UserList.vue -->
<template>
  <div>
    <input v-model="newUser.name" placeholder="Name">
    <input v-model="newUser.email" placeholder="Email">
    <button @click="addUser">Add User</button>
    <ul>
      <li v-for="user in users" :key="user.email">{{ user.name }} - {{ user.email }}</li>
    </ul>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      users: [],
      newUser: { name: '', email: '' }
    };
  },
  mounted() {
    this.fetchUsers();
  },
  methods: {
    fetchUsers() {
      axios.get('http://localhost:8080/api/users')
          .then(response => this.users = response.data);
    },
    addUser() {
      axios.post('http://localhost:8080/api/users', this.newUser)
          .then(() => {
            this.fetchUsers();
            this.newUser = { name: '', email: '' };
          });
    }
  }
};
</script>