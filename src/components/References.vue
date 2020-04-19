<template>
    <v-card v-if="typeof selectedReference === 'undefined'">
        <v-list>
            <v-list-item-group v-model="selectedMotif">
                <v-list-group v-for="motif in motifs" :key="motif._id" >
                    <template v-slot:activator>
                        <v-list-item-content>
                            <v-list-item-title v-text="motif.title">{{ motif.title }}</v-list-item-title>
                        </v-list-item-content>
                    </template>
                    <v-list-item-group v-model="selectedReference">
                        <v-list-item v-for="item in motif.items" :key="item._id">{{ item.title }}</v-list-item>
                    </v-list-item-group>
                    <v-divider></v-divider>
                </v-list-group>
            </v-list-item-group>
        </v-list>
    </v-card>
    <v-card v-else class="pa-5">
        <div class="d-flex align-center">
            <v-btn dark fab color="orange" small @click="selectedReference = undefined">
                <v-icon>arrow_back</v-icon>
            </v-btn>
            <v-spacer></v-spacer>
            <v-btn small color="success" @click="save">
                <v-icon left>check</v-icon>
                Zapisz
            </v-btn>
        </div>
        <SuggestionForm ref="form" :default="{ motif: motifs[selectedMotif].title, reference: motifs[selectedMotif].items[selectedReference]}"></SuggestionForm>
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
            motifs: [],
            selectedMotif: undefined,
            selectedReference: undefined
        }
    },
    created() {
        this.refresh();
    },
    methods: {
        refresh() {
            axios.get(`${process.env.VUE_APP_API_URL}/motifs`).then((response) => {
                this.motifs = response.data;
            }).catch((err) => {
                if(err.response.status === 401) {
                    this.$router.push('/login');
                } else {
                    this.$emit('showSnackbar', err.response.data.message);
                }
            });
        },
        save() {
            if(this.$refs.form.submit()) {
                axios.put(`${process.env.VUE_APP_API_URL}/motifs/${this.motifs[this.selectedMotif]._id}/references/${this.motifs[this.selectedMotif].items[this.selectedReference]._id}`, this.$refs.form.getData().reference).then(() => {
                    this.$emit('showSnackbar', 'Zmiany zostały zapisane');
               }).catch((err) => {
                   if(typeof err.response.data.message !== 'undefined') {
                       this.$emit('showSnackbar', err.response.data.message);
                   } else {
                       this.$emit('showSnackbar', err.message);
                   }
                   
               });
            }
        }
    }
}
</script>