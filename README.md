
# 🚀 Gensyn AI RL-Swarm Node Setup Guide

This guide helps you install and run the [Gensyn AI](https://github.com/gensyn-ai/rl-swarm) `rl-swarm` node with proper environment setup, including Python, Node.js, Yarn, CUDA, and system diagnostics.

---

## 📦 Prerequisites

Make sure you're on **Ubuntu 22.04+**.

32GB ram 

100gb storage

4 cores CPU

---

## 🔧 System Dependencies

```bash
sudo apt update && sudo apt install -y sudo

sudo apt update && sudo apt install -y python3 python3-venv python3-pip curl wget screen git lsof nano unzip iproute2 build-essential gcc g++

[ -f cuda.sh ] && rm cuda.sh; curl -o cuda.sh https://raw.githubusercontent.com/zunxbt/gensyn-testnet/main/cuda.sh && chmod +x cuda.sh && . ./cuda.sh
```


```bash
sudo apt update && sudo apt install -y python3 python3-venv python3-pip curl wget screen git lsof

curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -

sudo apt update && sudo apt install -y nodejs

curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -

echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list

sudo apt update && sudo apt install -y yarn
```

---


## 🧪 Verify Versions

```bash
node -v
npm -v
yarn -v
python3 --version
```

---

## 🧵 Launch in Screen Session

```bash
screen -S gensyn
```

---

## ⬇️ Clone and Set Up Project

```bash
git clone https://github.com/gensyn-ai/rl-swarm.git
cd rl-swarm
python3 -m venv .venv
source .venv/bin/activate
```

---

## 📦 Install & Upgrade Modal Login Dependencies

```bash
cd modal-login
yarn install
yarn upgrade
yarn add next@latest
yarn add viem@latest
cd ..
```

---

## 📥 Update to Specific Gensyn Version

```bash
# 1. Go to your repo folder
cd ~/rl-swarm

# 2. Fetch all latest tags from remote
git fetch --all --tags

# 3. Hard reset to latest main (or origin/main)
git reset --hard origin/main

# 4. Checkout to specific version (example: v0.5.4)
git checkout tags/v0.5.4 -b v0.5.4-branch
```



if you a old user so please copy your swarm.pem file to vps rl-swarm folder and use below command to start your node 

---

## ▶️ Run Node

```bash
cd $HOME/rl-swarm/
./run_rl_swarm.sh
```

## ▶️ open same vps with new terminal 

```bash
npx localtunnel --port 3000
```

When web ask for pass then give your ip in the password box 
Verify your email done enjoy 


---


## 🧼 Fix for Timeout Bug

```bash
sed -i 's/startup_timeout: float = *15/startup_timeout: float = 120/' ~/rl-swarm/.venv/lib/python3.12/site-packages/hivemind/p2p/p2p_daemon.py
```

---

## 🧪 Final Setup Commands if you face any terminate issue 

```bash
source ~/rl-swarm/.venv/bin/activate
cd ~/rl-swarm/genrl-swarm
pip install -e .
```

```bash
chmod 600 ~/rl-swarm/swarm.pem
cd ~/rl-swarm/
./run_rl_swarm.sh
```



# 🚀 Upgrade your node to New Release (v0.5.4)

---

### 🖥️ Step 1: Goto screen session

```bash
screen -r gensyn
```

---

### 🛑 Step 2: Stop your node

> Press `Ctrl + C` inside the screen to stop the running node.

---

### 📂 Step 3: Navigate to the `rl-swarm` directory

```bash
cd rl-swarm
```

---

### 🔄 Step 4: Pull the latest release

```bash
git switch main
git reset --hard
git clean -fd
git pull origin main
```

---

### 🧠 Step 5: Start the swarm node

```bash
python3 -m venv .venv
source .venv/bin/activate
./run_rl_swarm.sh
```

---

## 🧠 Notes

- To **detach** from the screen: `Ctrl + A` then `D`
- To **re-attach** later: `screen -r gensyn`

---

## 🙌 Credits

- Original repo: [Gensyn AI](https://github.com/gensyn-ai/rl-swarm)
- credits goes to - https://t.me/andhiiTGkamaii
- Special thanks to https://t.me/vikkyvirus https://t.me/DeviL_The_Ghost https://t.me/Zeekeyt
