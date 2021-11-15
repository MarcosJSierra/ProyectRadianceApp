<template>
  <v-container>
    <v-container>
      <v-autocomplete
        v-model="editedItem.tags"
        :items="tags"
        solo
        chips
        rounded
        label="Selecciona un tag para buscar articulos"
        prepend-inner-icon="mdi-tag"
        color="white"
        item-text="name"
        return-object
        @change="getArticles(editedItem.tags)"
      >
        <template v-slot:selection="data">
          <v-chip
            dark
            v-bind="data.attrs"
            :input-value="data.selected"
            @click="data.select"
          >
            <v-avatar left>
              <v-icon :color="data.item.color">{{ data.item.icon }}</v-icon>
            </v-avatar>
            {{ data.item.name }}
          </v-chip>
        </template>
        <template v-slot:item="data">
          <template v-if="typeof data.item !== 'object'">
            <v-list-item-content v-text="data.item"></v-list-item-content>
          </template>
          <template v-else>
            <v-list-item-avatar>
              <v-icon :color="data.item.color">{{ data.item.icon }}</v-icon>
            </v-list-item-avatar>
            <v-list-item-content>
              <v-list-item-title v-html="data.item.name"></v-list-item-title>
              <v-list-item-subtitle v-html="data.item.group"></v-list-item-subtitle>
            </v-list-item-content>
          </template>
        </template>
      </v-autocomplete>
    </v-container>
    <v-divider></v-divider>
    <v-container>
      <v-row>
        <v-col v-for="(item, index) in articles" :key="index" lg="12" md="6">
          <v-card @click="editItem(item)">
            <div class="d-flex flex-no-wrap justify-space-between">
              <div>
                <h2 class="section-title px-3 pt-3">{{ item.tittle }}</h2>
                <v-card-text>
                  <v-chip-group>
                    <v-chip
                      v-for="(tag, index) in item.tags"
                      :key="index"
                      :value="tag.tagId"
                      dark
                    >
                      <v-avatar left>
                        <v-icon :color="tag.color">{{ tag.icon }}</v-icon>
                      </v-avatar>
                      {{ tag.name }}
                    </v-chip>
                  </v-chip-group>
                  <p class="text ma-0">
                    {{ "Fecha de creación: " }}{{ item.creationDate }}
                  </p>
                  <p class="text">{{ "Escrito por: " }}{{ item.user.name }}</p>
                  <p class="description">{{ item.description }}</p>
                </v-card-text>
              </div>
              <div>
                <v-container fluid fill-height>
                  <v-avatar height="200" width="300" tile>
                    <v-img :src="item.image"></v-img>
                  </v-avatar>
                </v-container>
              </div>
            </div>
          </v-card>
        </v-col>
      </v-row>
    </v-container>
    <template>
      <div class="text-center">
        <v-dialog v-model="dialog" width="1000px">
          <v-card>
            <v-container>
              <v-img :src="this.editedItem.image" width="1000px"></v-img>
              <h2 class="section-title px-3 pt-3">{{ this.editedItem.tittle }}</h2>
              <v-card-text>
                <v-chip-group>
                  <v-chip
                    v-for="(tag, index) in this.editedItem.tags"
                    :key="index"
                    :value="tag.tagId"
                    dark
                  >
                    <v-avatar left>
                      <v-icon :color="tag.color">{{ tag.icon }}</v-icon>
                    </v-avatar>
                    {{ tag.name }}
                  </v-chip>
                </v-chip-group>
                <p class="text ma-0">
                  {{ "Fecha de creación: " }}{{ this.editedItem.creationDate }}
                </p>
                <p class="text">{{ "Escrito por: " }}{{ this.editedItem.user.name }}</p>
                <p class="description">{{ this.editedItem.description }}</p>
              </v-card-text>
            </v-container>
            <v-divider></v-divider>
            <v-container>
              <v-card elevation="0">
                <span v-html="editedItem.content"></span>
              </v-card>
            </v-container>
            <v-divider></v-divider>
            <h2 class="section-title px-3 pt-3">{{ "Comentarios" }}</h2>
            <v-divider></v-divider>
            <v-container>
              <v-list color="#E5E5E5" rounded>
                <v-list-item-group>
                  <template v-for="(item, index) in comments" class="py-2">
                    <v-list-item :key="item.title">
                      <template>
                        <v-list-item-content>
                          <v-list-item-title v-text="item.comment"></v-list-item-title>

                          <v-list-item-subtitle v-text="item.user"></v-list-item-subtitle>

                          <v-list-item-subtitle
                            v-text="item.creationDate"
                          ></v-list-item-subtitle>
                        </v-list-item-content>
                      </template>
                    </v-list-item>
                  </template>
                </v-list-item-group>
              </v-list>
            </v-container>
            <v-divider></v-divider>
            <v-card class="pa-3" elevation="0">
              <div>
                <div>
                  <v-text-field
                    label="Escribir comentario"
                    placeholder="Escribir comentario"
                    name="name"
                    type="text"
                    solo
                    v-model="editedComment.comment"
                  >
                  </v-text-field>
                </div>
                <v-layout class="justify-center">
                  <v-btn color="grey darken-4" dark @click="save(editedComment)"
                    >Publicar</v-btn
                  >
                </v-layout>
              </div>
            </v-card>
          </v-card>
        </v-dialog>
      </div>
    </template>
  </v-container>
</template>
<script>
import axios from "axios";
import store from "../../store/index";

