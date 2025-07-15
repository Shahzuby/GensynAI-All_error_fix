
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
git reset --hard
git pull origin main
git checkout tags/v0.5.4
git describe --tags
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

## and if your node terminate then use below commands for fix 👇

---

## 🧠 Patch System Diagnostics

### Edit `system_utils.py` for enhanced logging:

```bash
nano genrl-swarm/src/genrl_swarm/logging_utils/system_utils.py
```

Delete All text from nano and paste this full system info utility (already enhanced):  
```bash
import platform
import subprocess
import sys
import os
from shutil import which

import psutil

DIVIDER = "[---------] SYSTEM INFO [---------]"


def get_system_info():
    lines = ['\n']
    lines.append(DIVIDER)
    lines.append("")
    lines.append("Python Version:")
    lines.append(f"  {sys.version}")

    lines.append("\nPlatform Information:")
    lines.append(f"  System: {platform.system()}")
    lines.append(f"  Release: {platform.release()}")
    lines.append(f"  Version: {platform.version()}")
    lines.append(f"  Machine: {platform.machine()}")
    lines.append(f"  Processor: {platform.processor()}")

    lines.append("\nCPU Information:")
    lines.append(f"  Physical cores: {psutil.cpu_count(logical=False)}")
    lines.append(f"  Total cores: {psutil.cpu_count(logical=True)}")
    cpu_freq = psutil.cpu_freq()

    if cpu_freq:
        lines.append(f"  Max Frequency: {cpu_freq.max:.2f} Mhz")
        lines.append(f"  Current Frequency: {cpu_freq.current:.2f} Mhz")

    lines.append("\nMemory Information:")
    vm = psutil.virtual_memory()
    lines.append(f"  Total: {vm.total / (1024**3):.2f} GB")
    lines.append(f"  Available: {vm.available / (1024**3):.2f} GB")
    lines.append(f"  Used: {vm.used / (1024**3):.2f} GB")

    lines.append("\nDisk Information (>80%):")
    partitions = psutil.disk_partitions()
    for partition in partitions:
        try:
            if not os.path.exists(partition.mountpoint):
                continue
            disk_usage = psutil.disk_usage(partition.mountpoint)
            if disk_usage.used / disk_usage.total > 0.8:
                lines.append(f"  Device: {partition.device}")
                lines.append(f"    Mount point: {partition.mountpoint}")
                lines.append(f"      Total size: {disk_usage.total / (1024**3):.2f} GB")
                lines.append(f"      Used: {disk_usage.used / (1024**3):.2f} GB")
                lines.append(f"      Free: {disk_usage.free / (1024**3):.2f} GB")
        except (PermissionError, FileNotFoundError):
            lines.append(f"      Skipped (Access/File error) -> {partition.mountpoint}")

    lines.append("")

    # NVIDIA GPU
    if which('nvidia-smi'):
        try:
            lines.append("\nNVIDIA GPU Information:")
            nvidia_output = subprocess.check_output([
                'nvidia-smi',
                '--query-gpu=gpu_name,memory.total,memory.used,memory.free,temperature.gpu,utilization.gpu',
                '--format=csv,noheader,nounits'
            ]).decode()
            for gpu_line in nvidia_output.strip().split('\n'):
                name, total, used, free, temp, util = gpu_line.split(', ')
                lines.append(f"  GPU: {name}")
                lines.append(f"    Memory Total: {total} MB")
                lines.append(f"    Memory Used: {used} MB")
                lines.append(f"    Memory Free: {free} MB")
                lines.append(f"    Temperature: {temp}°C")
                lines.append(f"    Utilization: {util}%")
        except (subprocess.CalledProcessError, FileNotFoundError):
            lines.append("  Error getting NVIDIA GPU information")

    # AMD GPU
    if which('rocm-smi'):
        try:
            lines.append("\nAMD GPU Information:")
            rocm_output = subprocess.check_output([
                'rocm-smi', '--showproductname', '--showmeminfo', '--showtemp'
            ]).decode()
            lines.extend(f"  {line}" for line in rocm_output.strip().split('\n'))
        except (subprocess.CalledProcessError, FileNotFoundError):
            lines.append("  Error getting AMD GPU information")

    # Apple Silicon (Mac M1/M2)
    if platform.system() == 'Darwin' and platform.machine() == 'arm64':
        try:
            lines.append("\nApple Silicon Information:")
            cpu_brand = subprocess.check_output([
                'sysctl', '-n', 'machdep.cpu.brand_string'
            ]).decode().strip()
            lines.append(f"  Processor: {cpu_brand}")
            try:
                import torch
                if torch.backends.mps.is_available():
                    lines.append("  MPS: Available")
                    lines.append(f"  MPS Device: {torch.device('mps')}")
                else:
                    lines.append("  MPS: Not available")
            except ImportError:
                lines.append("  PyTorch not installed, cannot check MPS availability")
        except (subprocess.CalledProcessError, FileNotFoundError):
            lines.append("  Error getting Apple Silicon information")

    lines.append("")
    lines.append(DIVIDER)
    return "\n".join(lines)
```
to save press cntl=x y and enter 

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

---

## 🧠 Notes

- To **detach** from the screen: `Ctrl + A` then `D`
- To **re-attach** later: `screen -r gensyn`

---

## 🙌 Credits

- Original repo: [Gensyn AI](https://github.com/gensyn-ai/rl-swarm)
- credits goes to - https://t.me/andhiiTGkamaii
- Special thanks to https://t.me/vikkyvirus https://t.me/DeviL_The_Ghost https://t.me/Zeekeyt
