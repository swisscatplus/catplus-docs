# Tutorial Chemboard

## Install the chemboard on your computer

Follow the setup instructions on the [Readme](https://github.com/sdsc-ordes/catplus-chembord) or the [documentation](https://sdsc-ordes.github.io/catplus-docs/frontend/#setup): to set up the chemboard for development on your computer. You will need to fill in your S3 credentials additionally to the provided environment variables

## Run the application locally

With `just local` the application should now be starting.

## Technology used

The Chemboard uses [Sveltekit](https://svelte.dev/), which has an excellent free [tutorial](https://svelte.dev/tutorial/svelte/welcome-to-svelte), in case you want to familiarize yourself with this technology. Also consider exploring the [docs](https://svelte.dev/docs), whenever you struggle understanding some concept, mentioned in the tutorial.

## Exercises

You will get know the code base a bit via the following exercise:

### Exercise 1

Enhance the result interface for S3 by displayin the search filters along with the result counts.

[S3 Searchpage](https://sdsc-ordes.github.io/catplus-docs/frontend/#data)

You can code while the application is running, as it updates automatically

#### Step 1

Find the page, that needs to get updated:

In Svelte there is the `routes` folder: every route is listed by its url: So to change the page [S3 search page](https://catplus.swisscustodian.ch/chembord/data), that has the url: `https://catplus.swisscustodian.ch/chembord/data` you have to go to the data route: `routes/data` in the code base.

There you will find two code files:

- `+page.server.ts`: loading data and performing form actions on the server
- `+page.svelte`: rendering the page in the browser

See Docs
