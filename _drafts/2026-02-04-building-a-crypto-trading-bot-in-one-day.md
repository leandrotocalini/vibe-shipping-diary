---
layout: post
title: "Building a crypto trading bot in one day"
date: 2026-02-04 00:00:00 +0000
author: Leandro Tocalini Joerg
categories: [dev, ai]
tags: [crypto, trading, bots, shipping]
---

![Hero image](/assets/img/posts/shifty-hero.png)

I built a crypto trading bot in one day. Not a toy — a real, deployed, trading-with-actual-money bot. Here's how it happened.

## The idea

It started as a moonshot. I have a simple thesis: high-frequency micro-trades with small edges compound over time. 500+ trades per week, each targeting 0.3-0.5% profit. No leverage, just spot trading on Binance.

The goal wasn't to get rich quick. It was to build something that could run autonomously, make emotionless decisions, and learn from the process.

## From idea to deployed in hours

I used my two-agent setup: Alfred (my WhatsApp bot) for planning and architecture, Claude Code for implementation.

The conversation went something like:

> "Un bot para trading de crypto. Headless. Binance. Sin apalancamiento. 500+ trades por semana."

Within minutes, we had:
- A project structure
- A CLAUDE.md with full technical spec
- Milestones M1-M6 defined

Then I told Claude Code: "Read CLAUDE.md and start M1."

Four hours later, I had:
- WebSocket price streaming
- RSI + momentum signal generation
- Position sizing and risk management
- Paper trading mode
- Fly.io deployment with status endpoint

## The bugs were educational

### The stablecoin ghost

First deploy, the bot showed a "position" in USDCUSDT. But I never traded USDC — I just had some USDC in my wallet from a deposit.

The bot was detecting any crypto balance as a "position to manage." Classic case of the bot being too clever. Fix: filter out stablecoins from position recovery.

### Overbought markets don't care about your bot

Deployed at night, excited to see it trade. Checked the logs:

```
ETHUSDT SELL signal - RSI:69.0
XRPUSDT SELL signal - RSI:66.8
Cycle complete. Positions: 0
```

The bot was working perfectly — generating sell signals because the market was overbought. But it had no positions to sell. And it wouldn't buy because RSI > 40.

This is actually the bot doing its job: not buying at tops. The temptation is to tweak parameters to "make it do something." That's the exact emotional trading the bot is supposed to prevent.

## Tech stack

- **Python 3.11** + asyncio
- **python-binance** for exchange API
- **WebSocket** for real-time data (no polling rate limits)
- **Fly.io** for deployment (stateless, restarts are fine)
- **nginx** serving status.json for monitoring

Total lines of code: ~1,000. Not 50,000 like Freqtrade. Just enough to work.

## What I learned

1. **Stateless is a feature.** Fly.io can restart anytime. The bot recovers state from Binance API on startup. No database needed.

2. **The bot is not the strategy.** The bot is infrastructure. The strategy is parameters. Don't conflate them.

3. **Waiting is trading.** A bot that doesn't trade in unfavorable conditions is working correctly.

4. **Agents accelerate everything.** From idea to deployed production bot in one day, with proper architecture, tests, and monitoring. That's the leverage.

## What's next

- [ ] Bollinger Bands strategy (double confirmation)
- [ ] BTC sentiment check (don't trade altcoins when BTC dumps)
- [ ] Backtesting with historical data
- [ ] Actually making money (TBD 😅)

The bot is running. The market is overbought. I'm going to sleep.

Let's see if the fox catches something overnight. 🦊

---

*sHiFTy: Because the best trades happen when you're not watching.*
