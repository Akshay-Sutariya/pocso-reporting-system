# POCSO Shield — Blockchain-Based Anonymous Reporting System

> Hackathon project for Problem Statement 11: CyberTech Theme
> Anonymous · Immutable · AI-powered

---

## What this does

- Reporter submits a URL + description **with zero identity data**
- AI scores the report for risk (CRITICAL / HIGH / SUSPICIOUS / LOW)
- Evidence is pinned to **IPFS** (permanent, decentralised storage)
- Report hash + AI score is written to **Polygon blockchain** (immutable)
- High-risk reports (score ≥ 85) are **auto-escalated** by the smart contract
- Authorities see all reports on a dashboard with real blockchain links
- Every status update is also written to chain — full **audit trail**

---

## Project structure

```
pocso-shield/
├── contracts/ReportRegistry.sol   ← Solidity smart contract
├── scripts/deploy.js              ← Hardhat deploy script
├── hardhat.config.js
├── .env                           ← Root env (blockchain keys)
├── backend/
│   ├── index.js                   ← Express API server
│   ├── scorer.js                  ← AI risk engine
│   ├── ipfs.js                    ← Pinata IPFS helper
│   ├── contract.js                ← ethers.js wrapper
│   ├── seed.js                    ← Demo data seeder
│   └── .env                      ← Backend env (all API keys)
└── frontend/
    └── src/
        ├── App.jsx
        ├── pages/
        │   ├── ReportPage.jsx     ← Anonymous reporter portal
        │   └── DashboardPage.jsx  ← Authority case management
        └── components/
            ├── ZKPAnimation.jsx
            ├── RiskBadge.jsx
            ├── StatusBadge.jsx
            ├── AuditTrail.jsx
            └── StatsBar.jsx
```

---

## Full setup guide (follow in order)

### STEP 1 — Install Node.js

1. Go to https://nodejs.org
2. Download **LTS version** (v20 or higher)
3. Install it (just click Next through the installer)
4. Open a terminal and verify:
   ```
   node --version   # should show v20.x.x
   npm --version    # should show 10.x.x
   ```

---

### STEP 2 — Get a free Alchemy account (blockchain RPC)

1. Go to https://dashboard.alchemy.com
2. Sign up for free
3. Click **"Create new app"**
4. Set Chain: **Polygon**, Network: **Polygon Mumbai**
5. Give it any name (e.g. "pocso-shield")
6. Click the app → click **"API Key"**
7. Copy the **HTTPS** URL — looks like:
   `https://polygon-mumbai.g.alchemy.com/v2/abc123xyz...`
8. Save this — you'll need it in both `.env` files

---

### STEP 3 — Create a test wallet on MetaMask

> **IMPORTANT: Create a brand new wallet just for this hackathon. Never use your real wallet.**

1. Install MetaMask browser extension: https://metamask.io
2. Create a new wallet (save the seed phrase somewhere)
3. Switch network to **Polygon Mumbai**:
   - Click the network dropdown → Add network
   - Network name: Mumbai
   - RPC URL: https://rpc-mumbai.maticvigil.com
   - Chain ID: 80001
   - Symbol: MATIC
4. **Export your private key**:
   - Click account icon → Account Details → Export Private Key
   - Enter your password → copy the key (starts with 0x...)
5. **Get free test MATIC** (you need this to pay gas fees):
   - Go to https://mumbaifaucet.com
   - Paste your wallet address → click Get MATIC
   - Wait 1 minute — you'll see 0.5 MATIC in your wallet
   - Do this 2-3 times to get enough for testing

---

### STEP 4 — Get free Pinata API keys (IPFS)

1. Go to https://app.pinata.cloud
2. Sign up for free
3. Click **"API Keys"** in the left sidebar
4. Click **"New Key"**
5. Enable: **pinFileToIPFS** and **pinJSONToIPFS**
6. Give it a name → click Generate
7. **Copy both the API Key and API Secret** right now — the secret won't show again

---

### STEP 5 — Get free HuggingFace token (AI)

1. Go to https://huggingface.co
2. Sign up for free
3. Click your profile → Settings → **Access Tokens**
4. Click **"New token"**
5. Name it anything, select **"read"** role
6. Copy the token (starts with `hf_...`)

