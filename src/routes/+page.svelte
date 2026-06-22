<script lang="ts">
	import Front from '@frontapp/plugin-sdk'

	let context = $state<any>({})

	$effect(() => {
		const subscription = Front.contextUpdates.subscribe((frontContext) => {
			context = frontContext
		})
		return () => subscription.unsubscribe()
	})

	function extractEmailFromBlurb(blurb: string) {
		const match = blurb.match(/\(([^)]+)\)/)
		return match ? match[1] : null
	}

	const email = $derived.by(() => {
		const handle = context?.conversation?.recipient?.handle
		if (handle === 'no-reply@stockanalysis.com') {
			// extract the email from the first part of the message
			// looks like this: "From: Kristjan Mar Gunnarsson (kriistjanm@gmail.com) this is a message"
			return extractEmailFromBlurb(context.conversation.blurb)
		}
		return handle
	})
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
