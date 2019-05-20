<template>
  <div class="main">
    <v-data-table :headers="headers" :items="users" class="elevation-1">
      <template v-slot:items="props">
        <td class="text-xs-right">{{ props.item.id }}</td>
        <td class="text-xs-right">{{ props.item.name }}</td>
        <td class="text-xs-right">{{ props.item.email }}</td>
        <td class="text-xs-right">{{ props.item.company.name }}</td>
        <td class="justify-center layout px-0">
          <v-icon small class="mr-2" @click="openDialog(props.item)">
            edit
          </v-icon>
        </td>
      </template>
    </v-data-table>
    <div class="text-xs-center">
      <v-dialog v-model="dialog" width="500">
        <v-card>
          <v-card-title class="headline grey lighten-2" primary-title>Edit User</v-card-title>
            <v-form ref="form" v-model="valid" lazy-validation>
            <v-text-field
              class="input"
              v-model="savedUser.id"
              label="id"
              disabled
            ></v-text-field>
            <v-text-field
              class="input"
              v-model="savedUser.name"
              label="name"
              :rules="[v => !!v || 'Name is required']"
              required
            ></v-text-field>
            <v-text-field
              class="input"
              v-model="savedUser.email"
              label="E-mail"
              :rules="[v => !!v || 'Email is required']"
              required
            ></v-text-field>
            <v-text-field
              class="input"
              v-model="savedUser.company"
              label="Company"
              :rules="[v => !!v || 'Company name is required']"
              required
            ></v-text-field>
          </v-form>
          <v-divider></v-divider>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn :disabled="!valid" color="primary" flat @click="editUser">accept</v-btn>
            <v-btn color="error" flat @click="dialog = false">cancel</v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      headers: [
        {text: 'id',value: 'id'},
        {text: 'name',value: 'name'},
        {text: 'email',value: 'email'},
        {text: 'company',value: 'company'}
      ],
      users: [],
      dialog: false,
      valid: true,
      savedUser: {}
    }
  },
  methods: {
    async fetchUsers(val) {
      const res = await fetch(val)
      if (!res.ok) throw new Error(res.status)
      this.users = await res.json()
    },
    openDialog(val) {
      this.savedUser = {
        id: val.id,
        name: val.name,
        email: val.email,
        company: val.company.name
      }
      this.dialog = true
    },
    editUser() {
      fetch(`https://jsonplaceholder.typicode.com/users/${this.savedUser.id}`, {
        method: 'PUT',
        body: JSON.stringify({
          ...this.savedUser
        }),
        headers: {
          "Content-type": "application/json; charset=UTF-8"
        }
      })
      .then(response => response.json())
      .then(json => console.log(json))

      this.dialog = false
    }
  },
  mounted() {
    this.fetchUsers('https://jsonplaceholder.typicode.com/users')
  }
}
</script>


<style>
.container {
  padding: 0;
  margin: 0;
}
.main {
  height: 100vh;
  width: 100vw;
  display: flex;
  justify-content: center;
  align-items: center;
}
.input {
  margin: 0 50px;
}
.headline {
  margin-bottom: 20px;
}
</style>
