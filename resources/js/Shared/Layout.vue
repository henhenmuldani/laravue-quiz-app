<template>
    <div class="container">
        <Header />
        <slot />
        <Footer />
    </div>
</template>

<script setup>
import Header from "@/Shared/Header.vue";
import Footer from "@/Shared/Footer.vue";
</script>

<style lang="scss" scoped></style>
