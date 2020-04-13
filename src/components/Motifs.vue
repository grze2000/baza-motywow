<template>
    <v-card v-if="typeof selected === 'undefined'">
        <v-list>
            <v-list-item-group v-model="selected">
                <v-list-item v-for="item in motifs" :key="item._id">{{ item.title }}</v-list-item>
            </v-list-item-group>
        </v-list>
    </v-card>
    <v-card v-else>
        {{ this.motifs[this.selected].imageURL }}
    </v-card>
</template>

<script>
import axios from 'axios'

export default {
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
        }
    }
}
</script>