export default {
  name: "Magazine",
  data: () => {
    return {
      loading: true,
      dialog: false,
      dialogDelete: false,
      role: store.getters["role"],
      userMenu: [
        { title: "Ver Perfil", icon: "mdi-account", to: "/radiance/profile" },
        { title: "Salir", icon: "mdi-logout", to: "/login" },
      ],
      headers: [
        {
          text: "Nombre",
          align: "center",
          value: "tittle",
        },
        {
          text: "Fecha de modificación",
          align: "center",
          value: "lastModifyDate",
          sortable: false,
        },
        { text: "Creado por", align: "center", value: "user.name", sortable: false },
        { text: "Actions", align: "center", value: "actions", sortable: false },
      ],
      base: [
        { title: "Home", icon: "mdi-home", comment: "/radiance/home" },
        {
          title: "Revista",
          icon: "mdi-newspaper-variant-outline",
          comment: "/radiance/magazine",
        },
      ],
      articles: [],
      tags: [],
      comments: [],
      editedIndex: -1,
      editedComment: {
        commetId: "",
        comment: "",
        articleId: "",
        creationDate: "",
        user: "",
      },
      defaultComment: {
        commetId: "",
        comment: "",
        articleId: "",
        creationDate: "",
        user: "",
      },
      editedItem: {
        articleId: "",
        creationDate: "",
        tittle: "",
        description: "",
        content: "",
        user: {},
        lastModifyDate: "",
        image: "",
        tags: [],
      },
      defaultItem: {
        articleId: "",
        creationDate: "",
        tittle: "",
        description: "",
        content: "",
        user: {},
        lastModifyDate: "",
        image: "",
        tags: [],
      },
    };
  },

  computed: {
    formTitle() {
      return this.editedIndex === -1 ? "Nuevo Artículo" : "Editar Artículo";
    },
  },

  watch: {
    dialog(val) {
      val || this.close();
    },
    dialogDelete(val) {
      val || this.closeDelete();
    },
  },

  created() {
    this.initialize();
  },

  methods: {
    initialize() {
      this.loading = true;
      this.getArticles();
      axios
        .get("tag/")
        .then((response) => {
          if (response.status == 200) {
            this.tags = response.data;
          } else {
            console.log(response.status);
          }
        })
        .catch((error) => {
          console.log(response.status);
          let alert = {
            alert: true,
            alert_active: true,
            alert_message: "No se pudo obtener la lista de tags",
            alert_color: "error",
          };
          this.$emit("chanceAlert", alert);
        });
      this.loading = false;
    },

    getComments(articleId) {
      axios
        .get("comment/" + articleId)
        .then((response) => {
          if (response.status == 200) {
            this.comments = response.data;
          }
        })
        .catch((error) => {
          let alert = {
            alert: true,
            alert_active: true,
            alert_message: "No se pudo obtener la lista de comentarios",
            alert_color: "error",
          };
          this.$emit("chanceAlert", alert);
        });
      this.loading = false;
    },

    getArticles(tags) {
      if(tags != null) {
          console.log("get personalizado")
          axios
        .get("article/", {params: {filter: tags.name}})
        .then((response) => {
          if (response.status == 200) {
            this.articles = response.data;
          } else {
            console.log(response.status);
          }
        })
        .catch((error) => {
          let alert = {
            alert: true,
            alert_active: true,
            alert_message: "No se pudo obtener la lista de artículos",
            alert_color: "error",
          };
          this.$emit("chanceAlert", alert);
        });
      } else {
          console.log("get normal");
          axios
        .get("article/")
        .then((response) => {
          if (response.status == 200) {
            this.articles = response.data;
          } else {
            console.log(response.status);
          }
        })
        .catch((error) => {
          let alert = {
            alert: true,
            alert_active: true,
            alert_message: "No se pudo obtener la lista de artículos",
            alert_color: "error",
          };
          this.$emit("chanceAlert", alert);
        });
      }
      
    },

    editItem(item) {
      this.editedIndex = this.articles.indexOf(item);
      this.editedItem = Object.assign({}, item);
      this.getComments(item.articleId);
      this.dialog = true;
    },

    close() {
      this.dialog = false;
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem);
        this.editedComment = Object.assign({}, this.defaultComment);
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

    save(item) {
      this.loading = true;
      let json = {
        comment: item.comment,
        articleId: this.editedItem.articleId,
      };
      axios
        .post("comment/", json)
        .then((response) => {
          if (response.status == 200) {
            this.loading = false;
            let alert = {
              alert: true,
              alert_active: true,
              alert_message: "Comentario creado correctamente.",
              alert_color: "success",
            };
            this.$emit("chanceAlert", alert);
            this.close();
          }
        })
        .catch((error) => {
          let alert = {
            alert: true,
            alert_active: true,
            alert_message:
              "Algo salió mal durante la creación del comentario. Por favor, vuelve a intentarlo más tarde.",
            alert_color: "error",
          };
          this.$emit("chanceAlert", alert);
          this.close();
        });
      this.comments(this.editedItem.articleId);
    },
  },
};
</script>
<style>
h2.section-title {
  font-size: 25px !important;
  color: #212121 !important;
  font-family: "Montserrat" !important;
  font-weight: 600 !important;
  text-transform: uppercase !important;
}
h3.item-title {
  color: #212121 !important;
  font-family: "Montserrat" !important;
  font-weight: bold !important;
}

p.description {
  font-family: "Montserrat" !important;
  font-size: 18px !important;
  color: #212121 !important;
}
p.text {
  font-family: "Montserrat" !important;
  font-size: 13px !important;
  color: #212121 !important;
}
</style>
