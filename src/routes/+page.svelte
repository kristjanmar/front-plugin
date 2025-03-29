<script lang="ts">
	import Front from '@frontapp/plugin-sdk'
	import { onDestroy } from 'svelte'

	let context: any = {}
	const subscription = Front.contextUpdates.subscribe((frontContext) => {
		context = frontContext
	})

	onDestroy(() => {
		subscription.unsubscribe()
	})

	function extractEmailFromBlurb(blurb: string) {
		const email = blurb.match(/\(([^)]+)\)/)
		return email ? email[1] : null
	}

	$: email = context?.conversation?.recipient?.handle || 'kriistjanm@gmail.com'
	$: if (email === 'no-reply@stockanalysis.com') {
		// extract the email from the first part of the message
		// looks like this: "From: Kristjan Mar Gunnarsson (kriistjanm@gmail.com) this is a message"
		email = extractEmailFromBlurb(context.conversation.blurb)
	}

	async function copyToClipboard() {
		if (email) {
			try {
				await navigator.clipboard.writeText(email)
				// You could add a toast notification here if desired
			} catch (err) {
				console.error('Failed to copy email:', err)
			}
		}
	}
</script>

<div class="p-4">
	<h2 class="text-xl font-semibold">Shortcuts</h2>
	<div class="flex items-center gap-2">
		<div class="text-sm font-semibold text-gray-700">{email || 'Unknown email'}</div>
		{#if email}
			<button on:click={copyToClipboard} class="rounded-full hover:bg-gray-100" title="Copy email to clipboard">
				<svg
					xmlns="http://www.w3.org/2000/svg"
					width="16"
					height="16"
					viewBox="0 0 24 24"
					fill="none"
					stroke="currentColor"
					stroke-width="2"
					stroke-linecap="round"
					stroke-linejoin="round"
				>
					<rect x="9" y="9" width="13" height="13" rx="2" ry="2"></rect>
					<path d="M5 15H4a2 2 0 0 1-2-2V4a2 2 0 0 1 2-2h9a2 2 0 0 1 2 2v1"></path>
				</svg>
			</button>
		{/if}
	</div>

	<div class="mt-5 flex flex-col gap-1">
		{#if email}
			<a
				class="hover:underline"
				href="https://vendors.paddle.com/subscriptions/customers/all?sort_by=latest&search={encodeURIComponent(email)}"
				target="_blank">Paddle</a
			>
			<a
				class="hover:underline"
				href="https://app.convertkit.com/subscribers?q={encodeURIComponent(email)}&status=all"
				target="_blank">ConvertKit</a
			>
			<a
				class="hover:underline"
				href="https://supabase.com/dashboard/project/urfnzpbsaeuvfchdfjgz/editor/16632?sort=created_at%3Adesc&filter=email%3Aeq%3A{encodeURIComponent(
					email
				)}"
				target="_blank">Supabase</a
			>
		{:else}
			<a class="hover:underline" href="https://vendors.paddle.com/subscriptions/customers/all" target="_blank">Paddle</a
			>
			<a class="hover:underline" href="https://app.convertkit.com/subscribers" target="_blank">ConvertKit</a>
			<a
				class="hover:underline"
				href="https://supabase.com/dashboard/project/urfnzpbsaeuvfchdfjgz/editor/16632?sort=created_at%3Adesc"
				target="_blank">Supabase</a
			>
		{/if}
	</div>
</div>
