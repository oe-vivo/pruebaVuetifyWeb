<template>
    <v-data-table
      :headers="headers"
      :items="posts"
      :sort-by="[{ key: 'Autores', order: 'asc' }]"
      class="elevation-1"
    >
      <template v-slot:top>
        <v-toolbar
          flat
        >
          <v-toolbar-title>My CRUD</v-toolbar-title>
          <v-divider
            class="mx-4"
            inset
            vertical
          ></v-divider>
          <v-spacer></v-spacer>
          <v-dialog
            v-model="dialog"
            max-width="500px"
          >
            <template v-slot:activator="{ props }">
              <v-btn
                color="primary"
                dark
                class="mb-2"
                v-bind="props"
              >
                New Item
              </v-btn>
            </template>
            <v-card>
              <v-card-title>
                <span class="text-h5">{{ formTitle }}</span>
              </v-card-title>
  
              <v-card-text>
                <v-container>
                    <v-row v-for="(userPost, index) in userPosts" :key="index">
                    <v-col cols="4">
                        <h2>Título del Post</h2>
                        <p>{{ userPost.title }}</p>
                    </v-col>
                    <v-col cols="4">
                        <h2>Contenido del Post</h2>
                        <p>{{ userPost.body }}</p>
                    </v-col>
                    <v-col cols="4">
                        <h2>Nombre de Usuario</h2>
                        <p>{{ userPost.userName }}</p>
                    </v-col>

                  </v-row>
                </v-container>
              </v-card-text>
  
              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn
                  color="blue-darken-1"
                  variant="text"
                  @click="close"
                >
                  Cancel
                </v-btn>
                <v-btn
                  color="blue-darken-1"
                  variant="text"
                  @click="save"
                >
                  Save
                </v-btn>
              </v-card-actions>
            </v-card>
          </v-dialog>
          <v-dialog v-model="dialogDelete" max-width="500px">
            <v-card>
              <v-card-title class="text-h5">Are you sure you want to delete this item?</v-card-title>
              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="blue-darken-1" variant="text" @click="closeDelete">Cancel</v-btn>
                <v-btn color="blue-darken-1" variant="text" @click="deleteItemConfirm">OK</v-btn>
                <v-spacer></v-spacer>
              </v-card-actions>
            </v-card>
          </v-dialog>
        </v-toolbar>
      </template>
      <template v-slot:item.actions="{ item }">
        <v-icon
          size="small"
          class="me-2"
          @click="editItem(item.raw)"
        >
          mdi-pencil
        </v-icon>
        <v-icon
          size="small"
          @click="deleteItem(item.raw)"
        >
          mdi-delete
        </v-icon>
      </template>
      <template v-slot:no-data>
        <v-btn
          color="primary"
          @click="initialize"
        >
          Reset
        </v-btn>
      </template>
    </v-data-table>
  </template>


  <script>
 export default {
    data() {
      return {
        users: [],
        posts: [],
        userPosts: [], // Lista combinada de datos de usuarios y publicaciones
        dialog: false,
      dialogDelete: false,
      headers: [
        {
          title: 'Autores',
          align: 'start',
          sortable: false,
          key: 'usersPost',
        },
        { title: 'Titulo', key: 'title' },
        { title: 'Post', key: 'body' },
        { title: 'Actions', key: 'actions', sortable: false },
      ],
        };
    },
    created() {
      this.fetchUsers();
      this.fetchPosts();
    },
    methods: {
      async fetchUsers() {
        try {
          const response = await fetch('https://jsonplaceholder.typicode.com/users');
          const data = await response.json();
          this.users = data;
          this.combineUserData();
        } catch (error) {
          console.error('Error al obtener datos de usuarios:', error);
        }
      },
      async fetchPosts() {
        try {
          const response = await fetch('https://jsonplaceholder.typicode.com/posts');
          const data = await response.json();
          this.posts = data;
          this.combineUserData();
        } catch (error) {
          console.error('Error al obtener datos de publicaciones:', error);
        }
      },
      combineUserData() {
        // Combinar los datos de usuarios y publicaciones
        this.userPosts = this.users.map((user, index) => ({
          userName: user.name,
          title: this.posts[index] ? this.posts[index].title : '',
          body: this.posts[index] ? this.posts[index].body : '',
        }));
      },
    },
  };
  </script>
  