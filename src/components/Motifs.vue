<template>
    <v-card v-if="typeof selected === 'undefined'">
        <v-list>
            <v-list-item-group v-model="selected">
                <v-list-item v-for="item in motifs" :key="item._id">{{ item.title }}</v-list-item>
            </v-list-item-group>
        </v-list>
    </v-card>
    <v-card v-else>
        <v-container>
            <div class="d-flex flex-row pb-2 align-center">
                <v-btn dark fab color="orange" small @click="selected = undefined">
                    <v-icon>arrow_back</v-icon>
                </v-btn>
                <v-spacer></v-spacer>
                <v-btn color="success" small @click="save">
                    <v-icon left>check</v-icon>
                    Zapisz
                </v-btn>
            </div>
            <MotifForm :default="motifs[selected]" ref="form"></MotifForm>
        </v-container>
    </v-card>
</template>

<script>
import axios from 'axios'
import MotifForm from '../components/MotifForm.vue'

export default {
    components: {
        MotifForm
    },
    data() {
        return {
            motifs: [],
            selected: undefined
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
                axios.patch(`${process.env.VUE_APP_API_URL}/motifs/${this.motifs[this.selected]._id}`, this.$refs.form.form).then(() => {
                    this.refresh();
                    this.$emit('showSnackbar', 'Zmiany zostały zapisane!');
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