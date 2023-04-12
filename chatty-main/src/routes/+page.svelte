<script lang="ts">
	import ChatMessage from './components/ChatMessage.svelte'
	import link from './link-icon.png'
	import bgIMG from './bgIMG.jpg'
	import type { ChatCompletionRequestMessage } from 'openai'
	import { SSE } from 'sse.js'

	let query: string = ''
	let answer: string = ''
	let loading: boolean = false
	let chatMessages: ChatCompletionRequestMessage[] = []
	let scrollToDiv: HTMLDivElement

	function scrollToBottom() {
		setTimeout(function () {
			scrollToDiv.scrollIntoView({ behavior: 'smooth', block: 'end', inline: 'nearest' })
		}, 100)
	}

	const handleSubmit = async () => {
		loading = true
		chatMessages = [...chatMessages, { role: 'user', content: query }]

		const eventSource = new SSE('/api/chat', {
			headers: {
				'Content-Type': 'application/json'
			},
			payload: JSON.stringify({ messages: chatMessages })
		})

		query = ''

		eventSource.addEventListener('error', handleError)

		eventSource.addEventListener('message', (e) => {
			scrollToBottom()
			try {
				loading = false
				if (e.data === '[DONE]') {
					chatMessages = [...chatMessages, { role: 'assistant', content: answer }]
					answer = ''
					return
				}

				const completionResponse = JSON.parse(e.data)
				const [{ delta }] = completionResponse.choices

				if (delta.content) {
					answer = (answer ?? '') + delta.content
				}
			} catch (err) {
				handleError(err)
			}
		})
		eventSource.stream()
		scrollToBottom()
	}

	function handleError<T>(err: T) {
		loading = false
		query = ''
		answer = ''
		console.error(err)
	}
</script>
<div class="flex flex-col pt-2 w-full px-2 items-center gap-2">
	<h1 class="text-2xl font-bold text-center">Mock Interview</h1>
	<p class="text-sm italic text-center">- Powered by gpt-3.5-turbo</p>
	<div class="flex flex-col w-full px-8 text-end">
			<p>
				<a href="https://htmlpreview.github.io/?https://raw.githubusercontent.com/potdukhe12/My-Portfolio/main/Saurabh%20Potdukhe.html" class="text-sm" style="font-family: Lucida Console; text-decoration-line: underline; display: inline-block; color:dodgerblue">
					My Website
				</a>
				<img src={link} alt="link" width="15px" style="display: inline-block;" />
			</p>
	</div>
	<div class="h-[500px] w-full bg-gray-900 rounded-md p-4 overflow-y-auto flex flex-col gap-4" style="background-image: url({bgIMG}); background-repeat: repeat-y; background-size: cover;">
		<div class="flex flex-col gap-2">
			<ChatMessage type="assistant" message="Hello, my name is Mr. Smith!"
										  message1="I will be taking your mock interview."
										  message2="" 
										  message3="" />
			<ChatMessage type="assistant" message="Instructions: "
										  message1="1. /don't know/ : If you don't know the answer. "
										  message2="2. /change topic to <TOPIC>/ : To change the topic of interview. " 
										  message3="3. /next question/ : After I provide answer. " />
			<ChatMessage type="assistant" message="Select any TOPIC :"
										  message1="Example: JAVA, HTML, .NET, etc."
										  message2="" 
										  message3="" />
			{#each chatMessages as message}
				<ChatMessage type={message.role} message={message.content} message1="" message2="" message3="" />
			{/each}
			{#if answer}
				<ChatMessage type="assistant" message={answer} message1="" message2="" message3="" />
			{/if}
			{#if loading}
				<ChatMessage type="assistant" message="Loading.." message1="" message2="" message3="" />
			{/if}
		</div>
		<div class="" bind:this={scrollToDiv} />
	</div>
	<form
		class="flex w-full rounded-md gap-4 bg-gray-900 p-4"
		on:submit|preventDefault={() => handleSubmit()}
	>
		<input type="text" class="input input-bordered w-full" bind:value={query} />
		<button type="submit" class="btn btn-accent"> Send </button>
	</form>
</div>
