<template>
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
        <SuggestionForm ref="form" :default="suggestions[selected]"></SuggestionForm>
    </v-card>
</template>

<script>
import axios from 'axios'
import SuggestionForm from '../components/SuggestionForm.vue'

export default {
    components: {
        SuggestionForm
    },
    data() {
        return {
            suggestions: [],
            selected: undefined
        }
    },
    methods: {
        refresh() {
            axios.get(`${process.env.VUE_APP_API_URL}/suggestions`).then((response) => {
                this.suggestions = response.data;
                this.$emit('updateSuggestionCount', this.suggestions.length);
            }).catch((err) => {
                if(err.response.status === 401) {
                    this.$router.push('/login');
                } else {
                    this.$emit('showSnackbar', err.response.data.message);
                }
            });
        },
        discard() {
            axios.delete(`${process.env.VUE_APP_API_URL}/suggestions/${this.suggestions[this.selected]._id}`).then(() => {
                this.$emit('showSnackbar', 'Propozycja została odrzucona!');
                this.selected = undefined;
                this.refresh();
            }).catch((err) => {
                this.$emit('showSnackbar', `Błąd: ${err}`);
            });
        },
        approve() {
            axios.post(`${process.env.VUE_APP_API_URL}/suggestions/${this.suggestions[this.selected]._id}`, this.$refs.form.getData()).then(() => {
                this.$emit('showSnackbar', 'Propozycja została zatwierdzona!');
                this.selected = undefined;
                this.refresh();
            }).catch((err) => {
                this.$emit('showSnackbar', `Błąd: ${err}`);
            });
        }
    },
    created() {
        this.refresh();
    }
}
</script>