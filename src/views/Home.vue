<template>
    <div class="fill-height">
        <v-app-bar app clipped-left class="orange darken-2" dark>
            <v-app-bar-nav-icon @click="drawer = !drawer"></v-app-bar-nav-icon>
            <v-toolbar-title>Baza <strong>Motywów</strong></v-toolbar-title>
        </v-app-bar>

        <v-navigation-drawer app clipped v-model="drawer">
            <div class="d-flex flex-column fill-height">
                <v-list shaped class="list-scroll">
                    <v-subheader>Motywy</v-subheader>
                    <v-list-item-group v-model="selected">
                        <v-list-item v-for="item in motifs" :key="item._id" @click="hideDrawer">
                            <v-list-item-avatar>
                                <v-img :src="item.imageURL ? item.imageURL : defaultAvatar"></v-img>
                            </v-list-item-avatar>
                            <v-list-item-content>
                                <v-list-item-title v-text="item.title"></v-list-item-title>
                            </v-list-item-content>
                        </v-list-item>
                    </v-list-item-group>
                </v-list>
                <div class="text-center mt-auto py-3 br-top">
                    <v-btn
                        depressed
                        rounded
                        class="orange white--text"
                        @click="dialog = true"
                        max-width="100%"
                        small>
                        <v-icon left>create</v-icon>
                        <span>Zaproponuj nawiązanie</span>
                    </v-btn>
                </div>
            </div>
        </v-navigation-drawer>

        <v-content class="grey lighten-5 fill-height">
            <v-container fluid class="pa-5">
                <v-expansion-panels accordion v-if="typeof selected !== 'undefined'">
                    <v-expansion-panel v-for="item in motifs[selected].items" :key="item._id">
                        <v-expansion-panel-header>
                            {{ item.title + (item.year ? ` (${item.year})` : '') + (item.author ? ` - ${item.author}` : '')}}
                        </v-expansion-panel-header>
                        <v-expansion-panel-content>
                            <div class="d-flex flex-column">
                                <ul v-if="item.description.length > 1">
                                    <li class="py-3"
                                        v-for="paragraph in item.description"
                                        :key="paragraph"
                                        style="word-break: break-word;"
                                    >
                                        {{ paragraph }}
                                    </li>
                                </ul>
                                <span v-else-if="item.description.length" style="word-break: break-word;">
                                    {{ item.description[0] }}
                                </span>
                                <span class="caption mt-3 text-right grey--text text--darken-2">
                                    Typ: <strong>{{ item.type }}</strong>
                                    <span v-html="'&emsp;'"></span>
                                    Dodał(a): <strong>{{ item.nick }} ({{ new Date(item.dateOfCreation).toLocaleString().split(', ')[0] }})</strong>
                                </span>
                            </div>
                        </v-expansion-panel-content>
                    </v-expansion-panel>
                </v-expansion-panels>
                <div v-else>
                    <v-alert border="left" colored-border color="blue" elevation="2" icon="keyboard_backspace">
                        Wybierz motyw aby przeglądać nawiązania do utworów
                    </v-alert>
                    <v-card>
                        <v-container class="d-flex flex-column text-center">
                            <v-icon size="6em" color="blue">school</v-icon>
                            <p class="headline mt-5">
                                <strong class="blue--text"> Baza motywów</strong> jest zbiorem wątków z lektur, książek, filmów, seriali itp. pogrupowanych według motywów,
                                którego celem jest pomoc w tworzeniu prac wymagających odwołania do tekstów kultury. Dzięki tej bazie możesz szybko i łatwo powtórzyć
                                ważne wątki zarówno z utworów omawianych w szkole, jak i tych pozaszkolnych. <v-icon color="black">sentiment_satisfied</v-icon>
                            </p>
                            <p class="headline my-5">
                                W bazie znajduje się {{ itemCount }} w {{ motifCount }}.
                                Możesz pomóc rozwijać bazę dodając własne propozycje nawiązań do utworów za pomocą opcji
                                <v-btn color="orange" class="title" @click="dialog = true" text>
                                    Zaproponuj nawiązanie
                                </v-btn>
                            </p>
                        </v-container>
                    </v-card>
                </div>
            </v-container>
        </v-content>

        <v-dialog v-model="dialog" max-width="600" persistent>
            <v-card>
                <v-card-title class="headline d-flex flex-column align-start">
                    <span>Zaproponuj nawiązanie do utworu</span>
                    <span class="caption">* - pola obowiązkowe</span>    
                </v-card-title>
                <v-card-text>
                    <SuggestionForm ref="form"></SuggestionForm>
                </v-card-text>
                <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn color="red" text @click="closeDialog">Anuluj</v-btn>
                    <v-btn color="orange" dark depressed @click="submit" :loading="btnLoading">Wyślij</v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>

        <v-snackbar v-model="snackbar.show" :timeout="snackbar.timeout">{{ snackbar.message }}</v-snackbar>
    </div>
</template>

<script>
import SuggestionForm from '../components/SuggestionForm.vue'
import axios from 'axios'

export default {
    name: 'Home',
    components: {
        SuggestionForm 
    },
    data() {
        return {
            drawer: true,
            defaultAvatar: 'https://d1nz104zbf64va.cloudfront.net/dt/a/o/top-7-books-that-changed-the-world.jpg',
            motifs: [],
            dialog: false,
            selected: undefined,
            snackbar: {
                show: false,
                message: '',
                timeout: 6000
            },
            btnLoading: false,
        }
    },
    created() {
        axios.get(`${process.env.VUE_APP_API_URL}/motifs`).then((response) => {
            this.motifs = response.data;
        }).catch((err) => {
            this.showSnackbar(err.message);
        });
    },
    methods: {
        showSnackbar(text) {
            this.snackbar.message = text;
            this.snackbar.show = true;
        },
        submit() {
           if(this.$refs.form.submit()) {
               this.btnLoading = true;
               axios.post(`${process.env.VUE_APP_API_URL}/suggestions`, this.$refs.form.getData()).then(() => {
                   this.btnLoading = false;
                   this.$refs.form.reset();
                   this.dialog = false;
                   this.showSnackbar('Propozycja została zapisana i czeka na akceptację');
               }).catch((err) => {
                   this.btnLoading = false;
                   if(typeof err.response.data.message !== 'undefined') {
                       this.showSnackbar(err.response.data.message);
                   } else {
                       this.showSnackbar(`Wystąpił błąd: ${err.message}`);
                   }
               })
           }
        },
        closeDialog() {
            this.$refs.form.reset();
            this.dialog = false;
        },
        hideDrawer() {
            if(window.innerWidth < 1264) {
                this.drawer = false;
            }
        }
    },
    computed: {
        itemCount() {
            let counter = 0;
            for (let item of this.motifs) {
                counter += item.items.length;
            }
            if([2, 3, 4].includes(counter % 10) && Math.floor(counter / 10) % 10 !== 1) {
                counter = counter.toString()+' nazwiązania pogrupowane';
            } else if(counter === 1) {
                counter = counter.toString()+' nawiązanie pogrupowane';
            } else {
                counter = counter.toString()+' nawiązań pogrupowanych';
            }
            return counter;
        },
        motifCount() {
            if([2, 3, 4].includes(this.motifs.length % 10) && Math.floor(this.motifs.length / 10) % 10 !== 1) {
                return this.motifs.length.toString()+' motywy';
            } else if(this.motifs.length === 1) {
                return '1 motyw';
            } else {
                return this.motifs.length.toString()+' motywów';
            }
        }
    }
}
</script>
