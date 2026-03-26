# GitPump

Launch Solana tokens on [Pump.fun](https://pump.fun) directly from GitHub issue comments.

GitPump is a GitHub bot that monitors issue comments for `/launch` commands and automatically generates an AI token logo, uploads metadata to IPFS, launches the token on Pump.fun, and replies with the contract address and transaction link.

---

## Usage

In any GitHub issue on a repository that has GitPump installed, leave a comment:

```
/launch name=MoonCoin symbol=MOON
```

The bot will reply within ~30 seconds with the contract address, token logo, Pump.fun link, and Solscan transaction link.

**Rate limit:** 1 launch per hour per GitHub user.

---

## Self-Hosting Setup

### Prerequisites

- GitHub account
- Solana wallet with some SOL (~0.03 SOL per launch to cover transaction fees)
- A server or cloud instance to run the bot

### Step 1: Clone the repository

```bash
git clone https://github.com/buildish-arc/gitpump.git
cd gitpump
pnpm install
```

### Step 2: Set environment variables

Create a `.env` file in the project root:

```env
GITHUB_TOKEN=           # GitHub Personal Access Token with repo scope
GITHUB_WEBHOOK_SECRET=  # Random secret string for webhook verification
SOLANA_PRIVATE_KEY=     # Solana wallet private key (base58 format)
OPENAI_API_KEY=         # OpenAI API key for token image generation
DATABASE_URL=           # PostgreSQL connection string
```

| Variable | How to obtain |
|---|---|
| `GITHUB_TOKEN` | [github.com/settings/tokens](https://github.com/settings/tokens/new) — enable `repo` scope |
| `GITHUB_WEBHOOK_SECRET` | Any random string — e.g. `openssl rand -hex 32` |
| `SOLANA_PRIVATE_KEY` | Phantom wallet: Settings → Security → Export Private Key |
| `OPENAI_API_KEY` | [platform.openai.com/api-keys](https://platform.openai.com/api-keys) |
| `DATABASE_URL` | PostgreSQL connection URL from your database provider |

### Step 3: Start the server

```bash
pnpm run build
pnpm run start
```

The server listens on port `8080`.

### Step 4: Create a GitHub webhook

1. In your GitHub repository, go to **Settings > Webhooks > Add webhook**
2. Configure:
   - **Payload URL:** `https://your-domain.com/webhook`
   - **Content type:** `application/json`
   - **Secret:** same value as `GITHUB_WEBHOOK_SECRET`
   - **Events:** select *Issue comments* only
3. Click **Add webhook**. A green checkmark confirms the connection.

### Step 5: Verify

Open any issue in your repository and post:

```
/launch name=TestCoin symbol=TEST
```

The bot should reply within ~30 seconds.

---

## Command reference

| Command | Example |
|---|---|
| `/launch name=<TokenName> symbol=<TICKER>` | `/launch name=MoonCoin symbol=MOON` |

- `name` — Token name, max 32 characters
- `symbol` — Ticker symbol, max 10 characters
- One launch per GitHub account per hour
- Malformed or incomplete commands are ignored

---

## Live feed

All tokens launched via GitPump are listed at [gitpump.com](https://gitpump.com).

---

## License

MIT
