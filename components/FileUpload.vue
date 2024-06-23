<template>
	<div class="flex flex-row gap-2">
		<UInput type="file" ref="fileInput" @change="selectFile" icon="i-heroicons-folder" />
		<UButton @click="clrFile" icon="i-heroicons-arrow-path"></UButton>

	</div>
</template>

<script setup lang="ts">
import { useBase64 } from '@vueuse/core';
const target = defineModel('target')
const file = ref()
const { base64 } = useBase64(file)
const fileInput = ref<HTMLInputElement | null>(null);

const selectFile = (e) => {
	console.log(e)
	file.value = e[0]
}
const clrFile = () => {
	target.value = '';
	file.value = null;
	if (fileInput.value) {
		fileInput.value.value = '';
	}
};

watch(base64, (newVal) => {
	target.value = newVal
})
</script>
