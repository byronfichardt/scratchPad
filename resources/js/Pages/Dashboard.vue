<script setup>
import BreezeAuthenticatedLayout from '@/Layouts/Authenticated.vue';
import { Head } from '@inertiajs/inertia-vue3';
import NavTree from "@/Components/NavTree";
import MarkdownEditor from "@/Components/MarkdownEditor";

</script>

<template>
    <Head title="Dashboard" />

    <BreezeAuthenticatedLayout>
        <div class="py-4">
            <div class="mx-auto sm:px-6 lg:px-8">
                <div class="bg-white overflow-hidden shadow-sm sm:rounded-lg">
                    <div class="p-6 bg-white border-b border-gray-200">
                        <div class="flex">
                            <div class="flex-initial w-1/4">
                                <NavTree></NavTree>
                            </div>
                            <div class="flex-initial w-3/4">
                                <MarkdownEditor></MarkdownEditor>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </BreezeAuthenticatedLayout>
</template>
