# Tutorial Chembord

## Install the Chembord on your computer

Follow the setup instructions on the [Readme](https://github.com/sdsc-ordes/catplus-chembord) or the [documentation](https://sdsc-ordes.github.io/catplus-docs/frontend/#setup): to set up the Chembord for development on your computer. You will need to fill in your S3 credentials additionally to the provided environment variables

## Run the application locally

With `just local` the application should now be starting.

## Technology used

The Chembord uses [Sveltekit](https://svelte.dev/), which has an excellent free [tutorial](https://svelte.dev/tutorial/svelte/welcome-to-svelte), in case you want to familiarize yourself with this technology. Also consider exploring the [docs](https://svelte.dev/docs), whenever you struggle understanding some concept, mentioned in the tutorial.

## Exercises

You will get know the code base a bit via the following exercise:

### Exercise 1

> :large_blue_diamond: Enhance the result interface of the [S3 Searchpage](https://sdsc-ordes.github.io/catplus-docs/frontend/#data) by displaying the search filters along with the result counts.

You can code while the application is running, as it updates automatically

#### Step 1: Identifying the page code

In Svelte there is the `routes` folder: every route is listed by its url: So to change the page [S3 search page](https://catplus.swisscustodian.ch/chembord/data), that has the url: `https://catplus.swisscustodian.ch/chembord/data` you have to go to the data route: `routes/data` in the code base.

There you will find two code files:

- `+page.server.ts`: loading data and performing form actions on the server
- `+page.svelte`: rendering the page in the browser

On the `+page.svelte` you have components that are rendered:

`.svelte` usually come the following parts: [Svelte Tutorial: your first component](https://svelte.dev/tutorial/svelte/your-first-component)

- `script`: importing functions, loading data, defining variables (also dynamic variables as `derived` and state variables as `state`)
- `html`: where variables from the script section are used
- `styles`: custom styles for just this file if needed

```svelte
<script lang="ts">
	import { page } from '$app/state';
    import ResultsHeaderData from '$lib/components/ResultsHeaderData.svelte';

    let year = $derived(page.url.searchParams.get('year') || undefined);
	let month = $derived(page.url.searchParams.get('month') || undefined);
	let day = $derived(page.url.searchParams.get('day') || undefined);
	let number = $derived(page.url.searchParams.get('number') || undefined);

    const resultsTotal: number = $derived(data.resultTotal);
</script>

<ResultsHeaderData
    resultsTotal={resultsTotal}
/>
```

#### Step 2: Changing the page code

- `<ResultsHeaderData />` renderes the result header.
- it corresponds to a compontent that you see imported in the `script` section: `lib/components/ResultsHeaderData.svelte` and it gets as input only the `resultsTotal`.

So you want to extend this input, whith a title that is derived from the active search filters:
`year`, `month`, `day`, `mumber`:

> :small_blue_diamond: Make another derived variable for the title:

`const resultTitle = ...`

> :small_blue_diamond: provides that additional variable to `<ResultsHeaderData />`.

#### Step 3: Changing the component

Now let's look at the component:

```svelte
<script lang="ts">
	let {
		resultsTotal,
	} = $props();
</script>

<h1 class="mb-4 p-4 text-xl font-bold text-surface-800-200">
    Results ({resultsTotal})
</h1>
{#if !resultsTotal}
	<p>
		No results have been found.
	</p>
{/if}
```

It is currently quite small.

> :small_blue_diamond: add the new variable to the props of the component and inspect it with `$inspect`.

See [Svelte $props](https://svelte.dev/docs/svelte/$props) and [Svelte $inspect](https://svelte.dev/docs/svelte/$inspect)


### Exercise 2


> :large_blue_diamond: Enhance the result interface of the [Qlever Searchpage](https://sdsc-ordes.github.io/catplus-docs/frontend/#search) by displaying the search filters along with the result counts.

#### Step 1: Identifying the page code

> :small_blue_diamond: Similar as above, find the page that you wqnt to change.

#### Step 2: Identifying the component

> :small_blue_diamond: Find the page that you wqnt to change.

The component in this casse already get's a lot more items:

```svelte
<ResultsHeaderSearch
	resultsTotal={resultsTotal}
	initialFilters={initialFilters}
	resultColumns={resultColumns}
	query={sparqlQuery}
/>
```

#### Step 3: Inspect variables

> :small_blue_diamond: Inspect the variables that the component already has and make use of them for the resultTitle field. Display the resultTitle on the page by adapting the HTML

> :star:  :blush: Congratulations! You have finished this tutorial on the Chembord.

## Deployment

In order to deploy the chembord see instructions at [catplus-deployment](https://github.com/swisscatplus/catplus-deployment).
The deployment expects that there is an image available at [chembord packages](https://github.com/swisscatplus/catplus-chembord/pkgs/container/catplus-chembord).

### Prerequisites

- `podman`: images are currently build with podman: you need to install podman and run the podman server, see here: [podman-start](https://docs.podman.io/en/stable/markdown/podman-start.1.html).

### Build image

- build image: `just image build`

### Update image on github

The image is pushed automatically when the an update on the `main` branch happens by the github action:

- first time: login into github with podman: `podman login ghcr.io`
- once logged in you can push the image that has been build, for example: `podman push ghcr.io/swisscatplus/catplus-chembord:latest`.
