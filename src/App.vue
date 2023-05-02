<template>
  <div>
    <form @submit.prevent="submitForm">
      <div>
        <label for="name">Nome:</label>
        <input type="text" id="name" v-model="name" />
      </div>
      <div>
        <label for="email">E-mail:</label>
        <input type="email" id="email" v-model="email" />
      </div>
      <div>
        <label for="password">Senha:</label>
        <input type="password" id="password" v-model="password" />
      </div>
      <button v-if="!isEditing" type="submit">Registrar</button>
      <button v-if="isEditing" type="submit">Editar</button>
      <button v-if="isEditing" type="button" @click="cancelEdit">Cancelar</button>
    </form>

    <div class="user-cards">
      <div v-for="(user, key) in users" :key="key" class="card">
        <div class="card-content">
          <div><strong>Nome:</strong> {{ user.name }}</div>
          <div><strong>E-mail:</strong> {{ user.email }}</div>
          <div><strong>Senha:</strong> {{ user.password }}</div>
        </div>
        <div>
          <button @click="editUser(user)">Editar</button>
          <button @click="deleteUser(key)">Excluir</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { defineComponent } from 'vue'

export default defineComponent({
  data() {
    return {
      name: "",
      email: "",
      password: "",
      users: {},
      isEditing: false,
      editingUserKey: null,
    }
  },
  created() {
    this.getUsers()
  },
  methods: {
    getUsers() {
      const users = { ...localStorage }
      for (const key in users) {
        try {
          const userObj = JSON.parse(users[key])
          this.users[key] = userObj
        } catch (e) {
          console.error(`Could not parse user data for key ${key}: ${e}`)
        }
      }
    },
    submitForm() {
      if (this.isEditing && this.editingUserKey) {
        const user = {
          name: this.name,
          email: this.email,
          password: this.password,
        }
        localStorage.setItem(this.editingUserKey, JSON.stringify(user))
        this.isEditing = false
        this.editingUserKey = null
      } else {
        const user = {
          name: this.name,
          email: this.email,
          password: this.password,
        }
        localStorage.setItem(user.email, JSON.stringify(user))
      }
      this.getUsers()
      this.name = ""
      this.email = ""
      this.password = ""
    },
    deleteUser(key) {
      localStorage.removeItem(key)
      delete this.users[key]
    },
    editUser(user) {
      this.name = user.name
      this.email = user.email
      this.password = user.password
      this.isEditing = true
      this.editingUserKey = user.email
    },
    cancelEdit() {
      this.isEditing = false
      this.editingUserKey = null
      this.name = ""
      this.email = ""
      this.password = ""
    },
  },
})
</script>

<style>
.card {
  border: 1px solid #ccc;
  margin: 10px;
  padding: 10px;
  width: 300px;
}

.card-content {
  display: flex;
  flex-direction: column;
}
</style>