---

### STEP 6 — Fill in your .env files

**File 1: `pocso-shield/.env`** (root)
```
POLYGON_RPC_URL=https://polygon-mumbai.g.alchemy.com/v2/YOUR_KEY
DEPLOYER_PRIVATE_KEY=0xYOUR_PRIVATE_KEY
```

**File 2: `pocso-shield/backend/.env`**
```
POLYGON_RPC_URL=https://polygon-mumbai.g.alchemy.com/v2/YOUR_KEY
DEPLOYER_PRIVATE_KEY=0xYOUR_PRIVATE_KEY
CONTRACT_ADDRESS=         ← leave blank for now, filled after deploy
PINATA_API_KEY=your_pinata_api_key
PINATA_SECRET=your_pinata_secret
HUGGINGFACE_TOKEN=hf_your_token
PORT=4000
```

---

### STEP 7 — Install dependencies and deploy smart contract

Open a terminal. Run these commands one by one:

```bash
# 1. Go into the project root
cd pocso-shield

# 2. Install Hardhat and blockchain tools
npm install

# 3. Compile the smart contract
npm run compile
# You should see: "Compiled 1 Solidity file successfully"

# 4. Deploy to Polygon Mumbai
npm run deploy:mumbai
# You should see:
# Deploying ReportRegistry to mumbai ...
# ReportRegistry deployed to: 0xABC123...
# Contract address written to backend/.env
```

After deploy, open `backend/.env` and confirm `CONTRACT_ADDRESS` was auto-filled.

---

### STEP 8 — Start the backend

```bash
# Open a NEW terminal tab

cd pocso-shield/backend

# Install backend dependencies
npm install

# Start the server
npm run dev

# You should see:
# POCSO Shield backend running on http://localhost:4000
# Contract: 0xABC123...
```

Test it's working: open http://localhost:4000/health in your browser.
You should see: `{"status":"ok","contract":"0xABC...","network":"Polygon Mumbai"}`

---

### STEP 9 — Start the frontend

```bash
# Open a THIRD terminal tab

cd pocso-shield/frontend

# Install frontend dependencies
npm install

# Start the React app
npm start

# Browser will open at http://localhost:3000
```

---

### STEP 10 — Seed demo data (for your dashboard demo)

```bash
# In a fourth terminal tab (while backend is running)

cd pocso-shield/backend
node seed.js

# This submits 6 test reports with varied risk scores
# Each takes ~5 seconds (blockchain confirmation)
# Takes about 2 minutes total
```

After seeding, open http://localhost:3000 → switch to Authority Dashboard.
You should see all 6 reports with different risk scores.

---

## Demo flow (practice this 3 times)

1. Open http://localhost:3000
2. On the **Report** page:
   - Paste any URL
   - Type a description mentioning "child abuse" (triggers high score)
   - Click "Submit anonymous report"
   - Watch the ZKP animation play
   - See the Polygonscan link and IPFS link on the receipt
3. Open the Polygonscan link — show judges the **real transaction**
4. Switch to **Authority Dashboard**
5. Show your new report appeared — with AI score and auto-escalation flag
6. Change a report's status from Pending → Resolved
7. Click on any row → show the **audit trail** with blockchain block numbers

---

## Key talking points for judges

- **Privacy**: Reporter submits with zero identity. No IP, no email, no login.
- **Immutability**: Smart contract auto-escalates critical reports — no human can suppress them.
- **Audit trail**: Every status change is on-chain. Officers cannot secretly close cases.
- **AI + blockchain**: AI scores immediately, blockchain stores permanently.
- **IPFS**: Evidence is content-addressed — the CID changes if anyone tampers with it.
- **ZKP**: In production this uses Groth16 proofs. The animation shows the real proof steps.

---

## Troubleshooting

**"ABI not found" error**: Run `npm run compile` in the root folder first.

**"Contract not deployed" error**: Check `CONTRACT_ADDRESS` in `backend/.env`.

**Transaction failing**: Get more test MATIC from https://mumbaifaucet.com.

**Pinata errors**: Double-check API key and secret in `backend/.env`.

**Frontend can't reach backend**: Make sure backend is running on port 4000, and no firewall is blocking it.
