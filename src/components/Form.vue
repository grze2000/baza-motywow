<template>
    <v-form v-model="valid" ref="addNewForm">
        <v-row>
            <v-col cols="12">
                <v-text-field
                    label="Tytuł*"
                    hint="Podaj tytuł utworu"
                    v-model="suggestion.reference.title"
                    :rules="[v => !!v || 'To pole jest wymagane']"
                    required
                ></v-text-field>
            </v-col>
            <v-col cols="12" md="6">
                <v-text-field
                    label="Autor"
                    hint="Podaj autora. W przypadku filmów i seriali podaj reżysera/reżyserów"
                    v-model="suggestion.reference.author"
                ></v-text-field>
            </v-col>
            <v-col cols="12" md="6">
                <v-text-field
                    label="Rok wydania"
                    v-model="suggestion.reference.year"
                    :rules="[v => ((/^\d{4}$/.test(v) && parseInt(v) <= new Date().getFullYear()) || !v) || 'Podaj prawidłowy rok']"
                ></v-text-field>
            </v-col>
            <v-col cols="12" md="6">
                <v-overflow-btn
                    label="Typ utworu*"
                    :items="comboboxItems"
                    v-model="suggestion.reference.type"
                    :rules="[v => !!v || 'Wybierz typ utworu']"
                    required
                    dense
                ></v-overflow-btn>
            </v-col>
            <v-col cols="12" md="6">
                <v-text-field label="Twóje imię/pseudonim" v-model="suggestion.reference.nick"></v-text-field>
            </v-col>
            <v-col cols="12">
                <v-text-field
                    label="Motyw*"
                    hint="Określ do jakiego motywu pasuje wątek z utworu"
                    v-model="suggestion.motif"
                    :rules="[v => !!v || 'Podaj motyw',
                    v => !!v && v.length < 25 || 'Nazwa motywu jest zbyt długa']"
                    required
                ></v-text-field>
            </v-col>
            <v-col cols="12">
                <v-textarea
                    label="Opis*"
                    hint="Opisz wątek z utworu pasujący do motywu"
                    v-model="suggestion.reference.description"
                    :rules="[v => !!v || 'Podaj opis',
                    v => !!v && v.length > 150 || 'Opis jest zbyt krótki',
                    v => !!v && v.length < 800 || 'Tekst jest zbyt długi']"
                    required
                    no-resize
                    counter
                ></v-textarea>
            </v-col>
        </v-row>
    </v-form>
</template>
<script>
export default {
    name: 'Form',
    props: ['default'],
    data: () => ({
        suggestion: {
            motif: '',
            reference: {
                title: '',
                type: null,
                author: '',
                year: '',
                nick: '',
                description: ''
            }
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
        if(typeof this.default !== 'undefined') {
            this.suggestion = this.default;
        }
    },
    methods: {
        submit() {
            this.$refs.addNewForm.validate();
            return this.valid;
        },
        reset() {
            this.$refs.addNewForm.reset();
        }
    }
}
</script>