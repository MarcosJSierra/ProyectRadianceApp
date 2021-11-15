<template>
  <v-container fluid>
    <v-layout align-center row fill-height wrap>
      <v-img height="100%" src="@/assets/register.jpg" alt="">
        <v-sheet height="100%" width="100%" min-width="50vh" color="rgba(0, 0, 0, 0.32)">
          <v-container fluid>
            <v-layout align-center justify-center wrap>
              <v-card elevation="12" min-width="40vh" class="pa-3 ma-3" width="120vh">
                <h1 class="section-title text-center">Perfil de Usuario</h1>
                <v-card-text>
                  <v-form>
                    <h3 class="format">Datos personales</h3>
                    <v-text-field
                      label="Nombres y Apellidos"
                      placeholder="Nombres y Apellidos"
                      name="name"
                      prepend-inner-icon="mdi-account"
                      type="text"
                      solo
                      color="#F37154"
                      v-model="editedItem.name"
                    >
                    </v-text-field>
                    <v-text-field
                      label="Correo electrónico"
                      placeholder="Correo electrónico"
                      name="email"
                      prepend-inner-icon="mdi-at"
                      type="email"
                      solo
                      color="#F37154"
                      v-model="editedItem.mail"
                    >
                    </v-text-field>
                    <v-text-field
                      label="Teléfono"
                      placeholder="Teléfono"
                      name="phone"
                      prepend-inner-icon="mdi-phone"
                      type="number"
                      color="#F37154"
                      solo
                      v-model="editedItem.phoneNumber"
                    >
                    </v-text-field>
                  </v-form>
                </v-card-text>
                <v-card-actions>
                  <v-spacer></v-spacer>
                  <v-row align="center" justify="space-around" class="pb-12">
                    <v-col>
                      <v-btn
                        @click="registerUser(editedItem)"
                        color="grey darken-4"
                        dark
                        x-large
                        :loading="loading"
                        >Actualizar</v-btn
                      >
                    </v-col>
                  </v-row>
                </v-card-actions>
                <v-card-text>
                  <h3 class="format">Otros datos</h3>
                  <v-container>
                        <v-simple-table>
                          <template v-slot:default>
                            <thead>
                              <tr>
                                <th class="text-center">Propiedad</th>
                                <th class="text-center">Valor</th>
                              </tr>
                            </thead>
                            <tbody>
                              <tr>
                                <td class="text-center">{{ "Usuario" }}</td>
                                <td class="text-center">{{ editedItem.user }}</td>
                              </tr>
                              <tr>
                                <td class="text-center">{{ "Rol Actual" }}</td>
                                <td class="text-center">{{ editedItem.role }}</td>
                              </tr>
                            </tbody>
                          </template>
                        </v-simple-table>
                      </v-container>
                      <h3 class="format">Detalles de suscripción</h3>
                      <v-container>
                        <v-simple-table>
                          <template v-slot:default>
                            <tbody>
                              <tr>
                                <td class="text-center">{{ "Fecha de finalización" }}</td>
                                <td class="text-center">{{ editedItem.subscription.finalizationDate }}</td>
                              </tr>
                              <tr>
                                <td class="text-center">{{ "Suscripción actual" }}</td>
                                <td class="text-center">
                                  {{ editedItem.subscription.subscriptionType.name }}
                                </td>
                              </tr>
                              <tr>
                                <td class="text-center">{{ "Precio de suscripción" }}</td>
                                <td class="text-center">
                                  {{ "$"
                                  }}{{ editedItem.subscription.subscriptionType.price }}
                                </td>
                              </tr>
                            </tbody>
                          </template>
                        </v-simple-table>
                      </v-container>
                </v-card-text>
              </v-card>
              <v-card elevation="12" min-width="40vh" class="pa-3 ma-3" width="120vh">
                <h1 class="section-title text-center">Cambio de contraseña</h1>
                <v-card-text>
                  <v-form>
                    <h3 class="format">Contraseña actual</h3>
                    <v-text-field
                      id="password"
                      label="Contraseña"
                      placeholder="Contraseña"
                      name="password"
                      prepend-inner-icon="mdi-lock"
                      type="password"
                      solo
                      color="#F37154"
                      v-model="recover.oldPassword"
                    ></v-text-field>
                    <h3 class="format">Nueva contraseña</h3>
                    <v-text-field
                      label="Contraseña"
                      placeholder="Contraseña"
                      name="password"
                      prepend-inner-icon="mdi-lock"
                      :type="showPassword ? 'text' : 'password'"
                      solo
                      color="#F37154"
                      v-model="recover.newPassword"
                      :append-icon="showPassword ? 'mdi-eye' : 'mdi-eye-off'"
                      @click:append="showPassword = !showPassword"
                    ></v-text-field>
                  </v-form>
                </v-card-text>
                <v-card-actions>
                  <v-spacer></v-spacer>
                  <v-row align="center" justify="space-around" class="pb-12">
                    <v-col>
                      <v-btn
                        @click="changePassword(recover)"
                        color="grey darken-4"
                        dark
                        x-large
                        :loading="loading"
                        >Cambiar contraseña</v-btn
                      >
                    </v-col>
                  </v-row>
                </v-card-actions>
                <div v-if="newRegister" class="pt-12 pb-6">
                  <v-row align="center" justify="center">
                    <v-btn color="grey darken-4" dark x-large to="/login">Login</v-btn>
                  </v-row>
                </div>
              </v-card>
              <v-card elevation="12" min-width="40vh" class="pa-3 ma-3" width="120vh">
                <h1 class="section-title text-center">Historial de Pagos</h1>
                <v-container>
                  <v-simple-table>
                    <template v-slot:default>
                      <thead>
                        <tr>
                          <th class="text-center">Monto</th>
                          <th class="text-center">Fecha</th>
                        </tr>
                      </thead>
                      <tbody>
                        <tr v-for="(item, index) in historyItem" :key="index">
                          <td class="text-center">{{ item.amount }}</td>
                          <td class="text-center">{{ item.date }}</td>
                        </tr>
                      </tbody>
                    </template>
                  </v-simple-table>
                </v-container>
              </v-card>
            </v-layout>
          </v-container>
        </v-sheet>
      </v-img>
    </v-layout>
  </v-container>
