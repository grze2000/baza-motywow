<template>
    <v-app>
        <v-app-bar app clipped-left class="orange darken-2" dark>
            <div class="d-flex align-center">
                <v-app-bar-nav-icon @click="drawer = !drawer"></v-app-bar-nav-icon>
                <v-toolbar-title class="ml-5">Baza <strong>Motywów</strong></v-toolbar-title>
            </div>
        </v-app-bar>

        <v-navigation-drawer app clipped v-model="drawer">
            <div class="d-flex flex-column" style="height: 100%">
                <v-list shaped>
                    <v-subheader>Motywy</v-subheader>
                    <v-list-item-group v-model="selected">
                        <v-list-item v-for="item in motifs" :key="item.id">
                            <v-list-item-avatar>
                                <v-img :src="item.avatar ? item.avatar : defaultAvatar"></v-img>
                            </v-list-item-avatar>
                            <v-list-item-content>
                                <v-list-item-title v-text="item.title"></v-list-item-title>
                            </v-list-item-content>
                        </v-list-item>
                    </v-list-item-group>
                </v-list>
                <div class="text-center mt-auto py-3">
                    <v-btn depressed rounded class="orange white--text" @click="dialog = true" max-width="100%" small>
                        <v-icon left>create</v-icon>
                        <span>Zaproponuj nawiązanie</span>
                    </v-btn>
                </div>
            </div>
        </v-navigation-drawer>

        <v-content class="grey lighten-5">
            <v-container fluid class="pa-5">
                <v-expansion-panels accordion v-if="typeof selected !== 'undefined'">
                    <v-expansion-panel v-for="item in motifs[selected].items" :key="item.title">
                        <v-expansion-panel-header>{{ item.title + (item.year ? ` (${item.year})` : '') + (item.author ? ` - ${item.author}` : '')}}</v-expansion-panel-header>
                        <v-expansion-panel-content>
                            <div class="d-flex flex-column">
                                <ul v-if="Array.isArray(item.description)">
                                    <li class="py-3" v-for="paragraph in item.description" :key="paragraph">
                                        {{ paragraph }}
                                    </li>
                                </ul>
                                <span v-else>
                                    {{ item.description }}
                                </span>
                                <span class="caption mt-3 text-right grey--text text--darken-2">
                                    Typ: <strong>{{ item.type }}</strong>
                                    <span v-html="'&emsp;'"></span>
                                    Dodał(a): <strong>{{ item.nick }} ({{ item.createdAt }})</strong>
                                </span>
                            </div>
                        </v-expansion-panel-content>
                    </v-expansion-panel>
                </v-expansion-panels>
            </v-container>
        </v-content>

        <v-dialog v-model="dialog" max-width="600">
            <v-card>
                <v-card-title class="headline d-flex flex-column align-start">
                    <span>Zaproponuj nawiązanie do utworu</span>
                    <span class="caption">* - pola obowiązkowe</span>    
                </v-card-title>
                <v-card-text>
                    <v-form v-model="valid" ref="addNewForm">
                        <v-container>
                        <v-row>
                            <v-col cols="12">
                                <v-text-field
                                    label="Nazwa*"
                                    hint="Podaj nazwę utworu"
                                    v-model="form.name"
                                    :rules="[v => !!v || 'To pole jest wymagane']"
                                    required
                                ></v-text-field>
                            </v-col>
                            <v-col cols="12" md="6">
                                <v-text-field
                                    label="Autor"
                                    hint="Podaj autora. W przypadku filmów i seriali podaj reżysera/reżyserów"
                                    v-model="form.author"
                                ></v-text-field>
                            </v-col>
                            <v-col cols="12" md="6">
                                <v-text-field
                                    label="Rok wydania"
                                    v-model="form.year"
                                    :rules="[v => ((/^\d{4}$/.test(v) && parseInt(v) <= new Date().getFullYear()) || v === '') || 'Podaj prawidłowy rok']"
                                ></v-text-field>
                            </v-col>
                            <v-col cols="12" md="6">
                                <v-combobox
                                    label="Typ utworu*"
                                    :items="comboboxItems"
                                    v-model="form.type"
                                    :rules="[v => !!v || 'Wybierz typ utworu']"
                                    required
                                ></v-combobox>
                            </v-col>
                            <v-col cols="12" md="6">
                                <v-text-field label="Twóje imię/pseudonim" v-model="form.nick"></v-text-field>
                            </v-col>
                            <v-col cols="12">
                                <v-text-field
                                    label="Motyw*"
                                    hint="Określ do jakiego motywu pasuje wątek z utworu"
                                    v-model="form.motif"
                                    :rules="[v => !!v || 'Podaj motyw',
                                    v => !!v && v.length < 25 || 'Nazwa motywu jest zbyt długa']"
                                    required
                                ></v-text-field>
                            </v-col>
                            <v-col cols="12">
                                <v-textarea
                                    label="Opis*"
                                    hint="Opisz wątek z utworu pasujący do motywu"
                                    v-model="form.description"
                                    :rules="[v => !!v || 'Podaj opis',
                                    v => !!v && v.length > 150 || 'Opis jest zbyt krótki',
                                    v => !!v && v.length < 800 || 'Tekst jest zbyt długi']"
                                    required
                                    no-resize
                                ></v-textarea>
                            </v-col>
                        </v-row>
                    </v-container>
                    </v-form>
                </v-card-text>
                <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn color="red" text @click="closeDialog">Anuluj</v-btn>
                    <v-btn color="orange" dark depressed @click="submit">Wyślij</v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>
    </v-app>
