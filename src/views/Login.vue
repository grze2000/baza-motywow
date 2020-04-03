<template>
        <v-container fluid class="fill-height grey lighten-5">
            <v-row align="center" justify="center">
                <v-col cols="12" md="4">
                    <v-card class="" raised>
                        <v-toolbar flat dark color="orange darken-2">
                            <v-toolbar-title>Logowanie</v-toolbar-title>
                        </v-toolbar>
                        <v-card-text>
                            <v-form>
                                <v-text-field label="Nazwa użytkownika" prepend-icon="person"/>
                                <v-text-field type="password" label="Hasło" prepend-icon="lock"/>
                            </v-form>
                        </v-card-text>
                        <v-card-actions>
                            <v-spacer/>
                            <v-btn color="orange darken-2" dark>Zaloguj się</v-btn>
                        </v-card-actions>
                    </v-card>
                </v-col>
            </v-row>
        </v-container>
</template>

<script>
export default {
    name: 'Login'
}
</script>