</template>

<script>
import axios from "axios";
import store from "../../store/index";

export default {
  name: "Profile",
  data: () => ({
    username: store.getters["username"],
    editedItem: {
      userId: "",
      name: "",
      mail: "",
      phoneNumber: "",
      role: "",
      image: "",
      user: "",
      password: "",
      isVerified: "",
      subscription: {
        finalizationDate: "",
        status: "",
        subscriptionType: {
          subscriptionTypeId: "",
          description: "",
          name: "",
          price: "",
        },
      },
    },
    recover: {
      oldPassword: "",
      newPassword: "",
    },
    historyItem: {},
    defaultHisitoryItem: null,
    showPassword: false,
    loading: false,
    suscriptionTypes: [],
    alert: false,
    alert_active: false,
    alert_message: "",
    alert_color: "",
    alert_icon: "",
    newRegister: false,
  }),

  mounted: function () {
    if (alert) {
      this.hideAlert();
    }
  },
  created() {
    this.initialize();
  },

  methods: {
    hideAlert() {
      window.setInterval(() => {
        this.alert = false;
      }, 6000);
    },

    initialize() {
      this.loading = true;
      axios
        .get("user/" + this.username)
        .then((response) => {
          if (response.status == 200) {
            this.editedItem = response.data;
            axios
              .get("payment/" + this.editedItem.userId)
              .then((res) => {
                if (res.status == 200) {
                  this.historyItem = res.data;
                }
              })
              .catch((error) => {
                let alert = {
                  alert: true,
                  alert_active: true,
                  alert_message:
                    "Hubo un error al obtener el historial de pagos. Intenta de nuevo más tarde.",
                  alert_color: "error",
                };
                this.$emit("chanceAlert", alert);
              });
          }
        })
        .catch((error) => {
          let alert = {
            alert: true,
            alert_active: true,
            alert_message: "Hubo un error al obtener los datos del usuario.",
            alert_color: "error",
          };
          this.$emit("chanceAlert", alert);
        });

      this.loading = false;
    },

    registerUser() {
      console.log(this.editedItem.subscription.subscriptionType);
      this.loading = true;
      let json = {
        userId: this.editedItem.userId,
        name: this.editedItem.name,
        mail: this.editedItem.mail,
        phoneNumber: this.editedItem.phoneNumber,
        role: this.editedItem.role,
        image: this.editedItem.image,
        user: this.editedItem.user,
        password: this.editedItem.password,
        isVerified: this.editedItem.isVerified,
        subscription: {
          finalizationDate: this.editedItem.subscription.finalizationDate,
          status: this.editedItem.subscription.status,
          subscriptionType: {
            subscriptionTypeId: this.editedItem.subscription.subscriptionType.subscriptionTypeId,
            description: this.editedItem.subscription.subscriptionType.description,
            name: this.editedItem.subscription.subscriptionType.name,
            price: this.editedItem.subscription.subscriptionType.price,
          }
        },
      };
      axios
        .put("user/" + this.editedItem.userId, json)
        .then((response) => {
          if (response.status == 200) {
            this.loading = false;
            this.newRegister = true;
            let alert = {
              alert: true,
              alert_active: true,
              alert_message: "Tu cuenta ha sido actualizada éxitosamente.",
              alert_color: "success",
            };
            this.$emit("chanceAlert", alert);
          }
        })
        .catch((error) => {
          let alert = {
              alert: true,
              alert_active: true,
              alert_message: "Algo salió mal durante la actualización de tu cuenta. Por favor, vuelve a intentarlo más tarde.",
              alert_color: "error",
            };
            this.$emit("chanceAlert", alert);
        });
      this.alert = true;
      this.loading = false;
    },
    changePassword(recover) {
      let json = {
        password: recover.oldPassword,
        newPassword: recover.newPassword,
      };
      axios
        .put("changepassword/", json)
        .then((response) => {
          if (response.status == 200) {
            this.loading = false;
            let alert = {
              alert: true,
              alert_active: true,
              alert_message: "Tu contraseña ha sido actualizada éxitosamente.",
              alert_color: "success",
            };
            this.$emit("chanceAlert", alert);
          }
        })
        .catch((error) => {
          let alert = {
            alert: true,
            alert_active: true,
            alert_message:
              "Hubo un error al actualizar tu contraseña. Por favor, intenta más tarde.",
            alert_color: "error",
          };
          this.$emit("chanceAlert", alert);
        });
      this.recover.oldPassword = "";
      this.recover.newPassword = "";
      this.alert = true;
      this.loading = false;
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
h1.section-title {
  font-size: 35px !important;
  color: #212121;
  font-family: "Noto Serif" !important;
  font-weight: 600 !important;
}
p.description {
  font-family: "Montserrat" !important;
  font-size: 18px !important;
  margin: 32px 0px !important;
  color: #212121 !important;
}
h3.format {
  font-family: "Montserrat" !important;
  font-size: 18px !important;
  margin: 16px 0px !important;
  color: #8a8a8a !important;
  font-weight: 500 !important;
}
</style>
