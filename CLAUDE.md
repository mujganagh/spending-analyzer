# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Single-file web application (`index.html`) for analyzing personal spending across multiple financial statements. Processes files entirely in the browser via the Anthropic Claude API — no backend, no build step, no dependencies.

## Running the App

```bash
open index.html   # or serve it via any static file server
```

No installation, build, or package management needed.

## Architecture

The entire app lives in `index.html` as vanilla HTML/CSS/JS. Key flows:

**Two-pass Claude API pipeline:**
1. **Extract** — each uploaded file is sent to `claude-sonnet-4-20250514` individually; Claude returns `[{date, description, amount}]` JSON per file
2. **Categorize** — all extracted transactions are combined and sent in a second call; Claude returns a full summary with categories, insights, and tagged transactions

**File type handling:**
- Images (PNG/JPG/WEBP): sent as base64 via Claude's vision capability
- PDFs: sent as base64 document blocks
- CSV/TXT: sent as raw text (truncated to 40,000 chars)

**In-memory state:**
```js
let files = [];            // uploaded files with status tracking
let allTransactions = [];  // combined extracted transactions
let allSources = [];       // unique card/account names for tab filtering
```

**UI rendering** is fully imperative — `renderResults()` rebuilds the results section from state after each analysis. Category/source filters recalculate display without re-calling the API.

## API Integration

- Endpoint: `https://api.anthropic.com/v1/messages`
- Model: `claude-sonnet-4-20250514`
- The user supplies their API key at runtime (no key stored in code)
- Responses are stripped of markdown fences before `JSON.parse()`

## Deployment

Host `index.html` on any static hosting (GitHub Pages, Netlify, Vercel). No backend needed.
Remote: `https://github.com/mujganagh/spending-analyzer.git`
