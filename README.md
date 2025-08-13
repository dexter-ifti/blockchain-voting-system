
# 🗳️ Blockchain Voting System

A secure and transparent decentralized voting system built using **Ethereum**, **Smart Contracts**, **React**, **JavaScript**, and **Node.js**. This application ensures tamper-proof and verifiable voting, making it ideal for modern digital election use cases.

---

## 🚀 Features

- ✅ Ethereum-powered smart contract for vote recording
- ✅ MetaMask wallet integration for user authentication
- ✅ Real-time vote count display
- ✅ React-based frontend for a responsive and interactive UI
- ✅ Node.js and JavaScript used to handle blockchain interactions

---

## 🔧 Tech Stack

- **Frontend**: React.js, Tailwind CSS
- **Blockchain**: Solidity Smart Contract (Ethereum)
- **Wallet Integration**: MetaMask
- **Backend**: Node.js, Web3.js / Ethers.js
- **Smart Contract Platform**: Ganache (local testing) / Remix IDE
- **Deployment Consideration**: Can be deployed to Ethereum Testnet (e.g., Goerli)

---

## 🖼️ Screenshots


![image](https://github.com/user-attachments/assets/5814d190-2d02-4c3f-a5d3-d194e6326fe7)
![image](https://github.com/user-attachments/assets/530937f1-a503-41f3-922f-2b4f5d4ad237)
![image](https://github.com/user-attachments/assets/c7ad28d2-4573-41a7-b5f6-54aea8813a36)



---

## 💡 How It Works

1. Connect your MetaMask wallet
2. Smart contract gets deployed on Ethereum
3. Users can cast one vote using their wallet address (one vote per address)
4. Votes are recorded on the blockchain
5. Real-time results can be viewed through the frontend

---

## 🛠 Installation

```bash
git clone https://github.com/dexter-ifti/blockchain-voting-system.git
cd blockchain-voting-system
npm install both in frontend and backend you have to install npm insatll for installing packages 

---

## 👥 Git Workflow for Collaboration

To avoid conflicts and keep our main branch stable, **never commit directly to `master`**. Always work in your own branch.

### 1️⃣ Clone the repository (first time only)

```bash
git clone https://github.com/dexter-ifti/blockchain-voting-system.git
cd blockchain-voting-system
```

### 2️⃣ Create and switch to your personal branch

Replace `your-name` with your actual name or feature name.

```bash
git checkout -b your-name
```

Example:

```bash
git checkout -b alice-feature-voting-ui
```

### 3️⃣ Work on your branch

* Make code changes in your branch only.
* Frequently save your work:

```bash
git add .
git commit -m "Meaningful commit message here"
```

### 4️⃣ Push your branch to GitHub

```bash
git push origin your-name
```

### 5️⃣ Update your branch with latest changes from `main`

Before continuing work, make sure your branch has the latest updates:

```bash
git checkout main
git pull origin main
git checkout your-name
git merge main
```

### 6️⃣ Create a Pull Request (PR)

When your feature is ready:

1. Push your branch.
2. Go to GitHub and open a **Pull Request** from your branch → `main`.
3. Wait for review and approval before merging.

---