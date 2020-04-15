<template>
    <v-form ref="form" v-model="valid">
        <div class="d-flex flex-row">
            <div class="flex-grow-1 pa-2">
                <v-text-field label="Nazwa motywu" v-model="form.title" :rules="[v => !!v || 'To pole jest wymagane']"></v-text-field>
                <v-text-field label="Adres URL grafiki" v-model="form.imageURL"></v-text-field>
            </div>
            <div class="text-center pa-2">
                <v-avatar size="150" color="grey lighten-2">
                    <img v-show="validSource"
                        :src="form.imageURL"
                        alt=""
                        @load="validSource = true"
                        @error="validSource = false"
                        style="object-fit: cover"
                    >
                    <v-icon v-if="!validSource" x-large>insert_photo</v-icon>
                </v-avatar>
            </div>
        </div>
    </v-form>
</template>

<script>
export default {
    props: [
        'default'
    ],
    data() {
        return {
            validSource: false,
            form: {
                title: '',
                imageURL: ''
            },
            valid: true
        }
    },
    created() {
        this.form.title = this.default.title;
        this.form.imageURL = this.default.imageURL;
    },
    methods: {
        submit() {
            this.$refs.form.validate()
            return this.valid;
        }
    }
}
</script>