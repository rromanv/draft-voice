<template>
	<h1 class=" absolute top-4 left-4 text-6xl font-light text-gray-200">
		{{ record ? 'Listening....' : '' }}
	</h1>
	<div class="h-screen bg-gradient-to-r from-teal-400 to-blue-500 flex justify-center items-center">
		<div
			@click="go"
			class="transition-all	 transform flex justify-center items-center  w-1/4 h-1/4 rounded-xl shadow-xl"
			:class="`bg-${color}-500 bg-${color} ${moveClass}`"
		>
			<span class="text-4xl text-gray-800 text-center">{{ text }}</span>
		</div>
	</div>
</template>

<script>
	import { ref, computed } from 'vue'
	export default {
		setup() {
			const color = ref('white')
			const colors = ['red', 'blue', 'teal', 'pink', 'yellow', 'green']
			const text = ref('')
			const positionX = ref(0)
			const positionY = ref(0)
			const record = ref(false)

			const moveClass = computed(() => {
				const x =
					positionX.value >= 0
						? `translate-x-${positionX.value.toString()}`
						: `-translate-x-${(positionX.value * -1).toString()}`
				const y =
					positionY.value >= 0
						? `translate-y-${positionY.value.toString()} `
						: `-translate-y-${(positionY.value * -1).toString()} `

				return `${x} ${y}`
			})

			// eslint-disable-next-line
			const recognition = new webkitSpeechRecognition()
			recognition.lang = 'en-US'
			recognition.continuous = true
			recognition.interimResults = false
			recognition.maxAlternatives = 1
			recognition.onstart = () => (text.value = '...')
			recognition.onresult = (event) => {
				// const data = event.results[0][0].transcript
				const data = event.results[event.resultIndex][0].transcript.trim()
				console.log(data)
				colors.includes(data) ? (color.value = data) : (text.value = '')
				data == 'right'
					? (positionX.value += 10)
					: data == 'left'
					? (positionX.value -= 10)
					: data == 'up'
					? (positionY.value -= 10)
					: data == 'down'
					? (positionY.value += 10)
					: null
				data == 'stop recording' ? go() : null
				text.value = data
			}
			const go = () => {
				record.value = !record.value
				record.value ? recognition.start() : recognition.stop()
			}

			return { color, text, moveClass, record, go }
		},
		name: 'App',
	}
</script>
