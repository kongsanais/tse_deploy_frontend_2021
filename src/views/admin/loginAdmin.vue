<template>
  <v-container>
    <v-row class="justify-center">
      <v-card style="width:500px;" class="mt-8">
          <v-card-title primary-title>
            Login for Admin
          </v-card-title>
           <v-card class="ma-1">
           <v-img
            max-height="300px"
            lazy-src="@/assets/login-bg.jpg"
            src="@/assets/login-bg.jpg"
          ></v-img>
           </v-card>
        <v-card-text>
          <v-form
            @submit.prevent="submit"
            ref="form"
            v-model="valid"
            lazy-validation
          >
            <!-- Username -->
            <v-text-field
              name="email"
              label="Email"
              id="email"
              v-model="account.email"
              :rules="[
                (v1) => !!v1 || 'Email is required',
                (v2) =>
                  !!/^(([^<>()[\]\\.,;:\s@\&quot;]+(\.[^<>()[\]\\.,;:\s@\&quot;]+)*)|(\&quot;.+\&quot;))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/.test(
                    v2
                  ) || 'Please Enter Email',
              ]"
            />

            <!-- Password -->
            <v-text-field
              name="password"
              label="Password"
              id="password"
              v-model="account.password"
              :append-icon="isShowPassword ? 'visibility' : 'visibility_off'"
              @click:append="isShowPassword = !isShowPassword"
              :type="isShowPassword ? 'text' : 'password'"
              :rules="[
                (v1) => !!v1 || 'Password is required']"
              counter
            />

            <v-row class="justify-space-between px-3 pt-4">
              <v-btn type="submit" color="success" class="mt-2">Login</v-btn>
            </v-row>
          </v-form>
        </v-card-text>
      </v-card>
    </v-row>

    <v-dialog v-model="dialog_messenger.status" max-width="480">
      <v-card>
        <v-card-title class="headline grey lighten-2">
          {{ dialog_messenger.title }}
          <v-icon color="warning" class="mdi mdi-36px ml-2">
            mdi-alert-circle-outline
          </v-icon>
        </v-card-title>

        <v-card-text class="mt-3 pd-0">
          <h2 class="mb-3">{{ dialog_messenger.text }}</h2>
          <h3>
            <p><span v-html="dialog_messenger.sub_text"></span></p>
          </h3>
        </v-card-text>

        <v-card-actions>
          <v-spacer></v-spacer>

          <v-btn
            class="primary"
            light
            text
            @click="dialog_messenger.status = false"
            
          >
            Agree
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>


    <v-dialog
      v-model="dialog_load.status"
      hide-overlay
      persistent
      width="300"
    >
      <v-card
        color="primary"
        dark
      >
        <v-card-text>
           Please Wait
          <v-progress-linear
            indeterminate
            color="white"
            class="mb-0"
          ></v-progress-linear>
        </v-card-text>
      </v-card>
    </v-dialog>

  </v-container>
</template>

<script>
import api from "@/services/api";

export default {
  mounted() {
    if (api.isLoggedIn() && (api.getRole() == "Admin") ) {
      this.$router.push("/admin_home");
    }
  },
  data() {
    return {
      dialog_messenger: {
        status: false,
        title: "Message",
        text: "",
        sub_text: "",
      },
      isShowPassword: false,
      valid: true,
      account: {
        email: "",
        password: "",
      },
    dialog_load: {
      status: false,
      title: "Message",
      text: "",
      sub_text: "",
      router: "",
    },
    };
  },
  methods: {
    async submit() {
      var check = this.$refs.form.validate();

      if (check == true) 
      {
        this.dialog_load.status = true;
        let value = await this.$store.dispatch({
          type: "doLoginAdmin",
          email: this.account.email,
          password: this.account.password,
        });
        if(value == true){
          this.dialog_load.status = false;
        }else{
          this.dialog_load.status = false;
            this.dialog_messenger.text = "Email or Password Incorrect";
            this.dialog_messenger.sub_text = "";
            this.dialog_messenger.status = true;
        }

          // if(this.$store.state.isLogged){
          //   //////c
          // }else{
          //   this.dialog_messenger.text = "Email or Password Incorrect";
          //   this.dialog_messenger.sub_text = "";
          //   this.dialog_messenger.status = true;
          // }

      } else {
            this.dialog_messenger.text = "Please Check Email And password";
            this.dialog_messenger.sub_text = "";
            this.dialog_messenger.status = true;
      }


    },
  },
};
</script>

<style></style>
