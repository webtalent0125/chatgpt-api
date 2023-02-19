# ChatGPT API <!-- omit in toc -->

> Node.js client for the unofficial [ChatGPT](https://openai.com/blog/chatgpt/) API.

[![NPM](https://img.shields.io/npm/v/chatgpt.svg)](https://www.npmjs.com/package/chatgpt) [![Build Status](https://github.com/transitive-bullshit/chatgpt-api/actions/workflows/test.yml/badge.svg)](https://github.com/transitive-bullshit/chatgpt-api/actions/workflows/test.yml) [![MIT License](https://img.shields.io/badge/license-MIT-blue)](https://github.com/transitive-bullshit/chatgpt-api/blob/main/license) [![Prettier Code Formatting](https://img.shields.io/badge/code_style-prettier-brightgreen.svg)](https://prettier.io)

- [Intro](#intro)
- [Updates](#updates)
- [CLI](#cli)
- [Install](#install)
- [Usage](#usage)
  - [Usage - ChatGPTAPI](#usage---chatgptapi)
  - [Usage - ChatGPTUnofficialProxyAPI](#usage---chatgptunofficialproxyapi)
    - [Reverse Proxy](#reverse-proxy)
    - [Access Token](#access-token)
- [Docs](#docs)
- [Demos](#demos)
- [Projects](#projects)
- [Compatibility](#compatibility)
- [Credits](#credits)
- [License](#license)

## Intro

This package is a Node.js wrapper around [ChatGPT](https://openai.com/blog/chatgpt) by [OpenAI](https://openai.com). TS batteries included. ✨

<p align="center">
  <img alt="Example usage" src="/media/demo.gif">
</p>

## Updates

<details open>
<summary><strong>Feb 19, 2023</strong></summary>

We now provide three ways of accessing the unofficial ChatGPT API, all of which have tradeoffs:

| Method                      | Free?  | Robust?  | Quality?          |
| --------------------------- | ------ | -------- | ----------------- |
| `ChatGPTAPI`                | ❌ No  | ✅ Yes   | ☑️ Mimics ChatGPT |
| `ChatGPTUnofficialProxyAPI` | ✅ Yes | ☑️ Maybe | ✅ Real ChatGPT   |
| `ChatGPAPIBrowser` (v3)     | ✅ Yes | ❌ No    | ✅ Real ChatGPT   |

**Note**: I recommend that you use either `ChatGPTAPI` or `ChatGPTUnofficialProxyAPI`.

1. `ChatGPTAPI` - Uses `text-davinci-003` to mimic ChatGPT via the official OpenAI completions API (most robust approach, but it's not free and doesn't use a model fine-tuned for chat)
2. `ChatGPTUnofficialProxyAPI` - Uses an unofficial proxy server to access ChatGPT's backend API in a way that circumvents Cloudflare (uses the real ChatGPT and is pretty lightweight, but relies on a third-party server and is rate-limited)
3. `ChatGPTAPIBrowser` - (_deprecated_; v3.5.1 of this package) Uses Puppeteer to access the official ChatGPT webapp (uses the real ChatGPT, but very flaky, heavyweight, and error prone)

</details>

<details>
<summary><strong>Previous Updates</strong></summary>

<br/>

<details>
<summary><strong>Feb 5, 2023</strong></summary>

OpenAI has disabled the leaked chat model we were previously using, so we're now defaulting to `text-davinci-003`, which is not free.

We've found several other hidden, fine-tuned chat models, but OpenAI keeps disabling them, so we're searching for alternative workarounds.

</details>

<details>
<summary><strong>Feb 1, 2023</strong></summary>

This package no longer requires any browser hacks – **it is now using the official OpenAI completions API** with a leaked model that ChatGPT uses under the hood. 🔥

```ts
import { ChatGPTAPI } from 'chatgpt'

const api = new ChatGPTAPI({
  apiKey: process.env.OPENAI_API_KEY
})

const res = await api.sendMessage('Hello World!')
console.log(res.text)
```

Please upgrade to `chatgpt@latest` (at least [v4.0.0](https://github.com/transitive-bullshit/chatgpt-api/releases/tag/v4.0.0)). The updated version is **significantly more lightweight and robust** compared with previous versions. You also don't have to worry about IP issues or rate limiting.

Huge shoutout to [@waylaidwanderer](https://github.com/waylaidwanderer) for discovering the leaked chat model!

</details>
</details>

If you run into any issues, we do have a pretty active [Discord](https://discord.gg/v9gERj825w) with a bunch of ChatGPT hackers from the Node.js & Python communities.

Lastly, please consider starring this repo and <a href="https://twitter.com/transitive_bs">following me on twitter <img src="https://storage.googleapis.com/saasify-assets/twitter-logo.svg" alt="twitter" height="24px" align="center"></a> to help support the project.

Thanks && cheers,
[Travis](https://twitter.com/transitive_bs)

## CLI

To run the CLI, you'll need an [OpenAI API key](https://platform.openai.com/overview):

```bash
export OPENAI_API_KEY="sk-TODO"
npx chatgpt "your prompt here"
```

By default, the response is streamed to stdout, the results are stored in a local config file, and every invocation starts a new conversation. You can use `-c` to continue the previous conversation and `--no-stream` to disable streaming.

Under the hood, the CLI uses `ChatGPTAPI` with `text-davinci-003` to mimic ChatGPT.

```
Usage:
  $ chatgpt <prompt>

Commands:
  <prompt>  Ask ChatGPT a question
  rm-cache  Clears the local message cache
  ls-cache  Prints the local message cache path

For more info, run any command with the `--help` flag:
  $ chatgpt --help
  $ chatgpt rm-cache --help
  $ chatgpt ls-cache --help

Options:
  -c, --continue          Continue last conversation (default: false)
  -d, --debug             Enables debug logging (default: false)
  -s, --stream            Streams the response (default: true)
  -s, --store             Enables the local message cache (default: true)
  -t, --timeout           Timeout in milliseconds
  -k, --apiKey            OpenAI API key
  -n, --conversationName  Unique name for the conversation
  -h, --help              Display this message
  -v, --version           Display version number
```

## Install

```bash
npm install chatgpt
```

Make sure you're using `node >= 18` so `fetch` is available (or `node >= 14` if you install a [fetch polyfill](https://github.com/developit/unfetch#usage-as-a-polyfill)).

## Usage

To use this module from Node.js, you need to pick between two methods:

| Method                      | Free?  | Robust?  | Quality?          |
| --------------------------- | ------ | -------- | ----------------- |
| `ChatGPTAPI`                | ❌ No  | ✅ Yes   | ☑️ Mimics ChatGPT |
| `ChatGPTUnofficialProxyAPI` | ✅ Yes | ☑️ Maybe | ✅ Real ChatGPT   |

1. `ChatGPTAPI` - Uses `text-davinci-003` to mimic ChatGPT via the official OpenAI completions API (most robust approach, but it's not free and doesn't use a model fine-tuned for chat). You can override the model, completion params, and prompt to fully customize your bot.

2. `ChatGPTUnofficialProxyAPI` - Uses an unofficial proxy server to access ChatGPT's backend API in a way that circumvents Cloudflare (uses the real ChatGPT and is pretty lightweight, but relies on a third-party server and is rate-limited)

Both approaches have very similar APIs, so it should be simple to swap between them.

### Usage - ChatGPTAPI

Sign up for an [OpenAI API key](https://platform.openai.com/overview) and store it in your environment.

```ts
import { ChatGPTAPI } from 'chatgpt'

async function example() {
  const api = new ChatGPTAPI({
    apiKey: process.env.OPENAI_API_KEY
  })

  const res = await api.sendMessage('Hello World!')
  console.log(res.text)
}
```

You can override the default `model` (`text-davinci-003`) and any [OpenAI completion params](https://platform.openai.com/docs/api-reference/completions/create) using `completionParams`:

```ts
const api = new ChatGPTAPI({
  apiKey: process.env.OPENAI_API_KEY,
  completionParams: {
    temperature: 0.5,
    top_p: 0.8
  }
})
```

If you want to track the conversation, you'll need to pass the `parentMessageid` and `conversationid`:

```ts
const api = new ChatGPTAPI({ apiKey: process.env.OPENAI_API_KEY })

// send a message and wait for the response
let res = await api.sendMessage('What is OpenAI?')
console.log(res.text)

// send a follow-up
res = await api.sendMessage('Can you expand on that?', {
  conversationId: res.conversationId,
  parentMessageId: res.id
})
console.log(res.text)

// send another follow-up
res = await api.sendMessage('What were we talking about?', {
  conversationId: res.conversationId,
  parentMessageId: res.id
})
console.log(res.text)
```

You can add streaming via the `onProgress` handler:

```ts
const res = await api.sendMessage('Write a 500 word essay on frogs.', {
  // print the partial response as the AI is "typing"
  onProgress: (partialResponse) => console.log(partialResponse.text)
})

// print the full text at the end
console.log(res.text)
```

You can add a timeout using the `timeoutMs` option:

```ts
// timeout after 2 minutes (which will also abort the underlying HTTP request)
const response = await api.sendMessage(
  'write me a really really long essay on frogs',
  {
    timeoutMs: 2 * 60 * 1000
  }
)
```

If you want to see more info about what's actually being sent to [OpenAI's completions API](https://platform.openai.com/docs/api-reference/completions), set the `debug: true` option in the `ChatGPTAPI` constructor:

```ts
const api = new ChatGPTAPI({
  apiKey: process.env.OPENAI_API_KEY,
  debug: true
})
```

You'll notice that we're using a reverse-engineered `promptPrefix` and `promptSuffix`. You can customize these via the `sendMessage` options:

```ts
const res = await api.sendMessage('what is the answer to the universe?', {
  promptPrefix: `You are ChatGPT, a large language model trained by OpenAI. You answer as concisely as possible for each responseIf you are generating a list, do not have too many items.
Current date: ${new Date().toISOString()}\n\n`
})
```

Note that we automatically handle appending the previous messages to the prompt and attempt to optimize for the available tokens (which defaults to `4096`).

<details>
<summary>Usage in CommonJS (Dynamic import)</summary>

```js
async function example() {
  // To use ESM in CommonJS, you can use a dynamic import
  const { ChatGPTAPI } = await import('chatgpt')

  const api = new ChatGPTAPI({ apiKey: process.env.OPENAI_API_KEY })

  const res = await api.sendMessage('Hello World!')
  console.log(res.text)
}
```

</details>

### Usage - ChatGPTUnofficialProxyAPI

The API is almost exactly the same for the `ChatGPTUnofficialProxyAPI`; you just need to provide a ChatGPT `accessToken` instead of an OpenAI API key.

```ts
import { ChatGPTUnofficialProxyAPI } from 'chatgpt'

async function example() {
  const api = new ChatGPTUnofficialProxyAPI({
    accessToken: process.env.OPENAI_ACCESS_TOKEN
  })

  const res = await api.sendMessage('Hello World!')
  console.log(res.text)
}
```

See [demos/demo-reverse-proxy](./demos/demo-reverse-proxy.ts) for a full example:

```bash
npx tsx demos/demo-reverse-proxy.ts
```

#### Reverse Proxy

You can override the reverse proxy by passing `apiReverseProxyUrl`:

```ts
const api = new ChatGPTUnofficialProxyAPI({
  accessToken: process.env.OPENAI_ACCESS_TOKEN,
  apiReverseProxyUrl: 'https://your-example-server.com/api/conversation'
})
```

Known reverse proxies run by community members include:

| Reverse Proxy URL                                | Author                                       | Rate Limits | Last Checked |
| ------------------------------------------------ | -------------------------------------------- | ----------- | ------------ |
| `https://chat.duti.tech/api/conversation`        | [@acheong08](https://github.com/acheong08)   | 50 req/min  | 2/19/2023    |
| `https://gpt.pawan.krd/backend-api/conversation` | [@PawanOsman](https://github.com/PawanOsman) | ?           | 2/19/2023    |

Note: info on how the reverse proxies work is not being published at this time in order to prevent OpenAI from disabling access.

#### Access Token

To use `ChatGPTUnofficialProxyAPI`, you'll need a ChatGPT access token. You can either:

1. Use [acheong08/OpenAIAuth](https://github.com/acheong08/OpenAIAuth), which is a python script to login and get an access token automatically. This works with email + password accounts (e.g., it does not support accounts where you auth via Microsoft / Google).

2. You can manually get an `accessToken` by logging in to the ChatGPT webapp and then opening `https://chat.openai.com/api/auth/session`, which will return a JSON object containing your `accessToken` string.

Access tokens last for ~8 hours (TODO: need to verify the exact TTL).

**Note**: using a reverse proxy will expose your access token to a third-party. There shouldn't be any adverse effects possible from this, but please consider the risks before using this method.

## Docs

See the [auto-generated docs](./docs/classes/ChatGPTAPI.md) for more info on methods and parameters.

## Demos

Most of the demos use `ChatGPTAPI`. It should be pretty easy to convert them to use `ChatGPTUnofficialProxyAPI` if you'd rather use that approach. The only thing that needs to change is how you initialize the api with an `accessToken` instead of an `apiKey`.

To run the included demos:

1. clone repo
2. install node deps
3. set `OPENAI_API_KEY` in .env

A [basic demo](./demos/demo.ts) is included for testing purposes:

```bash
npx tsx demos/demo.ts
```

A [demo showing on progress handler](./demos/demo-on-progress.ts):

```bash
npx tsx demos/demo-on-progress.ts
```

The on progress demo uses the optional `onProgress` parameter to `sendMessage` to receive intermediary results as ChatGPT is "typing".

A [conversation demo](./demos/demo-conversation.ts):

```bash
npx tsx demos/demo-conversation.ts
```

A [persistence demo](./demos/demo-persistence.ts) shows how to store messages in Redis for persistence:

```bash
npx tsx demos/demo-persistence.ts
```

Any [keyv adaptor](https://github.com/jaredwray/keyv) is supported for persistence, and there are overrides if you'd like to use a different way of storing / retrieving messages.

Note that persisting message is required for remembering the context of previous conversations beyond the scope of the current Node.js process, since by default, we only store messages in memory. Here's an [external demo](https://github.com/transitive-bullshit/chatgpt-twitter-bot/blob/main/src/index.ts#L86-L95) of using a completely custom database solution to persist messages.

**Note**: Persistence is handled automatically when using `ChatGPTUnofficialProxyAPI` because it is connecting indirectly to ChatGPT.

## Projects

All of these awesome projects are built using the `chatgpt` package. 🤯

- [Twitter Bot](https://github.com/transitive-bullshit/chatgpt-twitter-bot) powered by ChatGPT ✨
  - Mention [@ChatGPTBot](https://twitter.com/ChatGPTBot) on Twitter with your prompt to try it out
- [ChatGPT API Server](https://github.com/waylaidwanderer/node-chatgpt-api) - API server for this package with support for multiple OpenAI accounts, proxies, and load-balancing requests between accounts.
- [ChatGPT Prompts](https://github.com/pacholoamit/chatgpt-prompts) - A collection of 140+ of the best ChatGPT prompts from the community.
- [Lovelines.xyz](https://lovelines.xyz?ref=chatgpt-api)
- [Chrome Extension](https://github.com/gragland/chatgpt-everywhere) ([demo](https://twitter.com/gabe_ragland/status/1599466486422470656))
- [VSCode Extension #1](https://github.com/mpociot/chatgpt-vscode) ([demo](https://twitter.com/marcelpociot/status/1599180144551526400), [updated version](https://github.com/timkmecl/chatgpt-vscode), [marketplace](https://marketplace.visualstudio.com/items?itemName=timkmecl.chatgpt))
- [VSCode Extension #2](https://github.com/barnesoir/chatgpt-vscode-plugin) ([marketplace](https://marketplace.visualstudio.com/items?itemName=JayBarnes.chatgpt-vscode-plugin))
- [VSCode Extension #3](https://github.com/gencay/vscode-chatgpt) ([marketplace](https://marketplace.visualstudio.com/items?itemName=gencay.vscode-chatgpt))
- [VSCode Extension #4](https://github.com/dogukanakkaya/chatgpt-code-vscode-extension) ([marketplace](https://marketplace.visualstudio.com/items?itemName=dogukanakkaya.chatgpt-code))
- [Raycast Extension #1](https://github.com/abielzulio/chatgpt-raycast) ([demo](https://twitter.com/abielzulio/status/1600176002042191875))
- [Raycast Extension #2](https://github.com/domnantas/raycast-chatgpt)
- [Telegram Bot #1](https://github.com/realies/chatgpt-telegram-bot)
- [Telegram Bot #2](https://github.com/dawangraoming/chatgpt-telegram-bot)
- [Telegram Bot #3](https://github.com/RainEggplant/chatgpt-telegram-bot) (group privacy mode, ID-based auth)
- [Telegram Bot #4](https://github.com/ArdaGnsrn/chatgpt-telegram) (queue system, ID-based chat thread)
- [Deno Telegram Bot](https://github.com/Ciyou/chatbot-telegram)
- [Go Telegram Bot](https://github.com/m1guelpf/chatgpt-telegram)
- [Telegram Bot for YouTube Summaries](https://github.com/codextde/youtube-summary)
- [GitHub ProBot](https://github.com/oceanlvr/ChatGPTBot)
- [Discord Bot #1](https://github.com/onury5506/Discord-ChatGPT-Bot)
- [Discord Bot #2](https://github.com/Nageld/ChatGPT-Bot)
- [Discord Bot #3](https://github.com/leinstay/gptbot)
- [Discord Bot #4 (selfbot)](https://github.com/0x7030676e31/cumsocket)
- [Discord Bot #5](https://github.com/itskdhere/ChatGPT-Discord-BOT)
- [Discord Bot #6 (Shakespeare bot)](https://gist.github.com/TheBrokenRail/4b37e7c44e8f721d8bd845050d034c16)
- [WeChat Bot #1](https://github.com/AutumnWhj/ChatGPT-wechat-bot)
- [WeChat Bot #2](https://github.com/fuergaosi233/wechat-chatgpt)
- [WeChat Bot #3](https://github.com/wangrongding/wechat-bot)
- [WeChat Bot #4](https://github.com/darknightlab/wechat-bot)
- [WeChat Bot #5](https://github.com/sunshanpeng/wechaty-chatgpt)
- [QQ Bot (plugin for Yunzai-bot)](https://github.com/ikechan8370/chatgpt-plugin)
- [QQ Bot (plugin for KiviBot)](https://github.com/KiviBotLab/kivibot-plugin-chatgpt)
- [QQ Bot (oicq)](https://github.com/easydu2002/chat_gpt_oicq)
- [QQ Bot (oicq + RabbitMQ)](https://github.com/linsyking/ChatGPT-QQBot)
- [QQ Bot (go-cqhttp)](https://github.com/PairZhu/ChatGPT-QQRobot)
- [EXM smart contracts](https://github.com/decentldotland/molecule)
- [Flutter ChatGPT API](https://github.com/coskuncay/flutter_chatgpt_api)
- [Carik Bot](https://github.com/luridarmawan/Carik)
- [Github Action for reviewing PRs](https://github.com/kxxt/chatgpt-action/)
- [WhatsApp Bot #1](https://github.com/pascalroget/whatsgpt) (multi-user support)
- [WhatsApp Bot #2](https://github.com/amosayomide05/chatgpt-whatsapp-bot)
- [WhatsApp Bot #3](https://github.com/navopw/whatsapp-chatgpt)
- [WhatsApp Bot #4](https://github.com/noelzappy/chatgpt-whatsapp) (schedule periodic messages)
- [WhatsApp Bot #5](https://github.com/hujanais/bs-chat-gpt3-api) (RaspberryPi + ngrok + Twilio)
- [Matrix Bot](https://github.com/matrixgpt/matrix-chatgpt-bot)
- [Rental Cover Letter Generator](https://sharehouse.app/ai)
- [Assistant CLI](https://github.com/diciaup/assistant-cli)
- [Teams Bot](https://github.com/formulahendry/chatgpt-teams-bot)
- [Askai](https://github.com/yudax42/askai)
- [TalkGPT](https://github.com/ShadovvBeast/TalkGPT)
- [ChatGPT With Voice](https://github.com/thanhsonng/chatgpt-voice)
- [iOS Shortcut](https://github.com/leecobaby/shortcuts/blob/master/other/ChatGPT_EN.md)
- [Slack Bot #1](https://github.com/trietphm/chatgpt-slackbot/)
- [Slack Bot #2](https://github.com/lokwkin/chatgpt-slackbot-node/) (with queueing mechanism)
- [Slack Bot #3](https://github.com/NessunKim/slack-chatgpt/)
- [Slack Bot #4](https://github.com/MarkusGalant/chatgpt-slackbot-serverless/) ( Serverless AWS Lambda )
- [Electron Bot](https://github.com/ShiranAbir/chaty)
- [Kodyfire CLI](https://github.com/nooqta/chatgpt-kodyfire)
- [Twitch Bot](https://github.com/BennyDeeDev/chatgpt-twitch-bot)
- [Continuous Conversation](https://github.com/DanielTerletzkiy/chat-gtp-assistant)
- [Figma plugin](https://github.com/frederickk/chatgpt-figma-plugin)
- [NestJS server](https://github.com/RusDyn/chatgpt_nestjs_server)
- [NestJS ChatGPT Starter Boilerplate](https://github.com/mitkodkn/nestjs-chatgpt-starter)
- [Wordsmith: Add-in for Microsoft Word](https://github.com/xtremehpx/Wordsmith)
- [QuizGPT: Create Kahoot quizzes with ChatGPT](https://github.com/Kladdy/quizgpt)
- [openai-chatgpt: Talk to ChatGPT from the terminal](https://github.com/gmpetrov/openai-chatgpt)
- [Clippy the Saleforce chatbot](https://github.com/sebas00/chatgptclippy) ClippyJS joke bot
- [ai-assistant](https://github.com/youking-lib/ai-assistant) Chat assistant
- [Feishu Bot](https://github.com/linjungz/feishu-chatgpt-bot)

If you create a cool integration, feel free to open a PR and add it to the list.

## Compatibility

- This package is ESM-only.
- This package supports `node >= 14`.
- This module assumes that `fetch` is installed.
  - In `node >= 18`, it's installed by default.
  - In `node < 18`, you need to install a polyfill like `unfetch/polyfill` ([guide](https://github.com/developit/unfetch#usage-as-a-polyfill)) or `isomorphic-fetch` ([guide](https://github.com/matthew-andrews/isomorphic-fetch#readme)).
- If you want to build a website using `chatgpt`, we recommend using it only from your backend API

## Credits

- Huge thanks to [@waylaidwanderer](https://github.com/waylaidwanderer), [@abacaj](https://github.com/abacaj), [@wong2](https://github.com/wong2), [@simon300000](https://github.com/simon300000), [@RomanHotsiy](https://github.com/RomanHotsiy), [@ElijahPepe](https://github.com/ElijahPepe), and all the other contributors 💪
- The original browser version was inspired by this [Go module](https://github.com/danielgross/whatsapp-gpt) by [Daniel Gross](https://github.com/danielgross)
- [OpenAI](https://openai.com) for creating [ChatGPT](https://openai.com/blog/chatgpt/) 🔥

## License

MIT © [Travis Fischer](https://transitivebullsh.it)

If you found this project interesting, please consider [sponsoring me](https://github.com/sponsors/transitive-bullshit) or <a href="https://twitter.com/transitive_bs">following me on twitter <img src="https://storage.googleapis.com/saasify-assets/twitter-logo.svg" alt="twitter" height="24px" align="center"></a>
