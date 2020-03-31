<template>
    <v-form v-model="valid" ref="addNewForm">
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
                <v-overflow-btn
                    label="Typ utworu*"
                    :items="comboboxItems"
                    v-model="form.type"
                    :rules="[v => !!v || 'Wybierz typ utworu']"
                    required
                    dense
                ></v-overflow-btn>
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
    </v-form>
</template>
<script>
export default {
    name: 'Form',
    data: () => ({
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
    methods: {
        submit() {
            this.$refs.addNewForm.validate();
            if(this.valid) {
                console.log(this.form);
            }
        },
        reset() {
            this.$refs.addNewForm.reset();
        }
    }
}
</script>