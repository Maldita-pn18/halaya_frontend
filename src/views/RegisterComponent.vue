<template>
  <v-form v-model="valid">
    <v-app id="inspire">
      <v-main>
        <v-container class="fill-height" fluid>
          <v-row align="center" justify="center">
            <v-col cols="12" sm="8" md="4">
              <v-card class="elevation-12">
                <v-list-item id="cardHeader" size="150">
                  <v-list-item-content>
                    <v-list-item-title
                      class="d-flex align-center justify-center pa-6 mx-auto headline"
                    >Sign Up</v-list-item-title>
                  </v-list-item-content>
                </v-list-item>
                <v-list-item class="d-flex align-center justify-center pa-6 mx-auto">
                  <img alt="wawen's ube halaya" src="../assets/wawens.png" height="154">
                </v-list-item>

                <v-spacer></v-spacer>

                <v-card-text>
                  <v-form autocomplete="off">
                    <v-text-field
                      ref="username"
                      label="Username"
                      name="Username"
                      prepend-icon="mdi-account"
                      type="text"
                      v-model="username"
                      :rules="nameRules"
                      class="border-design"
                      outlined
                      dense
                    ></v-text-field>
                    <v-snackbar v-model="isValid" color="deep-purple accent-4" elevation="24">
                      Username already exists
                      <template v-slot:action="{ attrs }">
                        <v-btn color="pink" text v-bind="attrs" @click="snackbar = false">Close</v-btn>
                      </template>
                    </v-snackbar>

                    <v-text-field
                      id="contact"
                      label="Phone"
                      name="contact"
                      prepend-icon="mdi-phone"
                      type="text"
                      v-model="contact"
                      :rules="contactRules"
                      class="border-design"
                      outlined
                      dense
                    ></v-text-field>
                    <v-text-field
                      autocomplete="current-password"
                      :value="userPassword"
                      prepend-icon="mdi-lock"
                      label="Enter password"
                      :append-icon="value ? 'mdi-eye-off' : 'mdi-eye'"
                      @click:append="() => (value = !value)"
                      :type="value ? 'password' : 'text'"
                      :rules="[rules.password]"
                      @input="_=>userPassword=_"
                      outlined
                      dense
                    ></v-text-field>
                    <v-text-field
                      id="password"
                      label="Confirm Password"
                      :append-icon="valuePass ? 'mdi-eye-off' : 'mdi-eye'"
                      @click:append="() => (valuePass = !valuePass)"
                      :type="valuePass ? 'password' : 'text'"
                      name="password"
                      prepend-icon="mdi-lock"
                      v-model="cPassword"
                      :rules="[passwordConfirmationRule]"
                      class="border-design"
                      outlined
                      dense
                    ></v-text-field>
                  </v-form>
                </v-card-text>
                <center>
                  <v-btn id="btnLogin" class="mb-5" block outlined rounded @click="signUp">Sign Up</v-btn>
                </center>
                <div align="center" justify="center">
                  <span>
                    Already have an account?
                    <a href="/login">Sign in</a>
                    <br>
                    <br>
                  </span>
                </div>
                <v-progress-linear
                  color="deep-purple accent-4"
                  v-show="loading"
                  indeterminate
                  rounded
                  height="6"
                ></v-progress-linear>
              </v-card>
            </v-col>
          </v-row>
        </v-container>
      </v-main>
    </v-app>
  </v-form>
</template>
<style scoped>
#btnLogin,
.border-design {
  border-color: purple !important;
  border-width: 2px !important;
  color: purple !important;
}
.v-btn {
  width: 85% !important;
  min-width: 85% !important;
  margin-left: 6%;
}
#cardHeader {
  background-color: purple;
  color: white !important;
}
</style>
<script>
import axios from "axios";
export default {
  data: () => ({
    loading: false,
    valid: false,
    username: "",
    isValid: false,
    showPassword: false,
    nameRules: [
      v => !!v || "Username is required",
      v => v.length <= 10 || "Username must be less than 10 characters"
    ],
    contact: "",
    contactRules: [
      v => !!v || "Phone number is required",
      v => /^(09|\+639)\d{9}$/.test(v) || "Input valid phone number"
    ],
    userPassword: "",
    valid: true,
    value: true,
    rules: {
      required: value => !!value || " Password is required.",
      password: value => {
        const pattern = /^(?=.*[a-z])(?=.*[A-Z])(?=.*[0-9])(?=.*[!@#_\$%\^&\*])(?=.{8,})/;
        return (
          pattern.test(value) ||
          "Min. 8 characters with at least one capital letter, a number and a special character."
        );
      }
    },
    cPassword: "",
    valuePass: true
  }),
  computed: {
    passwordConfirmationRule() {
      return () =>
        this.userPassword === this.cPassword || "Password does not match";
    }
  },

  mounted() {},
  methods: {
    signUp() {
      this.loading = true;
      let Reg = {
        uName: this.username,
        phone: this.contact,
        pass: this.userPassword,
        cPass: this.cPassword,
        role: "customer"
      };
      axios.post(this.url + "/api/register", Reg).then(response => {
        // console.log("Successfully Registered: ", response.data.message.message);
        if (response.data.message.message == "success") {
          localStorage.setItem("token", response.data.token);
          localStorage.setItem("id", response.data.user.id);
          localStorage.setItem("role", response.data.user.role);
          this.loading = false;
          this.$router.push("/customerHome");
        } else if (response.data.message === "invalid_username") {
          this.isValid = true;
          console.log("invali");
        }
      });
    }
  }
};
</script>

<style>
</style>
