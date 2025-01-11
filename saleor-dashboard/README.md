![Saleor Dashboard](https://user-images.githubusercontent.com/44495184/185379472-2a204c0b-9b7a-4a3e-93c0-2cb85205ed5e.png)

<div align="center">
  <h1>Saleor Dashboard</h1>
</div>

<div align="center">
  <p>A GraphQL-powered, single-page dashboard application for <a href="https://github.com/saleor/saleor">Saleor</a>.</p>
</div>

<div align="center">
  <a href="https://saleor.io/">🏠 Website</a>
  <span> • </span>
  <a href="https://docs.saleor.io/docs/3.x/">📚 Docs</a>
  <span> • </span>
  <a href="https://saleor.io/blog/">📰 Blog</a>
  <span> • </span>
  <a href="https://twitter.com/getsaleor">🐦 Twitter</a>
  <span> • </span>
  <a href="https://discord.gg/H52JTZAtSH">💬 Discord</a>
</div>

<div align="center">
   <span> • </span>
  <a href="https://githubbox.com/saleor/saleor-dashboard">🔎 Explore Code</a>
</div>

## Prerequisites

- Node.js v18+
- A running instance of [Saleor](../Docs/Saleor.md)

## Development

1. Enter the project directory:

```bash
cd saleor-dashboard
```

2. Install the dependencies:

```bash
npm i
```

3. Configure the env vars as described in [docs/configuration.md](docs/configuration.md).

4. Start the development server with:

```bash
npm run dev
```

> Note:
> If you see CORS errors, check [CORS configuration](https://docs.saleor.io/docs/3.x/developer/running-saleor/configuration#allowed_client_hosts) of your Saleor instance or CORS settings in the Cloud Console.

## Docs

- [Configuration ⚙️](docs/configuration.md)
- [Error tracking ⚠️](docs/error-tracking.md)
- [Running tests 🏁](docs/running-tests.md)
- [Usage with Docker 🐳](docs/docker.md)
- [Sentry adapter 🗼](docs/sentry-adapter.md)
- [Deployment 🌐](docs/deployment.md)
