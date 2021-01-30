<template>
	<input type="text" placeholder="Raqam" v-model="keyword" />

	<ul>
		<li v-for="[ fiskal, raqam ] in items">
			<div>{{ raqam }}</div>
			<div>{{ fiskal }}</div>
		</li>
	</ul>
</template>

<script setup>
import { ref, computed } from 'vue'

const data = ref([])

fetch('/data.txt')
	.then(resp => resp.text())
	.then(text => {
		data.value = text
			.split('\n')
			.slice(1)
			.filter(row => row)
			.map(row => {
				const body = row.split(',')[1]
				const [ , fiskal, raqam ] = body.match(/(\d+)\sfiskal.*(\d{6})\sraqam/)
				return [fiskal, raqam]
			})
	})

const keyword = ref('')
const items = computed(() => {
	if (!keyword.value) {
		return []
	}
	return data.value.filter(([ fiskal, raqam ]) => {
		return raqam.startsWith(keyword.value)
	})
})
</script>