</template>

<script>

export default {
  name: 'App',
  data: () => ({
    drawer: true,
    defaultAvatar: 'https://d1nz104zbf64va.cloudfront.net/dt/a/o/top-7-books-that-changed-the-world.jpg',
    motifs: [
        {
            id: 1,
            title: 'Przemiana',
            avatar: '',
            items: [
                {
                    title: 'Jądro ciemności',
                    author: 'Joseph Conrad',
                    description: 'Kurtz przebywają z dala od cywilizacji przechodzi przemianę. Staje się bezwzględny, podporządkowuje sobie miejscową ludność. Za nieposłudzeństwo karze śmiercią.'
                },
                {
                    title: 'Dziady cz.3',
                    author: 'Adam Mickiewicz',
                    description: 'TODO'
                }
            ]
        },
        {
            id: 2,
            title: 'Bunt przeciwko Bogu',
            avatar: 'https://encrypted-tbn0.gstatic.com/images?q=tbn%3AANd9GcSmP444T8irhwIaKxQbXkhpKfLOqZEFg74Z_lmIb4DCB5O6FVS9',
            items: [
                {
                    title: 'Mit o Prometeuszu',
                    description: 'Prometeusz wykrada bogom niebiański ogień i przekazuje go ludziom. Stosuje podstęp przeciw Zeusowi, za co ponosi karę',
                    type: 'Inny',
                    nick: 'grze2000',
                    createdAt: '31.03.2020'
                },
                {
                    title: 'Dziady cz.3',
                    author: 'Adam Mickiewicz',
                    description: 'TODO'
                }
            ]
        },
        {
            id: 3,
            title: 'Samotność',
            avatar: 'https://psychoterapiacotam.pl/wp-content/uploads/2018/01/Samotno%C5%9B%C4%87-przyczyny-skutki-objawy-i-leczenie-samotno%C5%9Bci.jpg',
            items: [
                {
                    title: 'Boulevard of Broken Dreams',
                    author: 'Green Day',
                    description: '',
                    year: '2004'
                }
            ]
        },
        {
            id: 4,
            title: 'Cierpienie',
            avatar: 'https://cultura.biografieonline.it/wp-content/uploads/2018/01/prometeo-1.jpg',
            items: [
                {
                    title: 'Mit o Prometeuszu',
                    description: 'Prometeusz oszukuje Zeusa przygotowując przeciw niemu podstęp. Zeus skazuje Prometeusza na niekończonce się cierpienie.'
                },
                {
                    title: 'Istoty ciemności',
                    author: 'Kami Garcia, Margaret Stohl',
                    description: ''
                }
            ]
        },
        {
            id: 5,
            title: 'Poświęcenie',
            avatar: 'https://img1.looper.com/img/gallery/the-real-reason-iron-man-had-no-dying-words-in-endgame/intro-1564493369.jpg',
            items: [
                {
                    title: 'Avengers: Koniec gry',
                    author: 'Joe Russo, Anthony Russo',
                    year: '2019',
                    description: [
                        'Tony Stark poświęca swoje życie aby zapobiec zniszczeniu świata oraz ocalić swoją córkę Morgan i zapewnić jej przyszłość. W ostatecznej walce Thanos zdobywa śmiercionośną broń i chcę jej użyć do zniszczenia ludzkości. Tony w ostatniej chwili odbiera przeciwnikowi broń. Stark widząc że to jedyny sposób na wygraną używa broni przeciw wrogom chociaż jest świadomy że poniesie przy tym śmierć.',
                        'Natasha Romanoff(Czarna Wdowa) oraz Clint Barton(Sokole Oko) zostają zmuszeni do podjęcia decyzji. Jedno z nich musi oddać życie aby możliwe było ocalenie ludzkości. Natasha nie ma własnej rodziny a Clint i jego rodzina są dla niej najbliższymi osobami. Żadne z nich nie chce aby to drugie się poświęciło. Ostatecznie to Czarna Wdowa powstrzymuje Clinta i poświęca swoje życie aby on mógł przeżyć i pomóc ocalić świat. '
                    ]
                }
            ]
        }
    ],
    dialog: false,
    selected: undefined,
    form: {
        name: '',
        type: null,
        author: '',
        year: '',
        nick: '',
        motif: '',
        description: ''
    },
    comboboxItems: [
        'Książka',
        'Film',
        'Serial',
        'Piosenka',
        'Wiersz',
        'Inny'
    ],
    valid: false
  }),
  created() {
      this.motifs.sort((a, b) => {
          if(a.title < b.title) return -1;
          else if(a.title > b.title) return 1;
          else return 0;
      });
  },
  methods: {
      submit() {
          this.$refs.addNewForm.validate();
          if(this.valid) {
              console.log(this.form);
          }
      },
      closeDialog() {
          this.$refs.addNewForm.reset();
          this.dialog = false;
      }
  }
};
</script>
