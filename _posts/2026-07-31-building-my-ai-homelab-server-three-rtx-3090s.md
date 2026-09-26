---
layout: post
title: "Building My AI Homelab Server: Three RTX 3090s and a Bet on Digital Sovereignty"
date: 2026-07-31
categories: [Homelab, AI, Infrastructure]
tags: [AI, LLM, Homelab, RTX3090, Threadripper, Ollama, OpenWebUI, Docker, NVIDIA, Ubuntu, RAG, Qdrant, DigitalSovereignty]
description: "Part 1 of my local AI server build: three RTX 3090s, a Threadripper PRO, 72GB of VRAM, and the full step-by-step for getting Ubuntu, the NVIDIA drivers, Docker, and a complete self-hosted AI stack running."
banner:
  image: https://img.youtube.com/vi/TRpr_qKydSo/maxresdefault.jpg
  opacity: 0.618
---

![](//youtu.be/TRpr_qKydSo)

This is the companion post for the video where I built my first purpose-built local AI server. If you came here looking for the exact commands and the full Docker Compose file to follow along, they're all below. If you want the "why," keep reading from the top.

Quick heads up before we get into it: this post covers Part 1 of a two-part series. Part 1 is the hardware build, the OS install, and getting a bare AI stack deployed on default settings. Part 2 (coming soon) is where I actually tune this thing: the RAG pipeline, the reranker, model selection, and benchmarking. If you're here for "how do I make this fast," that's Part 2. This post is "how do I make this exist."

---

## Why I'm Building a Local AI Server

Hey there homelabbers, self-hosters, IT-pros, and engineers. Rich here!

I'll admit it up front: I have three AI subscriptions. ChatGPT, Claude, and Google Gemini. That's the setup I'm trying to partially get out from under, and the reason comes down to one phrase I don't throw around lightly: **digital sovereignty**.

Every one of those subscriptions is a rented brain. I don't own the model, I don't own the weights, and I don't own the infrastructure it runs on. I own a login and a promise that the terms won't change tomorrow. Except terms do change. Pricing changes, rate limits change, and models get "improved" in ways that quietly make them worse at the exact thing you were using them for. I've watched this happen more than once: a company ships a new, more expensive model, and suddenly the old one you liked feels dumber.

Here's the part that actually worries me. I don't think today's pricing is the real price. Frontier AI is being sold to us right now at a loss, subsidized by venture capital. That subsidy doesn't last forever. When it ends, either prices go up, free tiers disappear, or access gets rationed. I call this the token apocalypse, and when it hits, nobody's going to keep running a business at a loss just because we got used to cheap tokens.

There's a second angle too, and it's less about business and more about policy. These are US companies, subject to US regulation and US export control decisions. This isn't a political statement, it's a resilience argument. If I depend entirely on infrastructure I don't control, for tools I now use daily for work and personal projects, I've got a single point of failure I didn't choose and can't negotiate with. We already saw a version of this play out: the US government forced Anthropic to suspend access to Fable 5 and Mythos 5 over export control concerns. That took access away from a lot of companies and people overnight, including plenty in countries friendly to the US. It got restored a few weeks later, but the point stands: that decision wasn't in anyone's hands but the vendor's and the government's.

And the timing has never been better for this bet to pay off. A year or two ago, local models were noticeably behind the frontier. That gap has closed fast. On a lot of coding, reasoning, and knowledge benchmarks, the best open-weight models today are within single digits of the closed frontier models, and on some tasks they're basically tied. Models like Qwen, DeepSeek, GLM, and Gemma are shipping frontier-adjacent capability under licenses I can actually download and run on my own hardware. That's why I think hitting 60-80% of what a frontier model can do is realistic, and honestly, that's a win. I don't need my local box to beat GPT or Claude. I need it to be good enough that I stop reaching for the cloud model out of habit.

So here's the short version: I don't want my daily tools to be something that can be taken away, throttled, or repriced overnight. Owning the hardware means I own the floor. It might be a lower floor than the frontier labs, but it's mine, and nobody can move it out from under me.

---

## The Hardware

"I want digital sovereignty" doesn't mean much if the box can't actually run these models at a usable speed. Here's what went into this build, and why.

### At a Glance

| Component | Part | Notes |
|---|---|---|
| GPU (x3) | EVGA RTX 3090 FTW3 Ultra | 24GB VRAM each, 72GB combined |
| CPU | AMD Threadripper PRO 3945WX | 12C/24T, 128 PCIe 4.0 lanes, 280W TDP |
| Cooling | Arctic Liquid Freezer WS360-SP6 | AIO, chosen for case clearance and 280W headroom |
| Motherboard | Gigabyte MC62-G40 (WRX80) | 7 PCIe 4.0 slots, 8-channel ECC memory |
| RAM | 8x 32GB DDR4 ECC | 256GB total, all 8 channels populated |
| Storage | 2x Samsung 980 500GB NVMe | One for OS/apps, one for model storage |
| PSU | be quiet! Dark Power Pro 12, 1500W | 80 Plus Titanium, 10x 8-pin PCIe |
| Case | Rosewill RSV-AI01, 4U | 8 hot-swap SAS bays, 11 expansion slots |

### The GPUs: Three RTX 3090 FTW3 Ultra

The stars of the show are three EVGA RTX 3090 FTW3 Ultra cards. There are newer GPUs on the market, but for local LLM inference there's really one number that matters: 24GB of VRAM per card, 72GB combined across the three. VRAM capacity is the whole ballgame for this use case. It determines how large a model you can load and how much context you can give it before things fall over. A newer card with less VRAM is often the worse choice here, no matter how much faster its compute is on paper.

I already owned one of these cards, which is why I picked up two more used, at roughly $1,200 each. This card launched back in 2020 at an MSRP north of $1,500. A five-year-old GPU holding that kind of value on the used market is not normal. Used tech is supposed to depreciate. This one hasn't. If anything, prices have firmed back up. Thanks, AI.

### CPU and Motherboard: The Part That Actually Matters

GPUs are the flashy part, but the CPU and motherboard are arguably more important to a multi-GPU build. A normal desktop CPU gives you roughly 20 to 24 PCIe lanes total. That's barely enough for one GPU at full speed, let alone three.

That's why I went with an **AMD Threadripper PRO 3945WX**: a 4.0GHz CPU that boosts to 4.3GHz, 280W TDP, 12 cores and 24 threads. Twelve cores is modest for a Threadripper, but core count isn't why I bought it. I needed the 128 PCIe 4.0 lanes this chip provides, which is what lets all three GPUs run at full x16 bandwidth. Picked this up off eBay for $129 with free shipping.

Pairing it with a **Gigabyte MC62-G40** workstation board on the WRX80 chipset, also off eBay, for $657. This board has six full x16 PCIe 4.0 slots plus one x8, for seven total. Every GPU in this build runs at full x16 simultaneously. It also supports up to 8 channels of ECC or non-ECC memory, has 2 M.2 NVMe Gen4 x4 slots, dual 10GbE via an Intel X550-AT2 controller, a dedicated IPMI management interface, an M.2 slot for Wi-Fi, 3 SlimSAS 4i interfaces, 4 SATA ports, and onboard audio. I picked this board, with a lot of input from the 2GT community, specifically because stuffing three full-size EVGA 3090s into a case requires a board with the physical layout to support it.

### Cooling

The 280W TDP on the Threadripper needed real cooling, so I went with an **Arctic Liquid Freezer WS360-SP6** AIO. Two reasons: first, it solves a case-clearance problem, since the PSU shelf on the Rosewill case sits partially above the CPU socket, and an air cooler just doesn't have the headroom there. Second, its cooling performance handles 280W without breaking a sweat. Arctic makes genuinely good coolers and fans, this one runs quiet and cool, and it carries a 6-year warranty.

### RAM and Storage

For RAM, I'm running 8x 32GB DDR4 ECC modules, maxing out all 8 channels on the board for 256GB total. That's more than I should reasonably need day to day, but if a model ever overflows the GPU VRAM, having that system memory as a fallback means I can hold the overflow there, at the cost of speed rather than a hard failure.

Storage is two used 500GB Samsung 980 NVMe drives I had on hand: one for boot, OS, and app storage, and the second dedicated to model storage.

### Power Supply

Rounding out the build is a **be quiet! Dark Power Pro 12**, 1500 watts, 80 Plus Titanium efficiency. This thing is a chonker. It supports up to 10 total 8-pin PCIe connectors and has enough headroom to safely run this system and absorb power spikes under load. Largest and heaviest ATX PSU I've ever installed.

### The Case: Rosewill RSV-AI01

Finding a case that could actually fit three full-size 3090s, without going full purpose-built server chassis, was the hardest part of sourcing this build. I landed on the **Rosewill RSV-AI01**, a 4U chassis.

Before this build, I honestly thought Rosewill made mediocre gear. I was wrong. This case is well built throughout: 8 hot-swappable SAS bays up front, 11 total expansion slots, support up to extended ATX/E-ATX motherboards, 3 hot-swappable 120mm fans in the middle of the chassis, 2 rear 80mm exhaust fans to help pull hot air off the rear GPUs, front USB-A and USB-C, and rack rails included. The only real downside is cost: $500 shipped.

### The Budget

Total spend on this build, not counting the parts I already had on hand (the first 3090, the RAM, and the NVMe drives), came to roughly **$3,635.53**. Known individual line items:

- 2x additional RTX 3090 FTW3 Ultra (used): ~$2,400
- Threadripper PRO 3945WX (used): $129
- Gigabyte MC62-G40 (used): $657
- Rosewill RSV-AI01 case: $500

The rest of that total covers the AIO cooler, the PSU, thermal paste, and cabling, which weren't broken out individually in the video. If I'd been buying every single part fresh, including the GPU I already owned, the RAM, and the NVMe drives, I estimate the real total lands closer to **$6,000**.

To be clear about what this build is and isn't: if your goal is the cheapest possible way to run a 7B parameter model, this is the wrong build to copy. This is "run genuinely large models at a size that can meaningfully replace frontier subscriptions," and that costs real money, even buying used like I did.

---

## The Build

The physical build itself went smoothly. Order of operations:

1. Prep the motherboard outside the case: install the Threadripper CPU, seat the 256GB of RAM, and install the two NVMe drives.
2. Mount the board into the case.
3. Mount the AIO radiator. Only one orientation actually made sense in this chassis: tubing routed along the far right side of the case.
4. Apply thermal paste (Arctic MX-6) and attach the waterblock to the CPU.
5. Wire up the front panel connectors.
6. Install the three 3090s. The first two went in without issue. The third was a fight, thanks to the AIO tubing running along that same side of the case. Got there with an extra set of hands and some patience.
7. Cable management and power last. Total cabling for this build: 9x 8-pin PCIe for the GPUs, 2x 8-pin CPU aux, the 24-pin ATX main connector, plus power run to the front hot-swap disk array for future-proofing.

One thing worth calling out if you're planning a similar build: Gigabyte put all of the case fan headers on the right side of this board. That's fine on its own, but with fans connected to those headers, the final 3090 couldn't fully seat into its slot. Niche problem, but worth knowing about before you're mid-build.

Looking at the finished result, there was no version of this build that worked with an air cooler under that PSU shelf. Not enough clearance, and with the sheer amount of cabling packed into this case, the AIO was the right call.

---

## Installing the OS

With the hardware assembled, it's time for software. First step: get **Ubuntu Server 26.04** installed using the AMI MegaRAC management interface built into the board.

Boot off a USB stick with the ISO written to it (I used Rufus). Nothing unusual about the install itself: stick with the defaults, select your language, let it test the mirrors, set up the first user, format the OS disk, and kick off the install. Start to finish, this took about 7 to 10 minutes.

From here on, everything happens over SSH. It's dramatically easier to work with the console when you can copy and paste, instead of fighting the web KVM.

---

## Step-by-Step: Getting the AI Stack Running

Everything below is copy-paste ready. This is the exact sequence from the video, run in order.

### 1. Update and Upgrade the Host

First thing after any fresh install: fully patch the box before doing anything else.

```bash
apt update; apt dist-upgrade -y; apt autoremove -y; apt autoclean -y; reboot
```

This single line updates the package lists, upgrades everything, removes packages that are no longer needed, clears the local package cache, and reboots. One command, no babysitting.

### 2. Install the NVIDIA Drivers

This is arguably the most important step in the whole setup. It used to be a huge pain. It isn't anymore.

```bash
ubuntu-drivers devices
```

This lists every available driver for the NVIDIA hardware detected in your system. You'll get a menu of options. Because this is a headless server with no GUI, you want the **`-server`** variant of the driver, not the desktop one. I went with a middle-of-the-road version, not bleeding edge and not near end-of-life:

```bash
apt install nvidia-driver-595-server
```

Pick whichever server driver version makes sense for your hardware and needs, this is just what I landed on. This installs a fair number of packages, including a kernel recompile to support the new driver. Confirm the prompt, let it finish, then reboot:

```bash
reboot
```

### 3. Validate the Drivers

After the box comes back up, confirm the GPUs are visible and the driver installed correctly:

```bash
nvidia-smi
```

This shows every NVIDIA device in the system, along with utilization, thermals, memory consumption, and any processes currently using them. You should see all three GPUs listed and idle.

### 4. Install Docker CE

Two components are left: Docker CE to run the containers, and the NVIDIA Container Toolkit to let those containers access the GPUs directly.

```bash
# Remove potentially conflicting packages
sudo apt remove -y docker.io docker-compose docker-compose-v2 \
  docker-doc podman-docker containerd runc

# Add Docker's repository and signing key
sudo apt update
sudo apt install -y ca-certificates curl

sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg \
  -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

sudo tee /etc/apt/sources.list.d/docker.sources >/dev/null <<EOF
Types: deb
URIs: https://download.docker.com/linux/ubuntu
Suites: $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")
Components: stable
Architectures: $(dpkg --print-architecture)
Signed-By: /etc/apt/keyrings/docker.asc
EOF

# Install Docker CE, Buildx, and Compose
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io \
  docker-buildx-plugin docker-compose-plugin
```

This removes any conflicting prior Docker installs, adds Docker's official repo and signing key, and installs Docker CE, the CLI, containerd, Buildx, and Compose. Verify it's running:

```bash
docker ps
```

If you get a response back at all, even an empty table, Docker is working.

### 5. Install the NVIDIA Container Toolkit

This is the piece that lets Docker containers actually reach the GPUs.

```bash
curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey \
  | sudo gpg --dearmor --yes -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg

curl -sL https://nvidia.github.io/libnvidia-container/stable/deb/nvidia-container-toolkit.list \
  | sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' \
  | sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list >/dev/null

sudo apt update
sudo apt install -y nvidia-container-toolkit

sudo nvidia-ctk runtime configure --runtime=docker
sudo systemctl restart docker
```

This adds NVIDIA's signing key, adds their repo, installs the container toolkit, configures Docker to use the NVIDIA runtime, and restarts the Docker daemon. Once this finishes, the foundation is done. Everything from here is deploying the actual AI stack.

### Why Docker for This Stack

A quick word on why every service below runs in a container instead of installed directly on the host. Three reasons:

1. **Updates are trivial and low-risk.** Pull a new image and recreate the container. If it breaks, roll back. The host OS is never at risk.
2. **Portability.** Most of the configuration for these services lives in the Compose file itself, which means this entire setup can be redeployed on different hardware without starting from scratch.
3. **It's how the industry actually runs this stuff.** Bigger installations run Kubernetes over Docker directly, but for a single-host setup like this, Docker is the right level of complexity.

### 6. Deploy the Stack

This Compose file builds out Open WebUI, Ollama, Qdrant, Tika, SearXNG, an Infinity reranker, Open Terminal, Caddy, and Dozzle, and wires them all together.

**One more prerequisite before you run this:** several of these services use bind mounts, not Docker named volumes, for the state that actually matters. That means the host paths referenced in the file need to already exist before you bring the stack up. Docker won't error out if they're missing, it'll just silently create empty directories in their place, and your data won't land where you expect. Create these on the host first:

- `/ai-box/openwebui/core` — Open WebUI's core state: the webui.db database, user accounts, and configuration.
- `/ai-box/openwebui/rag/docs` and `/ai-box/openwebui/rag/uploads` — documents and files fed into the RAG pipeline.
- `/ai_storage/qdrant/storage` — Qdrant's vector database storage. This one lives on a separate path (`/ai_storage` instead of `/ai-box`) because it's on its own dedicated storage rather than the same disk as everything else. Adjust it to wherever you're keeping bulk storage on your box.
- `/ai-box/ollama` — downloaded model weights. This directory gets big fast, budget your disk accordingly.
- `/ai-box/caddy/certs` — TLS certificates for Caddy.
- `/ai-box/caddy/Caddyfile` — this one's a file, not a directory, and it needs to exist with actual Caddy configuration in it before you run `docker compose up`. If it's missing, Docker will create an empty directory in its place instead of mounting a file, and Caddy will fail to start.

A one-liner to stub out the directories:

```bash
sudo mkdir -p /ai-box/openwebui/core /ai-box/openwebui/rag/docs /ai-box/openwebui/rag/uploads \
  /ai_storage/qdrant/storage /ai-box/ollama /ai-box/caddy/certs
```

You'll still need to write an actual Caddyfile by hand, that part's outside the scope of this post.

> **Before you copy this:** `OPEN_TERMINAL_API_KEY` under the `open-terminal` service is a placeholder on purpose, swap it for a password you generate yourself. Never ship a compose file with real secrets sitting in plaintext environment variables, especially not one you're going to post publicly.

```yaml
name: ai-box-stack
services:
  open-webui:
    image: ghcr.io/open-webui/open-webui:cuda
    deploy:
      resources:
        reservations:
          devices:
            - driver: nvidia
              count: all
              capabilities: [gpu]
    container_name: open-webui
    ulimits:
      nofile:
        soft: 65536
        hard: 65536
    ports:
      - "3000:8080"
    environment:
      - WEBUI_NAME=2GT AI
      - WHISPER_MODEL=large-v3
      - WHISPER_MODEL_DIR=/app/backend/data/cache/whisper/models
      - WHISPER_COMPUTE_TYPE=float16
      - WHISPER_LANGUAGE=en
      # --- Core ---
      - WEBUI_URL=<your host's URL>
      # Open WebUI stores webui.db + uploads under DATA_DIR
      - DATA_DIR=/app/backend/data
      # If you run Ollama as a service in this compose:
      - OLLAMA_BASE_URL=http://ollama:11434
      # Vector DB backend, Qdrant instead of the default embedded Chroma
      - VECTOR_DB=qdrant
      - QDRANT_URI=http://qdrant:6333
      - ENABLE_QDRANT_MULTITENANCY_MODE=true
      # Reranking + hybrid search
      - ENABLE_RAG_HYBRID_SEARCH=true
      - RAG_RERANKING_ENGINE=external
      - RAG_EXTERNAL_RERANKER_URL=http://infinity:7997/rerank
      - RAG_RERANKING_MODEL=BAAI/bge-reranker-v2-m3
      - RAG_TOP_K=20
      - RAG_TOP_K_RERANKER=5
      - RAG_SYSTEM_CONTEXT=true
      - ENABLE_API_KEYS=true
    volumes:
      # Core state (main DB webui.db, configs, etc.)
      - /ai-box/openwebui/core:/app/backend/data
      # Dedicated mounts for RAG-ish data:
      - /ai-box/openwebui/rag/docs:/app/backend/data/docs
      - /ai-box/openwebui/rag/uploads:/app/backend/data/uploads
    restart: unless-stopped
    depends_on:
      - ollama
      - qdrant

  qdrant:
    image: qdrant/qdrant:latest
    container_name: qdrant
    restart: unless-stopped
    volumes:
      - /ai-box/qdrant/storage:/qdrant/storage
    ports:
      - "6333:6333"
      - "6334:6334"

  tika:
    image: apache/tika:latest-full
    container_name: tika
    ports:
      - "9998:9998"
    restart: unless-stopped

  redis:
    image: redis:alpine
    container_name: searxng_redis
    restart: unless-stopped
    volumes:
      - redis_data:/data

  searxng:
    image: searxng/searxng:latest
    container_name: searxng
    restart: unless-stopped
    ports:
      - "8080:8080"
    volumes:
      - searxng_config:/etc/searxng
      - searxng_data:/var/cache/searxng
    environment:
      - SEARXNG_BASE_URL=<your URL to your server>
      - SEARXNG_REDIS_HOST=redis
      - SEARXNG_REDIS_PORT=6379
      - UWSGI_WORKERS=4
      - UWSGI_THREADS=4
    depends_on:
      - redis
    logging:
      driver: "json-file"
      options:
        max-size: "1m"
        max-file: "1"

  ollama:
    image: ollama/ollama:latest
    container_name: ollama
    ports:
      - "11434:11434"
    volumes:
      - /ai-box/ollama:/root/.ollama
    restart: unless-stopped
    environment:
      - OLLAMA_CONTEXT_LENGTH=131071
    deploy:
      resources:
        reservations:
          devices:
            - driver: nvidia
              count: all
              capabilities: [gpu]

  open-terminal:
    image: ghcr.io/open-webui/open-terminal:latest
    container_name: open-terminal
    restart: unless-stopped
    environment:
      OPEN_TERMINAL_API_KEY: "<choose-your-own-api-key>"
      OPEN_TERMINAL_PACKAGES: "ripgrep tree curl"
      OPEN_TERMINAL_PIP_PACKAGES: "httpx polars"
      OPEN_TERMINAL_MULTI_USER: "true"
    volumes:
      - open_terminal_home:/home/user
      # - /var/run/docker.sock:/var/run/docker.sock  # uncomment for Docker CLI access
    ports:
      - "8020:8000"
    security_opt:
      - no-new-privileges:false
    cap_drop:
      - NET_ADMIN
      - SYS_ADMIN

  infinity:
    image: michaelf34/infinity:latest
    container_name: infinity
    restart: unless-stopped
    ports:
      - "7997:7997"
    volumes:
      - infinity-cache:/app/.cache
    deploy:
      resources:
        reservations:
          devices:
            - driver: nvidia
              count: 1
              capabilities: [gpu]
    command: >
      v2
      --model-id BAAI/bge-reranker-v2-m3
      --engine torch
      --device cuda
      --batch-size 32
      --port 7997
      --no-bettertransformer
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:7997/health"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 120s

  dozzle:
    container_name: dozzle
    image: amir20/dozzle:latest
    restart: unless-stopped
    ports:
      - "8888:8080"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock:ro
      - dozzle_data:/data

volumes:
  ollama:
  redis_data:
  searxng_config:
  searxng_data:
  open_terminal_home:
  infinity-cache:
  caddy_data:
  caddy_config:
  dozzle_data:
```

Save that as `docker-compose.yml`, drop it in a working directory on the host, and bring the whole stack up with one command:

```bash
docker compose up -d --build
```

That's it. Every service downloads, builds, and starts, all connected on the same Docker network.

### What Each Service Does

A quick reference for what you just deployed, since the compose file alone doesn't explain itself:

| Service | Role |
|---|---|
| **open-webui** | The chat interface and RAG front end. This is what you actually interact with. |
| **ollama** | Runs the local LLMs and serves inference to Open WebUI. |
| **qdrant** | Vector database backing the RAG pipeline, replacing Open WebUI's default embedded Chroma. |
| **tika** | Document parsing and text extraction for anything you upload into RAG. |
| **redis** | Backing cache for SearXNG. |
| **searxng** | Self-hosted meta search engine, used to give the AI live web search capability. |
| **infinity** | Runs the reranking model (BAAI/bge-reranker-v2-m3) used to improve RAG result quality. |
| **open-terminal** | Sandboxed terminal/code execution environment accessible from Open WebUI. |
| **dozzle** | Real-time log viewer for all the running containers. |

Every one of these is running on default settings in this video. Tuning the RAG pipeline, dialing in the reranker, picking the right models for a 72GB VRAM budget, and benchmarking real performance is the entire subject of Part 2.

---

## Final Thoughts

I'm genuinely impressed with the Rosewill case. Whatever gave Rosewill a bad reputation in my head clearly wasn't earned, at least not by this chassis. Eight hot-swap SAS bays, four internal 2.5" mounts, hot-swap fans, room for a full radiator, solid build quality. It'd make a great server case even outside of a GPU-heavy build like this. The one real caveat: if you go with this case, budget for an AIO. The PSU shelf location makes an air cooler a non-starter.

The build itself went smoothly, with one gotcha worth remembering: Gigabyte's fan header placement on the right side of the MC62-G40 can interfere with seating a GPU in the final slot if you've got fans plugged in there. Worth checking before you're three GPUs deep into an install.

I intentionally didn't go deep on the AI stack configuration in the video, and I'm not going deep on it here either. There's a lot happening in that Compose file, a lot of reasoning behind each container's configuration, and it deserves its own dedicated video and post rather than getting rushed into the last few minutes of this one. That's coming soon.

In the meantime, everything above, the commands, the full compose file, is yours to copy and run. If you build something off this, or run into the fan header issue, or have questions about any piece of the stack, drop it in the comments or come find us in the Discord.

---

Thanks for reading, and thanks to everyone who supports the channel through Patreon and YouTube memberships. If you want to support what we do here, those links are on the site. Join the community Discord to talk homelab, self-hosting, and AI infrastructure with a lot of like-minded nerds. See you in Part 2.
