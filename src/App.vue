<template>
    <v-app>
        <router-view></router-view>
    </v-app>
</template>

<script>
export default {
    name: 'App'
};
</script>

<style>
html {
    overflow-y: auto !important;
}
.list-scroll {
    overflow-y: auto;
}
::-webkit-scrollbar-track
{
    -webkit-box-shadow: inset 0 0 5px rgba(128,128,128,0.2);
}
::-webkit-scrollbar
{
    width: 8px;
    background-color: #F5F5F5;
}
::-webkit-scrollbar-thumb
{
    background-color: #ffb74b;
    border-radius: 1px;
}
.br-top {
    border-top: 1px solid #e0e0e0;
}
</style>