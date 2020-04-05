<template>
    <div class="fill-height">
        <v-app-bar app clipped-left color="orange darken-2" dark>
            <v-app-bar-nav-icon @click="drawer = !drawer"></v-app-bar-nav-icon>
            <v-toolbar-title>
                Baza <strong>Motywów</strong>
                <span class="body-2 ml-2">
                    Panel Administratora
                </span>
            </v-toolbar-title>
            <v-spacer></v-spacer>
            <v-btn icon>
                <v-icon @click="refresh">refresh</v-icon>
            </v-btn>
            <v-btn icon>
                <v-icon>power_settings_new</v-icon>
            </v-btn>
        </v-app-bar>
        <v-navigation-drawer v-model="drawer" app clipped>
            <v-list shaped>
                <v-subheader>Panel Administratora</v-subheader>
                    <v-list-item @click="selected = undefined">
                        <v-list-item-content>
                            Oczekujące na potwierdzenie
                        </v-list-item-content>
                        <v-list-item-action>
                            <v-badge color="orange" :content="suggestions.length" bottom v-if="suggestions.length"></v-badge>
                        </v-list-item-action>
                    </v-list-item>
                    <v-list-item link to="/" target="_blank">
                        <v-list-item-title>
                            Baza motywów
                        </v-list-item-title>
                    </v-list-item>
            </v-list>
        </v-navigation-drawer>
        <v-content class="grey lighten-5 fill-height">
            <v-container fluid>
                <v-card v-if="typeof selected === 'undefined'">
                    <v-list v-if="suggestions.length">
                        <v-list-item-group v-model="selected">
                            <v-list-item v-for="item in suggestions" :key="item._id">
                                {{ item.reference.title + (item.reference.year ? ` (${item.reference.year})` : '') }}
                                <v-spacer></v-spacer>
                                Motyw: <strong class="pl-1">{{ item.motif }}</strong>
                            </v-list-item>
                        </v-list-item-group>
                    </v-list>
                    <v-container v-else class="text-center">Brak oczekujących propozycji</v-container>
                </v-card>
                <v-card v-else class="pa-5">
                    <div class="d-flex align-center">
                        <v-btn dark fab color="orange" small @click="selected = undefined">
                            <v-icon>arrow_back</v-icon>
                        </v-btn>
                        <v-spacer></v-spacer>
                            Utworzono: {{ new Date(suggestions[selected].reference.dateOfCreation).toLocaleString() }}
                        <v-spacer></v-spacer>
                        <v-btn color="error" small @click="discard">
                            <v-icon left>close</v-icon>
                            Odrzuć
                        </v-btn>
                        <v-btn color="success" class="ml-3" small @click="approve">
                            <v-icon left>check</v-icon>
                            Potwierdź
                        </v-btn>
                    </div>
                    <Form ref="form" :default="suggestions[selected]"></Form>
                </v-card>
            </v-container>
        </v-content>
        <v-snackbar v-model="snackbar.show">{{ snackbar.message }}</v-snackbar>
    </div>
</template>

<script>
import axios from 'axios'
import Form from '../components/Form.vue'

export default {
    name: 'Admin',
    components: {
        Form
    },
    data() {
        return {
            drawer: true,
            suggestions: [],
            selected: undefined,
            snackbar: {
                show: false,
                message: ''
            }
        };
    },
    created() {
      this.refresh();    },
    methods: {
        refresh() {
            axios.get(`${process.env.VUE_APP_API_URL}/suggestions`).then((response) => {
                this.suggestions = response.data;
            });
        },
        discard() {
            axios.delete(`${process.env.VUE_APP_API_URL}/suggestions/${this.suggestions[this.selected]._id}`).then(() => {
                this.snackbar.message = 'Propozycja została odrzucona!';
                this.snackbar.show = true;
                this.selected = undefined;
                this.refresh();
            }).catch((err) => {
                this.snackbar.message = `Błąd: ${err}`;
                this.snackbar.show = true;
            });
        },
        approve() {
            axios.post(`${process.env.VUE_APP_API_URL}/suggestions/${this.suggestions[this.selected]._id}`, this.$refs.form.suggestion).then(() => {
                this.snackbar.message = 'Propozycja została zatwierdzona!';
                this.snackbar.show = true;
                this.selected = undefined;
                this.refresh();
            }).catch((err) => {
                this.snackbar.message = `Błąd: ${err}`;
                this.snackbar.show = true;
            });
        }
    }
}
</script>