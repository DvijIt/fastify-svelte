# Fastify App Example

This project is a small but feature complete application build with Fastify and Svelte,
and it aims to show all the core concepts of Fastify, best practices, and recommendations.

There is no attached blog post or tutorial, you should go through the application code
and read the code comments, which will explain you best practices, protips, suggestions
and so forth, I hope you will like it!

## What is this application doing?

This application is an URL shortener, it exposes an admin UI and a series of routes
under the `/_app` prefix.
Any other request will be interpreted as redirect. If a redirect does not exists, 
the user will either see a standard 404 page, or a page with a series of suggestions
generated by Elasticsearch *(both this pages are server side rendered!)*.

The admin logins via GitHub OAuth, the application is also protected by a rate limiter
and during development it exposes a Swagger UI with every endpoint.
Read the next section to understand how the project works and how it is recommended
to explore it.

## Project structure

The project follows a well established pattern within the Fastify community,
it has two top level folders, `plugins` and `routes`.

<p style="text-align:center">
  <img src="./structure.png" alt="project structure">
</p>

The first one contains all the code that should be shared across your entire
aplication, such as authentication and rate limiting, while the second one
contains all the business logic, such as the redirect code and the admin APIs.

We recommend to follow this pattern, you can generate a project with the same
structure by using [fastify-cli](https://github.com/fastify/fastify-cli).

### How should I read through the comments?

You can read the project in any order, but I would recommend the following:

1. `app.js`
1. `plugins/authorization.js`
1. `plugins/elasticsearch.js`
1. `plugins/rate-limit.js`
1. `plugins/validUrl.js`
1. `plugins/swagger.js`
1. `routes/status.js`
1. `routes/frontend.js`
1. `routes/admin.js`
1. `routes/redirect/index.js`
1. `routes/redirect/update-count.js`
1. `routes/redirect/worker.cjs`
1. `routes/redirect/App.svelte`
1. `ui/*`
1. `test/helper.js`
1. `test/plugins/validUrl.test.js`
1. `test/routes/status.test.js`
1. `test/routes/admin.test.js.test.js`
1. `test/routes/redirect.test.js.test.js`

## FAQ 

### You are using ESM, how are you compiling the project?

I'm not. From Node.js v12.20 and Node.js v14 ES Modules are supported [out of the box](https://nodejs.org/api/esm.html).
As you can see in the `package.json`, there is a new field: `{ "type": "module" }`.
That field will instruct Node.js that the project is using ESM instead of CJS (common js, which is
`require`/`module.exports`), and every `.js` file will be a ESM module, while every `.cjs` file will be a CJS module.

A follow up question you can ask now is: "why is there a `rollup.config.js`?
Great question! That is used for compiling the Svelte frontend locatated in `ui/`.

### Can I use TypeScript?

Yes! Fastify supports TypeScript [out of the box](https://www.fastify.io/docs/latest/TypeScript/)!
The project is written in plain JavaScript because I didn't want to add too many things
to the project, but probably in the future there will be a branch with a TypeScript implementation.

### Why aren't you using a try-catch block in route declarations?

Fastify supports promises/async-await [out of the box](https://www.fastify.io/docs/latest/Routes/#async-await).
Everything is handled for you, if you throw an error inside a route handler (same goes
for hooks or plugins) the error will be caught automatically by Fastify and return
the most approriate error.

### Why Svelte?

No specific reason, any frontend framework will do the job well. I used Svelte because I like
it and because it makes it very easy to think about the frontend, without making me think
too much about how something should be written or weird APIs, but directly focusing
on the business logic while writing almost plain html/css/js.

## How to run this project

1. Create an `.env` file from the template:

    ```sh
    cp .env.template .env
    ```

2. Create a new GitHub OAuth application [here](https://github.com/settings/applications/new), then copy the app id and secret and add them to the env file:

    ```dosini
    GITHUB_APP_ID=<app-id>
    GITHUB_APP_SECRET=<app-secret>
    ```

3. Add your primary GitHub email to the `ALLOWED_USERS` variable:

    ```dosini
    ALLOWED_USERS=<your-primary-github-mail>
    ```

4. Run the `keys-generator` script and store the result in the `COOKIE_SECRET` env variable:

    ```sh
    node scripts/keys-generator.js
    ```

    ```dosini
    COOKIE_SECRET=<generated-key>
    ```

Now you can either run the project locally or deploy it.

### Locally

1. Use Node.js v18.

    ```sh
    curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.37.2/install.sh | bash
    nvm install 18
    node -v # should be v18.minor.patch
    ```

2. Install the project dependencies:

    ```sh
    npm install
    ```

3. In a separate terminal window, run Elasticsearch:

    ```sh
    npm run elasticsearch
    ```

4. Once Elasticsearch is up and running, run the `prepare-elasticsearch` script and copy the result in the `ELASTIC_URL` and `ELASTIC_API_KEY` env variables:

    ```js
    node scripts/prepare-elasticsearch.js
    ```

    ```dosini
    ELASTIC_ADDRESS=<result.address>
    ELASTIC_API_KEY=<result.apiKey>
    ```

5. You are all set! Run the project with thw following command:

    ```sh
    npm run dev
    ```

### Deploy

This section contains instructions for deploying this application.

Would you like to see more recipes? Open an [issue](https://github.com/delvedor/fastify-example/issues/new).
Do you already have a deploy recipe and want to share it? That's awesome, send a [pull request](https://github.com/delvedor/fastify-example/compare)!

#### Cloud  Run

Open [`deploy-recipes/cloud-run`](./deploy-recipes/cloud-run), you will find everything you need there.

#### Elastic Cloud

You can create an Elasticsearch cluster with Elastic Cloud, with a free 14-day trial of the [Elasticsearch Service](https://www.elastic.co/elasticsearch/service).

## Contribute

Feel free to send pull request with new features, bugfix or documentation improvements!

## Questions

Open an [issue](https://github.com/delvedor/fastify-example/issues/new) or take a look at our [`fastify/help`](https://github.com/fastify/help) repository.
We also have a [Discord community](https://discord.gg/D3FZYPy) you can join.

If you have any question related to Elasticsearch or the Elasticsearch client,
you can open a new discussion on [discuss.elastic.co](https://discuss.elastic.co/)
or in the client [issue tracker](https://github.com/elastic/elasticsearch-js/issues?q=is%3Aissue+is%3Aopen+sort%3Aupdated-desc).

## License

This software is licensed under the [Apache 2 license](./LICENSE).
