<template>
        <v-container fluid class="fill-height grey lighten-5 background">
            <v-row align="center" justify="center">
                <v-col cols="12" md="4">
                    <v-card class="" raised>
                        <v-toolbar flat dark color="orange darken-2">
                            <v-toolbar-title>Logowanie</v-toolbar-title>
                        </v-toolbar>
                        <v-card-text @keyup.enter="submit">
                            <v-form ref="loginForm" v-model="valid">
                                <v-text-field
                                    label="Nazwa użytkownika"
                                    prepend-icon="person"
                                    required
                                    :rules="[v=> !!v || 'To pole jest wymagane']"
                                    v-model="data.username"
                                ></v-text-field>
                                <v-text-field
                                    type="password"
                                    label="Hasło"
                                    prepend-icon="lock"
                                    required
                                    :rules="[v=> !!v || 'To pole jest wymagane']"
                                    v-model="data.password"
                                ></v-text-field>
                            </v-form>
                        </v-card-text>
                        <v-card-actions>
                            <v-spacer/>
                            <v-btn color="orange darken-2" dark @click="submit" :loading="loading">Zaloguj się</v-btn>
                        </v-card-actions>
                    </v-card>
                </v-col>
            </v-row>
            <v-snackbar v-model="snackbar.show" :timeout="snackbar.timeout">{{ snackbar.message }}</v-snackbar>
        </v-container>
</template>

<script>
import axios from 'axios'

export default {
    name: 'LoginView',
    data() {
        return {
            valid: false,
            data: {
                username: '',
                password: ''
            },
            snackbar: {
                show: false,
                message: '',
                timeout: 6000
            },
            loading: false
        };
    },
    methods: {
        submit() {
            this.$refs.loginForm.validate();
            if(this.valid) {
                this.loading = true;
                axios.post(`${process.env.VUE_APP_API_URL}/login`, this.data).then((response) => {
                    localStorage.setItem('token', response.data.token);
                    this.$router.push('/admin');
                }).catch((err) => {
                    this.snackbar.message = err.response.data.message;
                    this.snackbar.show = true;
                }).finally(() => {
                    this.loading = false;
                });
            }
        }
    }
}
</script>

<style scoped>
.background {
    position: relative;
}
.background:before {
    display: block;
    content:"";
    width: 100%;
    height: 100%;
    position: absolute;
    top: 0;
    left: 0;
    background-image: url('../../assets/background.jpg');
    background-size: cover;
    background-position: center;
    filter: blur(3px);
}
</style>