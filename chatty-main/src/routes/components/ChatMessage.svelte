<script lang="ts">
	import type { ChatCompletionRequestMessageRoleEnum } from 'openai'
	export let type: ChatCompletionRequestMessageRoleEnum
	export let message: string
	export let message1: string
	export let message2: string
	export let message3: string
</script>

<div class="chat {type === 'user' ? 'chat-end' : 'chat-start'} justify-end">
	<div class="chat-image avatar">
		<div class="w-10 rounded-full">
			<!--<img
				 src="https://ui-avatars.com/api/?name={type === 'user' ? 'Me' : 'S'}"
				alt="{type} avatar"
			/> -->
			<img
				src="{type === 'user' ? 'https://ui-avatars.com/api/?name=ME&font-size=0.4' : 'https://randomuser.me/api/portraits/men/32.jpg' }"
				alt="{type} avatar"
			/>
		</div>
	</div>
	<div class="chat-header">
		{type === 'user' ? 'Me' : 'Mr. Smith'}
	</div>
	<div class="chat-bubble {type === 'user' ? 'chat-bubble-primary' : 'chat-bubble-secondary'}">
		<div>{message}</div>
		<div style="font-size: small;">
			<div>{message1}</div>
			<div>{message2}</div>		<!-- <div> tag is used to make the messages appear on the new line -->
			<div>{message3}</div>
		</div>
	</div>
</div>
