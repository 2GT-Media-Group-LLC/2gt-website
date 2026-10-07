---
layout: post
title: "My Local AI Software Stack, Explained: Ollama, Open WebUI, Qdrant, SearXNG, and More"
date: 2026-09-29
categories: [Homelab, AI, Infrastructure]
tags: [AI, LLM, Homelab, Ollama, OpenWebUI, Qdrant, Tika, SearXNG, Infinity, OpenTerminal, RAG, Reranking, Docker, DockerCompose, NVIDIA, SelfHosted, DigitalSovereignty]
description: "Part 2 of my local AI server series: every piece of the software stack running on my triple RTX 3090 box, why I chose it, how a prompt actually flows through it, and a block-by-block walkthrough of the Docker Compose file that builds it all."
banner:
  image: https://img.youtube.com/vi/Wf2Kyc8mi4c/maxresdefault.jpg
  opacity: 0.618
---

![](//youtu.be/Wf2Kyc8mi4c)

This is the companion post for Part 2 of my local AI server series. In [Part 1]({% post_url 2026-07-31-building-my-ai-homelab-server-three-rtx-3090s %}), I built the hardware: three RTX 3090s, a Threadripper PRO, Ubuntu, the NVIDIA drivers, and Docker. At the end of that one, I promised you a full start-to-finish walkthrough of the software stack that actually runs everything. This is that walkthrough.

If you're just here for the Docker Compose file, [jump straight to it](#the-docker-compose-file-block-by-block). If you want to know what each piece does, why I picked it, and how they all talk to each other, keep reading from the top.

---

## Introduction

Hey there homelabbers, self-hosters, IT-pros, and engineers. Rich here!

Fair warning: this one's long, and it's technical. If that's your jam, stick around.

Also, like everything in technology, there are **a ton** of other ways to run local AI, and each one has its strengths and weaknesses. So please don't blow up the comment section telling me I'm an idiot for not using your favorite model runner, reranker, or embedder. I'd love for this to be a collaborative conversation. Get in the comments and tell me what you're running, why you're running it, and the cool stuff you've been able to do with it. Cool?

Here's what I'm covering:

- **The tools and software** I'm using, why I'm using them, what they're for, and how they connect.
- **How it all works together**, with three real scenarios that trace a prompt through the stack.
- **The Docker Compose file**, block by block, so you know what's happening in there and how to run it yourself.

Along the way I'll share the gotchas, oddities, and lessons I picked up putting this together.

What I'm *not* covering is how AI models, training, inference, and neural networks work under the hood. I'm not a mathematician, I wouldn't do it justice, and there are already plenty of great videos on that. This is about the practical side: giving you a robust, flexible local AI to run in your homelab.

---

## AI Isn't Magic. It's Just Software.

Before we get into the stack, I want to start with the bottom-line lesson that opened my eyes when I first started building these systems. It's the most foundational thing I've learned, and it's also helped me a lot when I talk about AI with non-technical people.

**AI isn't magic. It's just software.**

When you use ChatGPT or Claude, you get the "magical prompt" experience. You type a question or give it a task, and it somehow knows the answer and hands it back to you. What's actually happening is that **different tools behind the scenes do the work**, hand their results to the model, and the model uses them to generate your response.

A working AI isn't one all-knowing, all-doing thing. It's **a model plus a collection of tools the model calls to do the actual work**. Once you understand that, AI gets a lot less mysterious and a lot less "alive." Trust me, that's a good thing.

A "tool" can be almost anything:

- A dedicated application that performs an action or executes a task
- A script that does something specific
- An API call that queries a third-party service, database, or SaaS product and returns the results to the model

I've built tools that do everything from generating branded PowerPoint presentations to pulling IPMI data from servers to report back temperatures, and plenty in between. **Tools are the most useful way to extend what your local AI can do.** Without them, your local model can only answer from what it learned during training.

One more thing worth saying: the world is changing incredibly fast. A lot of the tools, configurations, and systems in this post will probably be outdated, or even wrong, in a year or two. As models get bigger and smarter, they'll do more of what tools do today on their own. I think we'll eventually need fewer tools, but the *concept* of tools isn't going away.

---

## The AI Software Stack

My stack is built from several components, each with its own job. Again, there are plenty of other applications that can get you to the same place. These are the ones I use.

### The Foundation: Ubuntu Server

Linux runs the world, and honestly, Linux runs the world's AI too. I use **Ubuntu Server** because I prefer Ubuntu's approach to Debian. You can run local AI on Linux, macOS, or even Windows, but in my opinion you'll have the best luck with Linux, followed by macOS.

### Containerization: Docker

Everything in this post runs as a container on the physical host. I prefer **Docker**, but Podman or anything else you like will work. The big SaaS AI providers generally run on Kubernetes, and you could too, but that's a lot to bite off for a single box.

You *could* install every one of these tools directly on the OS. I'm telling you right now, that's a bad idea. Running them as containers means:

- **They're portable.** Your compose file *is* your environment.
- **Updates and upgrades are low-risk.** You never put the stability of the host OS on the line.
- **Recovery is easy.** If everything goes to hell, you nuke and pave, and your compose file rebuilds the whole environment.
- **Experimenting is safe.** You can try new things later without risking the rest of the system.

With the base layers covered, let's get into the software that makes up the AI stack itself.

### The Model Runner: Ollama

This is the one that's definitely going to fill up the comments. For my stack, I use **[Ollama](https://ollama.com)**.

Ollama is an open-source program that runs large language models locally on your own hardware. It gives you:

- **Simple, flexible model management**: pull, update, and remove models with one command
- **An OpenAI-compatible API**
- **Broad compatibility** with a huge range of software and platforms

There are plenty of other model runners: vLLM, llama.cpp, LM Studio, and more. **Ollama isn't the fastest.** vLLM is generally faster, especially on NVIDIA GPUs. What Ollama gives me is flexibility. For me, the number one value of this platform is trying out new local models as soon as they drop. Ollama loads a new model on demand when I ask for it, without me tearing anything down or reconfiguring anything.

**My take:** if you're only planning to run one model, use vLLM. If you want flexibility, Ollama is the best choice.

### The User Interface: Open WebUI

This is arguably the most important user-facing part of any local AI deployment. There are lots of options for an interactive UI, but for my money, nothing beats **[Open WebUI](https://openwebui.com)**.

Open WebUI is an open-source, self-hosted web interface that gives you a proper ChatGPT-style chat experience on top of your own models. Point it at Ollama, or basically anything OpenAI-compatible, and you get:

- **Real model management** and a solid chat interface
- **Built-in RAG**, so you can chat with your own documents
- **Multi-user support** with admin controls
- **Plugins and tools**
- **Voice interaction**, if you wire up speech-to-text and text-to-speech

There are alternatives here too; LibreChat is one. But in my opinion, Open WebUI is the most complete chat interface with the most features. A big reason I lead with it is how aggressive the team behind it is about shipping. Every update blows me away with its list of new features, and they're keeping pace with a lot of what we see in frontier model chat interfaces.

Here's what really sells me on it, though. Every time I deploy Open WebUI and hand it to users, **they're instantly comfortable with it**. I think that says a lot.

### The Memory: Qdrant

Ollama and Open WebUI are the biggest and most visible parts of the stack. But as I said earlier, **tools are what make AI actually useful**, so let's get into them. First up is **[Qdrant](https://qdrant.tech)**.

Qdrant is an open-source **vector database**. It stores embeddings (vectors) and lets you run fast similarity searches over them. In an LLM/RAG stack, it's the "memory" piece. Uploaded documents get broken into **chunks**, the chunks get embedded, and the vectors get stored in Qdrant. When you ask a question about those documents, Qdrant serves up the most relevant pieces to the model, which uses them to generate your answer.

Think of Qdrant as **a librarian who never actually reads the books you give them**. You hand it a pile of documents, and it gives each section a fingerprint and files those fingerprints away. When you ask a question, it fingerprints your question too, finds the closest matching fingerprints, and hands the model those chunks of text. The model never has to memorize your documents. It gets handed the one or two pages it needs, reads them, and answers from them.

There are solid alternatives: pgvector, Milvus, Chroma, and others. I picked Qdrant over pgvector because it tends to give **better vector search quality** and **performs better at scale with multiple concurrent users**.

### The Document Reader: Apache Tika

**[Apache Tika](https://tika.apache.org)** is a toolkit that does one simple, important job: **it reads documents**. Not just one type, either. It handles over a thousand file types: Word docs, PowerPoint decks, Excel sheets, PDFs, emails, even images with text in them. Tika takes whatever you upload and breaks it down into information your local model can understand.

Why does that matter? **AI models can't read most of the file formats you'd want to give them.** Plain text and Markdown are fine, but the moment you feed your local model something like a PDF, it's going to throw an error. When you upload a file to Open WebUI, it sends the file to Tika first. Tika extracts the content from whatever format it's in, and that clean text is what the model ends up working with.

### Web Search: SearXNG

This next one's a big one: **[SearXNG](https://docs.searxng.org)**.

SearXNG is a **self-hosted metasearch engine**. It searches the internet on your behalf by sending your query to dozens of real search engines, aggregating the results, and handing them back to the model as JSON. It can also do a lot more than I'm using it for here. On its own, it's a personal, anonymous, self-hosted search engine with a nice web interface.

Here's why it matters. **A local model has no real-time information.** Its understanding of the world stops at the last date in its training data. The internet has current information, but your model can't reach it on its own. When you ask for the latest news, or ask it to search for 2GuysTek and write a report, the model uses SearXNG to run the search, gets the results back, and then writes its response.

**Without a tool like SearXNG, your local AI has no internet access.**

### Reranking: Infinity

Now let's talk about reranking, and the tool I use for it: **[Infinity](https://github.com/michaelfeil/infinity)**.

Infinity is an embedding and reranking server that supports NVIDIA, AMD, Apple Silicon, and even CPUs. I'm using it specifically for **reranking**. Reranking is a secret superpower that makes your local AI much better at picking the *right* data when it answers questions about the information you've given it.

When an AI searches documents, it uses embeddings to find the nearest matches. It grabs the 20 to 50 closest chunks of text and moves on. A fair number of those are just text that *sounds* similar without actually being relevant.

**A reranker is a second pass.** It takes your question and that whole list, reads each chunk side by side against the question, and re-sorts them by actual relevance.

AI is only as good as the documents it gets fed. **Bad docs in means a confident wrong answer out.** A reranker quietly fixes the input, so you get noticeably better answers from the same model and the same data.

### Sandboxed Execution: Open Terminal

The final core component is Open WebUI's newest tool, **[Open Terminal](https://github.com/open-webui/open-terminal)**.

Open Terminal is a **dedicated, sandboxed Linux command-line environment for your local AI**. In plain terms, it's a place where your AI can run Linux commands and scripts safely, without being able to hurt your physical host or its data.

Next to web search, **this is the second most powerful tool you can give your AI.** A couple of examples:

- Ask your AI to **ping google.com**. Without a terminal to run that command, it effectively can't.
- Ask it to **write a script that polls IPMI data from a server**. It can write that script all day long, but if you want it to actually *run* the script and bring back data, it needs a safe place to do that.

We've all heard the horror stories about OpenClaw deleting people's personal data because it just decided to. Open Terminal is the safe middle ground. Your model gets a place to run scripts and commands, and it can't go rogue and wipe your home directory by mistake.

### The Stack at a Glance

| Component | Role | Popular Alternatives |
|---|---|---|
| **Ubuntu Server** | Host operating system | Debian, other Linux distros, macOS |
| **Docker** | Container runtime for every service | Podman, Kubernetes |
| **Ollama** | Model runner / inference | vLLM, llama.cpp, LM Studio |
| **Open WebUI** | Chat UI, RAG orchestration, tool calling | LibreChat |
| **Qdrant** | Vector database (RAG "memory") | pgvector, Milvus, Chroma |
| **Apache Tika** | Document parsing and text extraction | |
| **SearXNG** | Self-hosted web search for the model | |
| **Infinity** | Reranking model server | |
| **Open Terminal** | Sandboxed command and script execution | |

---

## How It All Works Together

When I was writing up these components, I realized that all of this feels pretty abstract until you see how data actually flows between them. So here are three scenarios that show, at a high level, how these tools work together to produce those "magical" AI answers.

### Scenario 1: Asking About Something the Model Doesn't Know

**The prompt:** *"What's the newest Proxmox release, and what's changed?"*

1. **Open WebUI → Ollama.** Open WebUI forwards your prompt to Ollama. It doesn't just send the question. It also sends **a list of the tools the model can use**, and one of them is web search, which is SearXNG.
2. **The model realizes it doesn't know.** Its training data stops at a certain date, so it has no clue what shipped for PVE last month. Instead of guessing, it **responds with a tool call**: *"Run a web search for this query and give me what comes back."*
3. **Open WebUI → SearXNG.** Open WebUI runs the tool call for the model and sends the request to SearXNG.
4. **SearXNG searches the internet.** It sends the query to a bunch of different search engines and collects the results.
5. **Results come back.** The results are merged, deduplicated, and returned to Open WebUI.
6. **Open WebUI → Ollama.** The fresh, current information goes back to Ollama, the GPUs go *burrrr*, and the model writes its answer from up-to-date information.
7. **Ollama → Open WebUI → you.** The response comes back to Open WebUI, which shows it to you.

All of this happens incredibly fast, entirely behind the scenes. From your seat, it looks like the model just *knows* everything. It doesn't. It used a tool to get the information. **This is what I mean when I say AI isn't magic. It's software using a bunch of tools.**

### Scenario 2: Asking Questions About Your Own Documents

**The setup:** you attach a PDF to a chat and ask questions about it.

1. **Open WebUI → Tika.** The model can't do anything useful with a PDF (or most file formats), so Open WebUI sends the file to Apache Tika for content extraction. Tika pulls out clean text, runs OCR on any images, and sends that text back.
2. **Chunking and embedding.** Open WebUI splits the text into **chunks** and runs each one through an **embedding model** on the GPUs. An embedding is just a long list of numbers that represents what a chunk of text *means*. Those outputs are the **vectors**.
3. **Open WebUI → Qdrant.** The vectors are stored in Qdrant for later.
4. **You ask a question.** Open WebUI embeds your question the same way and runs a **hybrid search** against Qdrant. A hybrid search looks two ways at once:
   - a **semantic search** for passages with *similar meaning*, and
   - a **keyword search** for the *exact terms* in your question.
5. **Qdrant returns the top 20 candidates.** Plenty of those will be wrong, irrelevant, or outright garbage. If we handed all 20 to the model, we'd get a terrible answer. This is where the reranker earns its keep.
6. **Open WebUI → Infinity.** All 20 results go to Infinity, which runs **its own model on the same GPUs**. A reranking model is a very different kind of model. It doesn't answer questions. It reads your question and each chunk side by side and **scores how well the chunk actually answers the question**. That adds some time, but it dramatically improves the quality of what the model gets. Infinity keeps the **top 5** and drops the other 15.
7. **Open WebUI → Ollama.** Your prompt and those **5 high-value chunks** go to Ollama, and the model answers from *your actual data*.
8. **Ollama → Open WebUI → you.** The answer comes back to the chat window.

Here's what I find really interesting: **Ollama only shows up at the very end.** Almost all the work happened before the model did anything. Several other tools broke the data down, converted it, stored it, and reranked it, and only *then* did the model get its turn to produce the magical answer.

### Scenario 3: Having the AI Write and Run Its Own Code

**The prompt:** *"Write me a Python script that checks whether the services in my AI stack are up, then run it."*

This is where the local AI stops being a chatbot and starts acting like an **agent**.

1. **Open WebUI → Ollama.** Same as before, the prompt and the tool list go to Ollama. This request is different, though: we're asking the model to **create a file**, and that file needs somewhere to live and eventually run. That's Open Terminal's job.
2. **The model writes the script.** At this point it's just text, not a real file.
3. **Tool call #1: save the file.** The model makes a tool call to save the script. Open WebUI sends it to Open Terminal, which writes the file into the **sandboxed home directory** and confirms the task is done.
4. **Tool call #2: run it.** With the first half done, the model makes a second tool call to execute the script it just saved.
5. **Open Terminal runs the script.** It runs the script as written and returns the output to Open WebUI, which passes it to the model.
6. **The model reports back.** The model reads the output, writes a response, and sends it through Open WebUI to you.

### Why Sandboxing Matters

This is a key point. **Giving a model a command line is seriously powerful, and arguably dangerous**, for obvious reasons. AI makes mistakes all the time, and we've all seen some wild news stories lately about what happens when an AI model breaks containment and runs wild on the internet. It's a real risk.

That's why the terminal and script execution run in a **sandboxed environment**. Every execution in the example above happened **inside a Docker container, not on the physical machine**. That insulation is serious protection against a badly written script or command that could destroy data, rewrite configs, or otherwise break your environment.

---

## The Docker Compose File, Block by Block

Quick recap on why Docker is the right deployment choice here. When I say "Docker," what I really mean is **running these tools in containers instead of installing them on bare metal**. Docker is my pick for containers on a single system. Podman works great too, and if you're into torture and suffering, by all means, use Kubernetes. Either way, running a stack like this in containers gives you flexibility and protection you just don't get by installing everything on the base OS.

There are **seven services** in this one compose file. As I said up top, there are hundreds of ways to build something like this. This is how I built mine, and why.

> **Before you copy this:** a few fields are placeholders on purpose: `WEBUI_URL`, `SEARXNG_BASE_URL`, `SEARXNG_SECRET`, and **both** Open Terminal keys. Swap them for your own values before you deploy. More on that in [Running It Yourself](#running-it-yourself) below.

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
      # --- Speech-to-text (Whisper, on the GPU) ---
      - WHISPER_MODEL=large-v3
      - WHISPER_MODEL_DIR=/app/backend/data/cache/whisper/models
      - WHISPER_COMPUTE_TYPE=float16
      - WHISPER_LANGUAGE=en
      # --- Core ---
      - WEBUI_URL=http://your-server:3000/          # the address you use to reach Open WebUI
      # Open WebUI stores webui.db + uploads under DATA_DIR
      - DATA_DIR=/app/backend/data
      - OLLAMA_BASE_URL=http://ollama:11434
      - ENABLE_API_KEYS=true
      # --- Vector DB ---
      - VECTOR_DB=qdrant
      - QDRANT_URI=http://qdrant:6333
      - ENABLE_QDRANT_MULTITENANCY_MODE=true
      # --- Embeddings (run in Ollama, on the GPUs) ---
      - RAG_EMBEDDING_ENGINE=ollama
      - RAG_EMBEDDING_MODEL=mxbai-embed-large:latest   # pull it first: docker exec ollama ollama pull mxbai-embed-large
      - RAG_OLLAMA_BASE_URL=http://ollama:11434
      # --- Document extraction (Tika) ---
      - CONTENT_EXTRACTION_ENGINE=tika
      - TIKA_SERVER_URL=http://tika:9998
      - TIKA_SERVER_VERSION=4                # Tika's major version; bump this when latest-full moves to Tika 5
      # --- Reranking + hybrid search ---
      - ENABLE_RAG_HYBRID_SEARCH=true
      - RAG_RERANKING_ENGINE=external
      - RAG_EXTERNAL_RERANKER_URL=http://infinity:7997/rerank
      - RAG_RERANKING_MODEL=BAAI/bge-reranker-v2-m3
      - RAG_TOP_K=20
      - RAG_TOP_K_RERANKER=5
      - RAG_SYSTEM_CONTEXT=true
      # --- Web search (SearXNG) ---
      - ENABLE_WEB_SEARCH=true
      - WEB_SEARCH_ENGINE=searxng
      - SEARXNG_QUERY_URL=http://searxng:8080/search
      - WEB_SEARCH_RESULT_COUNT=3
      - WEB_SEARCH_CONCURRENT_REQUESTS=10
      # --- Open Terminal (admin-level, proxied through Open WebUI) ---
      # "key" MUST match OPEN_TERMINAL_API_KEY in the open-terminal service below
      - 'TERMINAL_SERVER_CONNECTIONS=[{"id":"open-terminal","name":"Open Terminal","url":"http://open-terminal:8000","key":"CHANGE-ME-open-terminal-key","auth_type":"bearer"}]'
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
      - tika
      - searxng
      - infinity
      - open-terminal

  qdrant:
    image: qdrant/qdrant:latest
    container_name: qdrant
    restart: unless-stopped
    volumes:
      - /ai-box/qdrant/storage:/qdrant/storage

  tika:
    image: apache/tika:latest-full           # major version must match TIKA_SERVER_VERSION in open-webui
    container_name: tika
    restart: unless-stopped

  searxng:
    image: searxng/searxng:latest
    container_name: searxng
    restart: unless-stopped
    configs:
      - source: searxng_settings                  # defined at the bottom of this file
        target: /etc/searxng/settings.yml
    volumes:
      - searxng_data:/var/cache/searxng
    environment:
      - SEARXNG_BASE_URL=http://your-server/
      - SEARXNG_SECRET=CHANGE-ME-long-random-string  # openssl rand -hex 32
      - UWSGI_WORKERS=4
      - UWSGI_THREADS=4
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
      - OLLAMA_KEEP_ALIVE=-1
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
      OPEN_TERMINAL_API_KEY: "CHANGE-ME-open-terminal-key"  # must match "key" in TERMINAL_SERVER_CONNECTIONS
      OPEN_TERMINAL_PACKAGES: "ripgrep tree curl"
      OPEN_TERMINAL_PIP_PACKAGES: "httpx polars"
      OPEN_TERMINAL_MULTI_USER: "true"
    volumes:
      - open_terminal_home:/home/user
      # - /var/run/docker.sock:/var/run/docker.sock  # uncomment for Docker CLI access
    security_opt:
      - no-new-privileges:false
    cap_drop:
      - NET_ADMIN
      - SYS_ADMIN

  infinity:
    image: michaelf34/infinity:latest
    container_name: infinity
    restart: unless-stopped
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

configs:
  searxng_settings:
    content: |
      use_default_settings: true
      server:
        limiter: false        # limiter is for public instances; this one is internal-only
        image_proxy: true
      search:
        formats:
          - html
          - json              # required, or Open WebUI's web search fails

volumes:
  searxng_data:
  open_terminal_home:
  infinity-cache:
```

### Open WebUI

Open WebUI is the front door for most user interaction, like you saw in the scenarios. A few things worth calling out:

- **The `:cuda` image tag.** Without it, Open WebUI runs speech-to-text (Whisper) on your CPU. With three RTX 3090s in the box, I want it on the GPUs instead. The `deploy.resources.reservations` block below it tells the container which GPUs it can use. In my case, all of them.
- **Port `3000`** is the external port clients use to reach the web interface.
- **The `environment` block.** You can set all of this in the Open WebUI GUI, but defining it here means I don't have to click through the admin settings every time I redeploy. Here's what it sets:
  - Speech-to-text (Whisper) settings
  - My system URL (`WEBUI_URL`)
  - The connection to the local Ollama container
  - The connection to Qdrant
  - Reranking and hybrid search settings
  - The embedding model
  - The connections to SearXNG, Tika, and Open Terminal
- **The Open Terminal key gotcha.** The `key` inside `TERMINAL_SERVER_CONNECTIONS` **must match** `OPEN_TERMINAL_API_KEY` in the `open-terminal` service further down. If they don't match, it won't work.
- **`volumes`** map Open WebUI's data to fixed paths on the host, so your users, chats, and settings survive a rebuild.
- **`depends_on`** keeps Open WebUI from starting until every supporting tool container is up.

There's a lot in there I'm flying past for the sake of an overview. Let me know if a deep line-by-line breakdown is something you'd want in a follow-up. For now, if you have an NVIDIA GPU, you can use my settings exactly as they are. They're tuned for the best performance I've been able to get out of this box.

### Qdrant

Super basic, because Qdrant doesn't need much special configuration. It uses the latest image, sets a container name and restart policy, and, most importantly, **mounts a volume on the local filesystem** so Qdrant's databases persist.

You might notice there's **no `ports` block**. That's deliberate. Nothing outside the shared Docker network needs to reach Qdrant, so I don't expose it.

### Apache Tika

Just as simple. It uses the **`latest-full`** image tag (the full build includes OCR support), plus a name and restart policy. Tika doesn't need permanent storage, and like Qdrant, it **isn't exposed to the LAN** because there's no reason to.

One small gotcha: the comment in the file says the Tika image's major version must match `TIKA_SERVER_VERSION` in the Open WebUI block. When `latest-full` moves to a new major version, bump that value.

### SearXNG

A bit more involved, but not bad:

- Latest image, container name, and restart policy.
- A volume for the container's **ephemeral cache**.
- Environment variables for your **host URL** (`SEARXNG_BASE_URL`) and the **secret key** (`SEARXNG_SECRET`) SearXNG uses to sign its cookies. **You need to fill both of these in.**
- A **`logging` block** that caps how much log data SearXNG can write, so it can't fill up your disk.
- A **`configs`** reference. SearXNG needs a couple of special settings to work with Open WebUI, chiefly JSON output enabled. That config is defined at the bottom of the compose file and gets built on every run. More on that below.

### Ollama

Maybe a little surprising, but Ollama is pretty lightweight here:

- Latest image and container name.
- **Port `11434` exposed to my LAN.**
- **`OLLAMA_CONTEXT_LENGTH`** explicitly sets the context window size.
- **`OLLAMA_KEEP_ALIVE=-1`** tells Ollama to **never unload a model** once it's loaded.
- GPU access to the physical cards to run the models.

A couple of notes on my Ollama setup:

**Why it's exposed outside Docker:** I like connecting things like Continue in VS Code, and other tools that want local models, directly to Ollama. That comes with risk, because **Ollama is unauthenticated in this config**. I own and control this private network, so I'm comfortable with it. If you're not, **delete the entire `ports` block** and you're done.

**Why keep-alive is `-1`:** waiting for a big model like Qwen3.6 to load adds a lot of delay before anything happens. Keeping it resident skips that wait. If that doesn't matter to you, remove the setting and Ollama will unload idle models on its default timer.

### Open Terminal

- Latest image, container name, and restart policy.
- **`OPEN_TERMINAL_API_KEY`**: the key Open WebUI uses to connect. Remember, it has to match the key in the Open WebUI block.
- **`OPEN_TERMINAL_PACKAGES`** and **`OPEN_TERMINAL_PIP_PACKAGES`**: the system and Python packages installed and available on deployment. Add more as you need them. My defaults have worked well so far.
- **`OPEN_TERMINAL_MULTI_USER`**: each logged-in user gets their own account and home directory.
- A **volume** for persistent home directories, plus a few **security settings** that drop capabilities the sandbox doesn't need.

### Infinity

Latest image, container name, and restart policy, plus a **cache volume** and **GPU access** so it can run its reranking model on the card. The `command` block is passed to Infinity at startup and tells it **which reranking model to load** (`BAAI/bge-reranker-v2-m3`) and how to run it. Below that is a **`healthcheck`**, so Docker knows if something goes wrong inside the container.

### Configs and Volumes

The **`configs`** section holds the SearXNG configuration I mentioned earlier. Every time you run this compose file, it generates that config and attaches it to your SearXNG instance automatically. The key line is **`json`** under `search.formats`. Without it, Open WebUI's web search fails.

Finally, the **`volumes`** section at the bottom declares the internal Docker volumes used by the containers above. That's it.

---

## Running It Yourself

**1. Fill in the placeholders.** Before you deploy, replace these values with your own:

| Field | Where | What to put there |
|---|---|---|
| `WEBUI_URL` | `open-webui` | The address you use to reach Open WebUI, e.g. `http://192.168.1.50:3000/` |
| `SEARXNG_BASE_URL` | `searxng` | Your host's URL |
| `SEARXNG_SECRET` | `searxng` | A long random string |
| `key` in `TERMINAL_SERVER_CONNECTIONS` | `open-webui` | Your Open Terminal API key |
| `OPEN_TERMINAL_API_KEY` | `open-terminal` | **The same** Open Terminal API key |

An easy way to generate the secret and the API key:

```bash
openssl rand -hex 32
```

**Heads up on storage paths:** Open WebUI, Qdrant, and Ollama use bind mounts under `/ai-box/...`. If you don't have that path on your host, change those lines to wherever you keep persistent data. Model weights in particular get big fast, so put `/ai-box/ollama` on a disk with plenty of room.

**2. Bring up the stack.** From the directory holding your `docker-compose.yml`:

```bash
sudo docker compose up -d
```

Let it finish. The first run pulls every image, plus the Whisper and reranker models, so give it some time.

**3. Pull the embedding model.** Open WebUI is configured to use `mxbai-embed-large` through Ollama for embeddings, so pull it down:

```bash
sudo docker exec ollama ollama pull mxbai-embed-large
```

Then point a browser at `http://your-server:3000`, create your admin account, pull down a chat model, and start experimenting.

---

## Final Thoughts

So that's the entire stack: **seven containers, one compose file, one host.** It can search the web, read my documents, and write and run its own code, and none of it leaves my homelab for a cloud model.

If you saw [Part 1]({% post_url 2026-07-31-building-my-ai-homelab-server-three-rtx-3090s %}), you know one of the big reasons for all of this is **digital sovereignty**: more control over what AI I use and how I use it. I don't think we're out of the woods yet on what governments will do, or how they'll control what AI you can access and how. This box is my answer to that problem, the only way I know how: do it myself.

This system has been incredibly useful. I've already started moving my workflows from Claude and ChatGPT over to it. I've opened up access to a few other people, and they're using it to build real things. In fact, **if you're a root-level member of our Patreon or YouTube membership program, you get access to use this system!**

One of the coolest things about this setup is how flexible it is. When a new open-weight model or tool comes out, I pull it down, spin it up, and start experimenting right away. This platform is everything I wanted in locally hosted AI, and I couldn't be happier with it.

Oh, and one last thing. I've probably said it too many times already, but there are a ton of ways to configure and deploy a stack like this, and a ton of alternative tools to choose from. **If you've made it this far, tell me what tools you're using, why you're using them, and how it's going.** I'm on a quest to make this system the best it can be, and I know this community has knowledge that can help me and everyone else. Share it in the comments or come find us in the Discord.

---

Thanks for watching and reading, folks, and thank you to the fine people who support us through **Patreon** and the **YouTube Membership** program. If you'd like to support what we do here, consider checking those out. Join our community **Discord** and chat with me and like-minded homelabbers, geeks, and nerds, and as always, we'll see you on the next one!
