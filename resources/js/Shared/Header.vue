<template>
    <header
        class="d-flex flex-wrap justify-content-center py-3 mb-4 border-bottom"
    >
        <!-- <a
            href="/"
            class="d-flex align-items-center mb-3 mb-md-0 me-md-auto link-body-emphasis text-decoration-none"
        >
            <svg class="bi me-2" width="40" height="32">
                <use xlink:href="#bootstrap" />
            </svg>
            <span class="fs-4">Simple header</span>
        </a> -->

        <div class="col-md-3 mb-2 mb-md-0">
            <Link href="/" class="d-inline-flex link-body-emphasis">
                <span class="fs-4">{{ appName }}</span>
            </Link>
        </div>

        <ul class="nav nav-pills">
            <li class="nav-item">
                <Link
                    href="/"
                    :class="{
                        'nav-link active': $page.url === '/',
                        'nav-link': $page.url != '/',
                    }"
                    aria-current="page"
                    >Home</Link
                >
            </li>
            <li class="nav-item">
                <Link
                    href="/quiz"
                    :class="{
                        'nav-link active': $page.url === '/quiz',
                        'nav-link': $page.url != '/quiz',
                    }"
                    >Quiz</Link
                >
            </li>
            <li class="nav-item">
                <Link
                    href="/questions"
                    :class="{
                        'nav-link active': $page.url === '/questions',
                        'nav-link': $page.url != '/questions',
                    }"
                    >Questions</Link
                >
            </li>
            <li class="nav-item">
                <Link
                    href="/results"
                    :class="{
                        'nav-link active': $page.url === '/results',
                        'nav-link': $page.url != '/results',
                    }"
                    >Results</Link
                >
            </li>
        </ul>
    </header>
</template>

<script setup>
import { Link, usePage } from "@inertiajs/vue3";
import { computed } from "vue";
const page = usePage();
const appName = computed(() => page.props.appName);
</script>

<style lang="scss" scoped></style>
