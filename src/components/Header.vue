<template>
    <header>
        <nav class="flex justify-center p-4">
            <li class="mx-2">
                <router-link :to="{name: 'home'}">
                    Home
                </router-link>
            </li>
            <li class="mx-2">
                <router-link :to="{name: 'about'}">
                    About
                </router-link>
            </li>
        </nav>
    </header>
</template>
<script setup>
import { RouterLink } from 'vue-router'
</script>
<style scoped>

</style>