# Agentic coding

I have been a daily user of coding AI since 2021, when GitHub launched the first Copilot beta and I was fortunate enough to secure one of the limited early spots. Since then, I have gone through many phases. In 2022, I rushed to get on OpenAI's first public ChatGPT (GPT-3.5) beta, and soon after I began tracking the nascent AI coding and agentic benchmarking scene.

A lot of synthetic benchmarks were proposed, but to me, the real value was in [SWE-bench](https://www.swebench.com/), the [LM Arena](https://arena.ai/leaderboard/text/coding), [GAIA](https://huggingface.co/spaces/gaia-benchmark/leaderboard), and the [ARC-AGI](https://arcprize.org/arc-agi) series of benchmarks.

The SWE-Bench benchmark in poarticular led me to discover and experiment with many of the proto-agentic coding harnesses back when those terms had not been coined yet. I ran them all -- SWE-Agent, AutoCodeRover, Moatless Tools, Aider, and even contributed code to a few of the early agentic coding frameworks. This taught me a lot about the different approaches to multi-agent (multi-role / multi-mode 'boomerang' style) AI coding, Mixture of Models, prompting strategies, AI retrieval systems, and more.

I became obsessed with new models and AI architectures. Artificial Intelligence has been an obsession of mine since I was a young teenager in the 1990s, but since 2014 I must have read more than 500 AI papers around language models, transformers, Reinforcement Learning, RNNs, LSTMs/GRUs, spiking neural nets, continuous learning, State Space Models, JEPAs, world models, AI memory, and neuroscience. In early 2024, I built my own LLM benchmarking suite, which I have since used to evaluate more than 200 large language models. Whenever something new comes out, I put it under a microscope and try to pick it apart, understand its architecture, and evaluate it on the aspects of my benchmarks (Coding, Grounded recall, Wordplay, Associative logic, and Planning). We have truly come a long way since GPT-3, and I have collected the receipts to prove it.

The next phase had me deep-diving into the tooling/harness side. Spanning 2024-2025, I did extensive hands-on testing of different coding IDEs and VS Code plugins: Cursor, Continue, Copilot Agent Mode, OpenHands, Cline, Roo Code, Kilo Code, Windsurf, Antigravity, Codex, Jules, Claude Code, and many more. Since mid-2025, I have been using Claude Code almost exclusively, though mostly through the [Conductor](https://www.conductor.build/) multi-agent harness which wraps around it for convenient orchestration of many agents in parallel.

I pay for the expensive Claude Code MAX plan. It's expensive, yes, but after spending more than $100 per day two days in a row on the pay-as-you-go Claude API in the summer of 2025, I couldn't afford not to.

I am a heavy user. I want the best. Currently (April 2026), that is Claude Opus 4.6.

## My setup

My setup is minimal. I use mostly vanilla Claude Code (Opus 4.6 1M) with Conductor and a relatively short global CLAUDE.md file. You want to throw a >100K token system prompt at Claude in order to micromanage every aspect of the AI SDLC? Go ahead, but I hope you're doing deep weekly ablation tests to verify that all of that complexity is actually adding value, especially since this space changes so rapidly that you might be overfitting to last week's harness and model capabilities, and ending up hurting your performance without ever realizing it.

I don't run OpenClaw (yet / currently).

I use [RTK](https://github.com/rtk-ai/rtk). I am considering [Caveman Compression](https://github.com/wilpel/caveman-compression).
