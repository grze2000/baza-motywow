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
                <v-icon @click="$refs.component.refresh()">refresh</v-icon>
            </v-btn>
            <v-btn icon @click="logout">
                <v-icon>power_settings_new</v-icon>
            </v-btn>
        </v-app-bar>
        <v-navigation-drawer v-model="drawer" app clipped>
            <v-list shaped>
                <v-subheader>Panel Administratora</v-subheader>
                    <v-list-item-group v-model="component">
                        <v-list-item value="Suggestions">
                            <v-list-item-content>
                                Oczekujące na potwierdzenie
                            </v-list-item-content>
                            <v-list-item-action>
                                <v-badge
                                    bottom
                                    color="orange"
                                    :content="suggestionCount"
                                    v-if="suggestionCount"></v-badge>
                            </v-list-item-action>
                        </v-list-item>
                        <v-list-item value="Motifs">
                            <v-list-item-content>Motywy</v-list-item-content>
                        </v-list-item>
                        <v-list-item value="References">
                            <v-list-item-content>Nawiązania</v-list-item-content>
                        </v-list-item>
                    </v-list-item-group>
                    <v-list-item link to="/" target="_blank">
                        <v-list-item-title>
                            Baza motywów
                        </v-list-item-title>
                    </v-list-item>
            </v-list>
        </v-navigation-drawer>
        <v-content class="grey lighten-5 fill-height">
            <v-container fluid>
                <component
                    :is="component"
                    @showSnackbar="showSnackbar"
                    @updateSuggestionCount="updateSuggestionCount"
                    ref="component"
                ></component>
            </v-container>
        </v-content>
        <v-snackbar v-model="snackbar.show">{{ snackbar.message }}</v-snackbar>
    </div>
</template>

<script>
import axios from 'axios'
import Suggestions from '../components/Suggestions.vue'
import Motifs from '../components/Motifs.vue'
import References from '../components/References.vue'

export default {
    name: 'Admin',
    components: {
        Suggestions,
        Motifs,
        References
    },
    data() {
        return {
            drawer: true,
            snackbar: {
                show: false,
                message: ''
            },
            component: 'Suggestions',
            suggestionCount: 0
        };
    },
    created() {
        const token = localStorage.getItem('token');
        if(!token) {
            this.$router.push('/login');
        }
        axios.defaults.headers.common['Authorization'] = token;
    },
    methods: {
        logout() {
            localStorage.removeItem('token');
            this.$router.push('/login');
        },
        showSnackbar(message) {
            this.snackbar.message = message;
            this.snackbar.show = true;
        },
        updateSuggestionCount(count) {
            this.suggestionCount = count;
        }
    }
}
</script>