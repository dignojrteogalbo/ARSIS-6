<script lang="ts">
	import {
		Timeline,
		TimelineItem,
		Button,
		Textarea,
		Input,
		Img,
		Fileupload
	} from 'flowbite-svelte';
	import { PlusOutline, PlusSolid, TrashBinOutline } from 'flowbite-svelte-icons';
	import AdditionButton from './AdditionButton.svelte';
	import RemoveButton from './RemoveButton.svelte';
	import type { MouseEventHandler } from 'svelte/elements';
	import File from './File.svelte';
	import ImageAdditionButton from './ImageAdditionButton.svelte';

	export let title: string;
	export let editMode: boolean = false;
	export let date: string;
	export let description: string;
	export let steps: Array<Object>;
	export let links: Array<string> = [];
	export let problemLinks: Array<string> = [];

	export let createNewStep: MouseEventHandler<EventTarget>;
	export let removeThisStep: MouseEventHandler<EventTarget>;

	function addEmpty(position: number) {
		steps.splice(position + 1, 0, { type: 'text', body: '' });
		steps = steps;
	}

	function changeType(position: number) {
		let step = steps[position];
		step['body'] = '';
		if (step['type'] == 'text') {
			step['type'] = 'image';
		} else {
			step['type'] = 'text';
		}
		steps[position] = step;
	}

	function removeAt(position: number) {
		steps.splice(position, 1);
		steps = steps;
	}
</script>

<TimelineItem {title} {date} class="pb-2">
	{#if editMode}
		<Input bind:value={title} class="mb-2" />
		<Textarea
			class="text-base font-normal text-gray-500 dark:text-gray-400"
			bind:value={description}
			rows="3"
		/>
		<br />
		{#if steps.length > 0}
			<h3>Steps</h3>
		{/if}
		<AdditionButton
			onclick={() => {
				addEmpty(-1);
			}}
			first={true}
		/>
		{#each steps as step, i}
			{#if step.type == 'text'}
				<div class="mt-4">
					<Input bind:value={step.body} />
					<AdditionButton
						onclick={() => {
							addEmpty(i);
						}}
						last={i == steps.length - 1}
					/>
					<ImageAdditionButton
						onclick={() => {
							changeType(i);
						}}
					/>
					<RemoveButton
						onclick={() => {
							removeAt(i);
						}}
					/>
				</div>
			{:else if step.type == 'image'}
				<File bind:encodedString={step.body} />
				<AdditionButton
					onclick={() => {
						addEmpty(i);
					}}
				/>
				<ImageAdditionButton
					onclick={() => {
						changeType(i);
					}}
					isImage={true}
				/>
				<RemoveButton
					onclick={() => {
						removeAt(i);
					}}
				/>
			{/if}
		{/each}

		<br />
	{:else}
		<p class="text-base font-normal text-gray-500 dark:text-gray-400">
			{description}
		</p>
		{#each steps as step}
			{#if step.type == 'text'}
				<div>{step.body}</div>
			{:else if step.type == 'image'}
				<Img src="data:image/png;base64,{step.body}" />
			{/if}
		{/each}
	{/if}
	{#each problemLinks as link}
		<Button class="m-1">{link}</Button>
	{/each}
	{#each links as link}
		<Button color="alternative" class="m-1">{link}</Button>
	{/each}

	<br />
	{#if editMode}
		<Button color="alternative" class="m-1" on:click={createNewStep}>New Step</Button>
		<Button class="m-1" on:click={removeThisStep}>Remove</Button>
	{/if}
</TimelineItem>
