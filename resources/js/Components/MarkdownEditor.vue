<script setup>
import Editor from '@toast-ui/editor'
import '@toast-ui/editor/dist/toastui-editor.css';
import {onMounted} from "vue";

onMounted(() => {
    const editor = new Editor({
        el: document.querySelector('#editor'),
        height: '600px',
        initialEditType: 'markdown',
        previewStyle: 'vertical',
        initialValue: '',
        hideModeSwitch: true
    })
})
</script>
<template>
    <div id="editor" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm"></div>
</template>

