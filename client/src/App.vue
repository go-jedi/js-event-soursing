<template>
	<div className="center">
		<div>
			<div className="form">
				<input type="text" v-model.trim="inputForm" />
				<button @click="sendMessage">Отправить</button>
			</div>
			<div v-for="msg in messages" className="messages" :key="msg.id">
				<div class="message">
					{{ msg.message }}
				</div>
			</div>
		</div>
	</div>
</template>

<script>
import { defineComponent, ref, onMounted } from "vue"
import axios from "axios"

export default defineComponent({
	setup() {
		const inputForm = ref("")
		const messages = ref([])

		const subscribe = async () => {
			const eventSource = new EventSource("http://localhost:5000/connect")
			eventSource.onmessage = (e) => {
				const message = JSON.parse(e.data)
				messages.value.push(message)
			}
		}

		const sendMessage = async () => {
			const formData = {
				id: Date.now(),
				message: inputForm.value,
			}
			await axios.post(`http://localhost:5000/new-messages`, formData)
			inputForm.value = ""
		}

		onMounted(async () => {
			await subscribe()
		})

		return {
			inputForm,
			sendMessage,
			messages
		}
	}
})
</script>

<style lang="scss" scoped></style>