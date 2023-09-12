<template>
  <v-container>
    <v-data-table :headers="headers" :items="userPosts" class="elevation-1">
      <template v-slot:top>
        <v-toolbar flat>
          <v-toolbar-title>CRUD de Usuarios y Publicaciones</v-toolbar-title>
          <v-divider class="mx-4" inset vertical></v-divider>
          <v-spacer></v-spacer>
          <v-btn color="primary" dark @click="openCreateDialog">Nuevo</v-btn>
        </v-toolbar>
      </template>
      <template v-slot:item.actions="{ item }">
        <v-icon size="small" class="me-2" @click="openEditDialog(item)"
          >mdi-pencil</v-icon
        >
        <v-icon size="small" @click="openDeleteDialog(item)">mdi-delete</v-icon>
      </template>
    </v-data-table>

    <!-- CRUD Actions -->
    <v-dialog v-model="dialog" max-width="500px">
      <v-card>
        <v-card-title class="text-h5">{{ formTitle }}</v-card-title>
        <v-card-text>
          <v-container>
            <v-row>
              <v-col cols="12" sm="6" md="6">
                <v-text-field
                  v-model="editedItem.userName"
                  label="Nombre de Usuario"
                ></v-text-field>
              </v-col>
              <v-col cols="12" sm="6" md="6">
                <v-text-field
                  v-model="editedItem.title"
                  label="Título del Post"
                ></v-text-field>
              </v-col>
              <v-col cols="12">
                <v-textarea
                  v-model="editedItem.body"
                  label="Contenido del Post"
                ></v-textarea>
              </v-col>
            </v-row>
          </v-container>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue" text @click="close">Cancelar</v-btn>
          <v-btn color="blue" @click="save">Guardar</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <v-dialog v-model="dialogDelete" max-width="500px">
      <v-card>
        <v-card-title class="text-h5"
          >¿Estás seguro de que deseas eliminar este elemento?</v-card-title
        >
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue" text @click="closeDelete">Cancelar</v-btn>
          <v-btn color="blue" @click="deleteItemConfirm">OK</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-container>
</template>

<script>
export default {
  data() {
    return {
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
      userPosts: [], // Lista combinada de datos de usuarios y publicaciones
      editedIndex: -1,
      editedItem: {
        userName: "",
        title: "",
        body: "",
      },
      defaultItem: {
        userName: "",
        title: "",
        body: "",
      },
    };
  },
  computed: {
    formTitle() {
      return this.editedIndex === -1 ? "Nuevo Elemento" : "Editar Elemento";
    },
  },
  created() {
    this.fetchData();
  },
  methods: {
    async fetchData() {
      try {
        // Realizar solicitudes fetch para obtener usuarios, títulos y cuerpos de texto
        const [usersResponse, postsResponse] = await Promise.all([
          fetch("https://jsonplaceholder.typicode.com/users"),
          fetch("https://jsonplaceholder.typicode.com/posts"),
        ]);

        const [usersData, postsData] = await Promise.all([
          usersResponse.json(),
          postsResponse.json(),
        ]);

        // Combinar los datos de usuarios, títulos y cuerpos de texto
        this.userPosts = usersData.map((user, index) => ({
          userName: user.name,
          title: postsData[index] ? postsData[index].title : "",
          body: postsData[index] ? postsData[index].body : "",
        }));
      } catch (error) {
        console.error("Error al obtener datos:", error);
      }
    },
    openCreateDialog() {
      this.editedIndex = -1;
      this.editedItem = Object.assign({}, this.defaultItem);
      this.dialog = true;
    },
    openEditDialog(item) {
      this.editedIndex = this.userPosts.indexOf(item);
      this.editedItem = Object.assign({}, item);
      this.dialog = true;
    },
    openDeleteDialog(item) {
      this.editedIndex = this.userPosts.indexOf(item);
      this.editedItem = Object.assign({}, item);
      this.dialogDelete = true;
    },
    deleteItemConfirm() {
      if (this.editedIndex > -1) {
        this.userPosts.splice(this.editedIndex, 1);
      }
      this.closeDelete();
    },
    close() {
      this.dialog = false;
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem);
        this.editedIndex = -1;
      });
    },
    closeDelete() {
      this.dialogDelete = false;
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem);
        this.editedIndex = -1;
      });
    },
    save() {
      if (this.editedIndex > -1) {
        // Editar elemento existente
        Object.assign(this.userPosts[this.editedIndex], this.editedItem);
      } else {
        // Agregar nuevo elemento
        this.userPosts.push(this.editedItem);
      }
      this.close();
    },
  },
};
</script>
