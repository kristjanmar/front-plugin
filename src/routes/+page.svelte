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

	$: email = context?.conversation?.recipient?.handle
	$: if (email === 'no-reply@stockanalysis.com') {
		// extract the email from the first part of the message
		// looks like this: "From: Kristjan Mar Gunnarsson (kriistjanm@gmail.com) this is a message"
		email = extractEmailFromBlurb(context.conversation.blurb)
	}
</script>

<div class="p-4">
	<h2 class="text-xl font-semibold">Shortcuts</h2>
	<div class="text-sm font-semibold text-gray-700">{email || 'Unknown email'}</div>

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
