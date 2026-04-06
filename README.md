# 🛡️ POCSO Shield

**Blockchain-based Anonymous Reporting System for Illegal Online Content**

> Built for Hackathon — Problem Statement 11 | CyberTech Theme

[![Node.js](https://img.shields.io/badge/Node.js-v20+-green?logo=node.js)](https://nodejs.org)
[![Solidity](https://img.shields.io/badge/Solidity-0.8.19-blue?logo=ethereum)](https://soliditylang.org)
[![React](https://img.shields.io/badge/React-18-61DAFB?logo=react)](https://reactjs.org)
[![Polygon](https://img.shields.io/badge/Network-Polygon%20Amoy-purple?logo=polygon)](https://polygon.technology)
[![IPFS](https://img.shields.io/badge/Storage-IPFS%20via%20Pinata-65C2CB)](https://pinata.cloud)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow)](LICENSE)

---

## 📖 What is POCSO Shield?

POCSO Shield lets anyone report illegal online content (like CSAM) **completely anonymously**. There is no login, no email, no identity — just a URL and a description.

Here's what happens when a report is submitted:

1. **AI scores** the report for risk: `CRITICAL / HIGH / SUSPICIOUS / LOW`
2. **Evidence is pinned to IPFS** — permanent, decentralised, tamper-proof storage
3. **Report hash + AI score is written to the Polygon blockchain** — immutable and public
4. **High-risk reports (score ≥ 85) are auto-escalated** by the smart contract — no human can suppress them
5. **Authorities review all reports** on a dashboard with real blockchain audit trails

There is also a **Chrome browser extension** that warns users in real-time when they visit a domain flagged for illegal content.

---

## 🏗️ Architecture Overview

```
Reporter (Anonymous)
        │
        ▼
  [ React Frontend ]  ──────────────────────────────────────────┐
  localhost:3000                                                 │
        │                                                        │
        ▼                                                        │
  [ Node.js / Express Backend ]                         [ Chrome Extension ]
  localhost:4000                                        Checks blocklist &
        │                                               warns user in browser
        ├──► [ HuggingFace AI ]  ← scores the report
        │
        ├──► [ Pinata / IPFS ]   ← stores evidence permanently
        │
        └──► [ Polygon Blockchain ]  ← stores report hash + score immutably
                    │
                    ▼
          [ Smart Contract ]  ← auto-escalates critical reports
          ReportRegistry.sol    immutable audit trail on-chain
```

---

## 📁 Project Structure

```
pocso-shield/
│
├── contracts/
│   └── ReportRegistry.sol       ← Solidity smart contract (the core logic)
│
├── scripts/
│   └── deploy.js                ← Deploys the smart contract to blockchain
│
├── hardhat.config.js            ← Blockchain network configuration
├── .env                         ← Root environment file (blockchain keys)
├── package.json                 ← Root project config (Hardhat tools)
│
├── backend/
│   ├── index.js                 ← Main Express API server
│   ├── scorer.js                ← AI risk scoring engine
│   ├── ipfs.js                  ← Pinata/IPFS file upload helper
│   ├── contract.js              ← Talks to blockchain via ethers.js
│   ├── moderator.js             ← Content moderation logic
│   ├── threatDetector.js        ← Threat analysis
│   ├── blocklist.js             ← Maintains flagged domain list
│   ├── crypto.js                ← Encryption utilities
│   ├── seed.js                  ← Loads demo data for testing
│   ├── abi.json                 ← Smart contract interface
│   ├── package.json             ← Backend dependencies
│   └── .env                     ← Backend environment file (all API keys)
│
├── frontend/
│   ├── public/index.html
│   └── src/
│       ├── App.jsx              ← Main React app
│       ├── pages/
│       │   ├── ReportPage.jsx   ← Anonymous reporter portal
│       │   └── DashboardPage.jsx ← Authority case management dashboard
│       ├── components/
│       │   ├── ZKPAnimation.jsx ← Zero-knowledge proof animation
│       │   ├── RiskBadge.jsx    ← Risk level badge component
│       │   ├── StatusBadge.jsx  ← Report status badge
│       │   ├── AuditTrail.jsx   ← Blockchain audit trail viewer
│       │   └── StatsBar.jsx     ← Dashboard statistics bar
│       └── utils/
│           └── encryption.js   ← Client-side encryption helpers
│
└── extension/
    ├── manifest.json            ← Chrome extension configuration
    ├── background.js            ← Background service worker
    ├── content.js               ← Injected into web pages
    ├── popup.html               ← Extension popup UI
    └── warning.html             ← Warning page shown on flagged sites
```

---

## 🚀 Complete Setup Guide (Beginner Friendly)

> **Estimated time:** 30–45 minutes for first-time setup
> **Difficulty:** Beginner — follow each step in order, don't skip any

---

### ✅ Prerequisites — What you need before starting

| Tool | Minimum Version | Check Command |
|------|----------------|---------------|
| Node.js | v20 or higher | `node --version` |
| npm | v10 or higher | `npm --version` |
| Git | Any version | `git --version` |
| A browser | Chrome recommended | — |

---

### STEP 1 — Install Node.js

1. Go to [https://nodejs.org](https://nodejs.org)
2. Download the **LTS version** (v20 or higher)
3. Run the installer — click "Next" through all screens
4. Open a **terminal** (Command Prompt on Windows, Terminal on Mac/Linux)
5. Verify the installation:

```bash
node --version    # should print something like: v20.11.0
npm --version     # should print something like: 10.2.4
```

> **Windows tip:** Search "cmd" in the Start menu to open a terminal.
> **Mac tip:** Press `Cmd + Space`, type "Terminal", press Enter.

---

### STEP 2 — Clone / Download the Project

If you downloaded the `.rar` file, extract it and navigate into the folder:

```bash
cd pocso-shield-final/pocso-shield
```

Or if cloning from GitHub:

```bash
git clone https://github.com/YOUR_USERNAME/pocso-shield.git
cd pocso-shield
```

---

### STEP 3 — Create a Free Alchemy Account (Blockchain RPC)

You need Alchemy to connect to the Polygon blockchain.

1. Go to [https://dashboard.alchemy.com](https://dashboard.alchemy.com)
2. Sign up for free (no credit card needed)
3. Click **"Create new app"**
4. Select:
   - Chain: **Polygon**
   - Network: **Polygon Amoy** (the test network)
   - Name: anything (e.g. `pocso-shield`)
5. Open the app → click **"API Key"**
6. Copy the **HTTPS URL** — it looks like:
   ```
   https://polygon-amoy.g.alchemy.com/v2/abc123xyz...
   ```
7. Save this URL — you'll need it in **Step 7**

---

### STEP 4 — Create a Test Wallet on MetaMask

> ⚠️ **IMPORTANT:** Create a **brand new wallet** just for this project. Never use your real personal wallet for development.

1. Install the MetaMask browser extension: [https://metamask.io](https://metamask.io)
2. Click "Create a new wallet" and save your seed phrase somewhere safe
3. Add the **Polygon Amoy** test network:
   - Click the network dropdown (top of MetaMask) → "Add network" → "Add a network manually"
   - Fill in:
     | Field | Value |
     |-------|-------|
     | Network name | Polygon Amoy |
     | RPC URL | https://rpc-amoy.polygon.technology |
     | Chain ID | 80002 |
     | Symbol | MATIC |
     | Block explorer | https://amoy.polygonscan.com |
   - Click Save
4. **Export your private key:**
   - Click the circle icon (top right) → Account Details → Export Private Key
   - Enter your MetaMask password → copy the key (starts with `0x...`)
5. **Get free test MATIC** (needed to pay gas fees on blockchain):
   - Go to [https://faucet.polygon.technology](https://faucet.polygon.technology)
   - Select **Amoy** network, paste your wallet address → request tokens
   - Alternatively try: [https://www.alchemy.com/faucets/polygon-amoy](https://www.alchemy.com/faucets/polygon-amoy)
   - Wait 1–2 minutes — you'll see test MATIC appear in MetaMask

---

### STEP 5 — Get Free Pinata API Keys (IPFS Storage)

Pinata stores your evidence files permanently on IPFS.

1. Go to [https://app.pinata.cloud](https://app.pinata.cloud)
2. Sign up for free
3. Click **"API Keys"** in the left sidebar
4. Click **"+ New Key"**
5. Enable both: **pinFileToIPFS** and **pinJSONToIPFS**
6. Give it any name → click **Generate**
7. **Copy both values immediately** — the Secret is only shown once:
   - `API Key` (a short string)
   - `API Secret` (a long string)

---

### STEP 6 — Get a Free HuggingFace Token (AI Scoring)

HuggingFace powers the AI risk scoring.

1. Go to [https://huggingface.co](https://huggingface.co)
2. Sign up for free
3. Click your profile photo → **Settings** → **Access Tokens** (in left sidebar)
4. Click **"New token"**
5. Name it anything, choose **"read"** permission
6. Copy the token — it starts with `hf_...`

---

### STEP 7 — Fill in the Environment Files

You have **two** `.env` files to fill in. Open them in any text editor (Notepad, VS Code, etc.).

**File 1: `pocso-shield/.env`** (in the root folder)

```env
POLYGON_RPC_URL=https://polygon-amoy.g.alchemy.com/v2/YOUR_ALCHEMY_KEY
DEPLOYER_PRIVATE_KEY=0xYOUR_METAMASK_PRIVATE_KEY
```

**File 2: `pocso-shield/backend/.env`**

```env
POLYGON_RPC_URL=https://polygon-amoy.g.alchemy.com/v2/YOUR_ALCHEMY_KEY
DEPLOYER_PRIVATE_KEY=0xYOUR_METAMASK_PRIVATE_KEY
CONTRACT_ADDRESS=                    ← leave this blank for now!
PINATA_API_KEY=your_pinata_api_key
PINATA_SECRET=your_pinata_api_secret
HUGGINGFACE_TOKEN=hf_your_token_here
PORT=4000
```

> **Tip:** Replace only the placeholder values. Keep the `=` sign and don't add spaces around it.

---

### STEP 8 — Install Root Dependencies & Deploy Smart Contract

Open a terminal in the `pocso-shield` folder and run these commands **one by one**:

```bash
# Step 8a: Install Hardhat and blockchain tools
npm install
```

> You'll see a lot of packages being downloaded. This is normal. Wait for it to finish.

```bash
# Step 8b: Compile the smart contract
npm run compile
```

> ✅ You should see: `Compiled 1 Solidity file successfully`

```bash
# Step 8c: Deploy the smart contract to Polygon Amoy
npm run deploy:mumbai
```

> ✅ You should see something like:
> ```
> Deploying ReportRegistry to mumbai ...
> ReportRegistry deployed to: 0xABC123def456...
> Contract address written to backend/.env
> ```

After this step, open `backend/.env` — you'll see `CONTRACT_ADDRESS` has been auto-filled with the deployed contract address. ✅

---

### STEP 9 — Start the Backend Server

> Open a **new terminal window/tab** for this step (keep the previous one open)

```bash
# Go into the backend folder
cd pocso-shield/backend

# Install backend dependencies
npm install

# Start the server in development mode
npm run dev
```

> ✅ You should see:
> ```
> POCSO Shield backend running on http://localhost:4000
> Contract: 0xABC123...
> ```

**Test that it works:** Open [http://localhost:4000/health](http://localhost:4000/health) in your browser.

You should see: `{"status":"ok","contract":"0xABC...","network":"Polygon Amoy"}`

> If the terminal shows an error, double-check your `backend/.env` values.

---

### STEP 10 — Start the Frontend

> Open a **third terminal window/tab** for this step

```bash
# Go into the frontend folder
cd pocso-shield/frontend

# Install frontend dependencies
npm install

# Start the React app
npm start
```

> ✅ Your browser should automatically open at [http://localhost:3000](http://localhost:3000)

If it doesn't open automatically, go to [http://localhost:3000](http://localhost:3000) manually.

---

### STEP 11 — Load Demo Data (Optional but Recommended)

This fills your dashboard with sample reports to show the judges.

> Open a **fourth terminal window/tab** (while backend is still running in Step 9)

```bash
cd pocso-shield/backend
node seed.js
```

> ✅ This submits 6 test reports with different risk scores. Each takes ~5 seconds (blockchain confirmation time). Total: ~2 minutes.

After it finishes, open [http://localhost:3000](http://localhost:3000) → switch to **Authority Dashboard** → you should see all 6 reports.

---

### STEP 12 — Install the Chrome Extension (Optional)

1. Open Chrome → go to `chrome://extensions`
2. Enable **Developer Mode** (toggle in the top right)
3. Click **"Load unpacked"**
4. Select the `pocso-shield/extension` folder
5. The POCSO Shield extension icon should appear in your browser toolbar ✅

---

## 🎬 Demo Flow (Practice This 3 Times Before Presenting!)

1. Open [http://localhost:3000](http://localhost:3000)
2. On the **Report page:**
   - Paste any URL (e.g. a fake test URL)
   - Type a description mentioning `child abuse` (this triggers a high-risk AI score)
   - Click **"Submit anonymous report"**
   - Watch the Zero-Knowledge Proof animation play
   - A receipt appears with a **Polygonscan link** and an **IPFS link**
3. Click the Polygonscan link → show judges the **real on-chain transaction** ✅
4. Switch to the **Authority Dashboard**
5. Show your new report appeared — with AI score and auto-escalation flag
6. Change a report status from `Pending` → `Resolved`
7. Click any report row → show the **audit trail** with real blockchain block numbers

---

## 🗝️ Key Talking Points for Judges

| Feature | What to say |
|---------|-------------|
| **Zero Identity** | Reporter submits with no login, no email, no IP logging |
| **Immutability** | Smart contract auto-escalates critical reports — no human can suppress them |
| **Audit Trail** | Every status change is recorded on-chain. Officers cannot secretly close cases |
| **AI + Blockchain** | AI scores immediately, blockchain stores the result permanently |
| **IPFS Evidence** | Evidence is content-addressed — if anyone tampers with it, the hash changes |
| **ZKP** | In production, Groth16 proofs are used — the animation shows the real proof steps |

---

## 🛠️ Troubleshooting

| Error | Fix |
|-------|-----|
| `ABI not found` | Run `npm run compile` in the root folder first |
| `Contract not deployed` | Check `CONTRACT_ADDRESS` in `backend/.env` is filled |
| Transaction failing | Get more test MATIC from the Polygon faucet |
| Pinata errors | Double-check `PINATA_API_KEY` and `PINATA_SECRET` in `backend/.env` |
| Frontend can't reach backend | Make sure backend is running on port 4000 in a separate terminal |
| `npm install` fails | Try deleting `node_modules` folder and `package-lock.json`, then run `npm install` again |
| Port 3000 already in use | React will ask to use port 3001 — press `Y` |
| Port 4000 already in use | Change `PORT=4001` in `backend/.env` and update API URL in frontend |

---

## 🧰 Tech Stack Summary

| Layer | Technology |
|-------|-----------|
| Frontend | React 18, plain CSS |
| Backend | Node.js, Express |
| Blockchain | Solidity 0.8.19, Hardhat, ethers.js v6 |
| Network | Polygon Amoy Testnet |
| Storage | IPFS via Pinata |
| AI Scoring | HuggingFace Inference API |
| Browser Extension | Chrome Extension Manifest v3 |

---

## 👥 Team

Built with ❤️ for Hackathon PS11 — CyberTech Theme
