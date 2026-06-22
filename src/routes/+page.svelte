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

	let copied = $state(false)

	async function copyEmail() {
		if (!email) return
		try {
			await navigator.clipboard.writeText(email)
			copied = true
			setTimeout(() => (copied = false), 1500)
		} catch (err) {
			console.error('Failed to copy email:', err)
		}
	}
</script>

<div class="p-4">
	<h2 class="text-xl font-semibold">Shortcuts</h2>
	<div class="flex items-center gap-2">
		<div class="text-sm font-semibold text-gray-700">{email || 'Unknown email'}</div>
		{#if email}
			<button
				type="button"
				onclick={copyEmail}
				class="inline-flex cursor-pointer items-center justify-center rounded-full p-0.5 text-gray-400 hover:bg-gray-100 hover:text-gray-700"
				title="Copy email to clipboard"
				aria-label="Copy email to clipboard"
			>
				{#if copied}
					<svg
						xmlns="http://www.w3.org/2000/svg"
						class="text-green-600"
						width="16"
						height="16"
						viewBox="0 0 24 24"
						fill="none"
						stroke="currentColor"
						stroke-width="2"
						stroke-linecap="round"
						stroke-linejoin="round"
					>
						<polyline points="20 6 9 17 4 12"></polyline>
					</svg>
				{:else}
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
				{/if}
			</button>
		{/if}
	</div>

	<div class="mt-5 flex flex-col gap-1">
		{#if email}
			<a
				class="hover:underline"
				href="https://stockanalysis.com/admin/users/?q={encodeURIComponent(email)}"
				target="_blank">Lookup User</a
			>
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
			<a class="hover:underline" href="https://stockanalysis.com/admin/users/" target="_blank">Lookup User</a>
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
