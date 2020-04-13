<template>
    <v-card>
        <v-list>
            <div v-for="motif in motifs" :key="motif._id">
                <v-subheader>{{ motif.title }}</v-subheader>
                <v-list-item v-for="item in motif.items" :key="item._id">{{ item.title }}</v-list-item>
            </div>
        </v-list>
    </v-card>
</template>

<script>
import axios from 'axios'

export default {
    data() {
        return {
            motifs: []
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