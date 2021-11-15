<template>
  <v-container fluid>
    <v-layout align-space-around row fill-height wrap>
      <v-img src="@/assets/login.jpg" alt="" height="93vh">
        <v-sheet height="93vh" color="rgba(0, 0, 0, 0.32)">
          <v-container fill-height fluid>
            <v-layout align-center justify-center>
              <v-card class="elevation-12" min-width="40vh">
                <v-toolbar color="grey darken-4" dark>
                  <v-spacer />
                  <v-icon color="#F37154" large>mdi-lightning-bolt</v-icon>
                  <v-toolbar-title id="login_bar" class="intro-text"
                    >Radiance</v-toolbar-title
                  >
                  <v-spacer />
                </v-toolbar>
                <v-card-text>
                    <v-container>
                  <v-row justify="space-around" class="py-10">
                    <p id="login_title">Recuperación de contraseña</p>
                    
                  </v-row>
                  <v-row justify="center">
                  <p>Escribe el correo que registraste en tu cuenta. </p>
                  
                  </v-row>
                  <v-row justify="center">
                  <p>Enviaremos un correo a esta dirección con instrucciones a seguir.</p>
                  </v-row>
                  </v-container>
                  <v-form class="px-3">
                    <v-text-field
                      label="Correo electrónico"
                      placeholder="Correo electrónico"
                      name="mail"
                      prepend-inner-icon="mdi-at"
                      type="email"
                      solo
                      color="#F37154"
                      v-model="editedItem.mail"
                      class="py-3"
                    ></v-text-field>
                    <div>
                      <v-alert
                        v-model="alert"
                        :value="alert_active"
                        :color="alert_color"
                        :icon="
                          alert_color == 'success'
                            ? 'mdi-check-circle'
                            : 'mdi-alert-circle'
                        "
                        dense
                        text
                        dismissible
                        border="left"
                        transition="scroll-y-transition"
                      >
                        {{ alert_message }}</v-alert
                      >
                    </div>
                  </v-form>
                </v-card-text>
                <v-card-actions>
                  <v-spacer></v-spacer>
                  <v-row align="center" justify="space-around" class="pb-12">
                    <v-col>
                      <v-btn
                        color="grey darken-4"
                        dark
                        x-large
                        :loading="loading"
                        @click="logIn"
                        >Enviar</v-btn
                      >
                    </v-col>
                  </v-row>
                </v-card-actions>
                <v-card-text>
                 
                    
                </v-card-text>
              </v-card>
            </v-layout>
          </v-container>
        </v-sheet>
      </v-img>
    </v-layout>
  </v-container>
</template>

<script>
import store from "../../store/index";
import { mapGetters } from "vuex";
import axios from "axios";

export default {
  name: "LoginRadiance",
  data: () => ({
    isAuth: "",
    alert: false,
    alert_active: false,
    alert_message: "",
    alert_color: "",
    alert_icon: "",
    loading: false,
    editedItem: {
      mail: "",
    },
  }),
  computed: {
    ...mapGetters({
      statusLogin: "loginError",
    }),
  },

  created() {
    this.initialize();
  },

  methods: {
    initialize() {
      store.dispatch("clearUserData");
    },

    logIn() {
      this.loading = true;
      let mail = this.editedItem.mail;

      axios
        .get("recoverpassword/", {params: {
            mail
        }})
        .then((response) => {
          if (response.status == 200) {
            this.setAlert(true, "success", "Correo enviado exitosamente.");
            this.alert = true;
            this.loading = false;
          }
        })
        .catch((error) => {
          this.setAlert(true, "error", "Error al enviar correo.");
          this.alert = true;
          this.loading = false;
        });
    },

    setAlert(active, color, message) {
      this.alert_active = active;
      this.alert_color = color;
      this.alert_message = message;
    },
  },
};
</script>

<style>
#login_bar {
  font-size: 30px !important;
  color: #ffffff;
  font-family: "Noto Serif" !important;
  font-weight: 600 !important;
}
#login_title {
  font-size: 24px !important;
  font-family: "Noto Serif" !important;
  font-weight: 600 !important;
  color: #212121;
}
</style>
