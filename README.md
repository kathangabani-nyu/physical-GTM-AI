<div align="center">

# Peel — Reach buyers where screens can't

**Peel treats a billboard as an account-based marketing channel.** It finds the streets where your ideal customers concentrate, recommends real permitted billboard inventory there, generates creative from your existing brand rules, and predicts the attention it'll get from the pedestrians who are your ICPs.

![Peel — Reach buyers where screens can't](docs/assets/hero.png)

[![Next.js](https://img.shields.io/badge/Next.js-15-000000?logo=nextdotjs&logoColor=white)](https://nextjs.org/)
[![React](https://img.shields.io/badge/React-19-61DAFB?logo=react&logoColor=black)](https://react.dev/)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.7-3178C6?logo=typescript&logoColor=white)](https://www.typescriptlang.org/)
[![Mapbox GL](https://img.shields.io/badge/Mapbox_GL-3-000000?logo=mapbox&logoColor=white)](https://www.mapbox.com/)
[![deck.gl](https://img.shields.io/badge/deck.gl-9-6E40C9)](https://deck.gl/)
[![Three.js](https://img.shields.io/badge/Three.js-r185-000000?logo=threedotjs&logoColor=white)](https://threejs.org/)
[![OpenAI](https://img.shields.io/badge/OpenAI-gpt--4o_·_gpt--image--1-412991?logo=openai&logoColor=white)](https://openai.com/)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

</div>

---

## The problem

Out-of-home (OOH) is one of the last large ad channels still bought on gut feel. Buying a billboard today means:

- **You can't easily tell where your actual ICPs are** in a city.
- **You have no way to predict whether a board will actually be visible** before you spend.

Meanwhile, every growth team already knows their ICP and runs ABM digitally. Peel asks: *what if a billboard were just another ABM surface* — targetable by account density, generatable from your brand, testable before spend, and wired straight into outbound?

![Opportunity map — target accounts clustered into a geofenced hotspot with ranked, fit-scored clusters](docs/assets/opportunity-map.jpg)

<div align="center"><sub>Peel clusters ICP-matched companies into geographic opportunity "blobs" — here a B2B tech-buyer hotspot in SoMa — and ranks each by fit and board reach.</sub></div>

---

## How it works

The product is a **guided pipeline**. Each stage feeds the next.

| # | Stage | What happens |
| --- | --- | --- |
| **1** | **Onboard from a URL** | Drop in a company website. Peel scrapes the homepage and infers a structured **creative brief** (identity, voice, color palette, core message, CTA) and an **ICP** (audience, segment, intent signals) — shown as an animated "scan." |
| **2** | **Find opportunity blobs** | Against a dataset of San Francisco businesses, Fiber AI clusters companies matching the ICP into geographic **opportunity blobs** — e.g. *"SoMa Finance SaaS Cluster."* Blobs re-rank live as the ICP is edited. |
| **3** | **Pick placements** | For the selected blob, Peel ranks real, permitted billboard inventory (**SF General Advertising Sign** records) by visibility, dwell time, and proximity to target accounts. |
| **4** | **Generate local creative** | It produces billboard creative tuned to the brief and street context, then composites it onto the real sign geometry in a 3D scene. |
| **5** | **Test attention** | A 3D map runs a crowd + traffic simulation; **Vision Studio** uses a vision-language model to predict what a passer-by actually notices — *board found? / first thing the eye lands on / brand recall / legibility / shareability* — from Street View or an uploaded photo. |
| **6** | **Export & hand off** | One click renders a **PDF campaign report**; matched accounts drop into an outbound queue wired to **Orange Slice** for coordinated follow-up. |

### Inside the app

![3D city scene with live pedestrian, employee, and vehicle simulation](docs/assets/3d-scene.jpg)

<div align="center"><sub>A depth-correct 3D city (Mapbox + deck.gl) with interleaved 3D buildings, a live crowd simulation, traffic flow, and pedestrian "vision" agents — across 559 permitted SF boards.</sub></div>

<br />

![Physics-grounded visibility scoring and a ranked, contact-mapped Target ICP list](docs/assets/visibility-icp.jpg)

<div align="center"><sub>Visibility, recall, glance capture, time-to-notice, and scene-attention share scored per board, beside a ranked, contact-mapped Target ICP list.</sub></div>

<br />

![Vision Studio journal replaying how a pedestrian sees a board from the street](docs/assets/vision-journal.jpg)

<div align="center"><sub>Vision Studio replays how a specific pedestrian profile actually sees a board from the street — with SEEN / NOTICE / RECALL read-outs beside the creative brief that drives the mockup.</sub></div>

---

## Notable features

- **URL → creative brief + ICP** — an animated "scan" of the company site returns a structured brief and ideal-customer profile.
- **Opportunity blobs** — physical-world clusters of ICP companies, scored and live-re-ranked as the ICP is edited.
- **Real permitted inventory** — ranked SF General Advertising Sign records with city permit deep-links, visibility scores, and dwell estimates — not invented placements.
- **AI-generated local creative** — board art generated from the brief and composited onto the actual sign geometry.
- **Depth-correct 3D city** — a Mapbox + deck.gl scene with interleaved 3D buildings, live crowd simulation, traffic flow, and pedestrian "vision" agents that capture what they'd see.
- **Vision Studio attention testing** — a VLM scores a street scene for what grabs the eye, brand recall, legibility, and shareability, with a heuristic saliency fallback.
- **Live environment** — current weather and time-of-day (via Open-Meteo) drive the look of the scene.
- **One-click hand-off** — a PDF campaign report and an Orange Slice outbound queue.

---

## Why we built this

OOH is one of the last large ad channels that's still bought on gut feel — while every growth team already knows their ICP and runs ABM digitally. We wanted to ask: what if a billboard were just another ABM surface — **targetable by account density, generatable, testable before spend, and connected to outbound?** Peel is that experiment.

---

## Architecture

```
┌──────────────────────────────────────────────────────────────┐
│  Next.js 15 App Router (React 19, TypeScript, Tailwind v4)     │
│                                                                │
│  /            Guided pipeline — brief/ICP scan, opportunity    │
│               blobs, ranked boards, creative + outbound        │
│  /map         Full-screen 3D Mapbox + deck.gl scene            │
│  /vision      Vision Studio — VLM attention testing            │
│  /sightline   Line-of-sight / visibility inspector             │
│                                                                │
│  /api/*       company-brief · opportunities · generate-creative│
│               vision-simulate · streetview · current-conditions│
│               outbound · campaign-report (PDF) · …             │
└──────┬─────────────────┬──────────────────┬──────────────────┘
       │                 │                  │
 ┌─────▼──────┐   ┌───────▼───────┐   ┌──────▼───────────┐
 │  OpenAI     │   │  Fiber AI      │   │  Orange Slice     │
 │  gpt-4o     │   │  (account      │   │  (outbound /      │
 │  gpt-image-1│   │   enrichment)  │   │   sales-ops)      │
 └────────────┘   └───────────────┘   └──────────────────┘
       │                 │                  │
 ┌─────▼─────────────────▼──────────────────▼──────────────┐
 │  Street View · Open-Meteo · SF GASP + Accela permits ·   │
 │  SF roads & pedestrian-count datasets (prefetched)       │
 └──────────────────────────────────────────────────────────┘
```

---

## Tech stack

**Framework & UI**

- Next.js 15 (App Router) · React 19 · TypeScript
- Tailwind CSS v4
- Zod for schema validation

**Maps, 3D & simulation**

- Mapbox GL + react-map-gl
- deck.gl (core, layers, mapbox, react, widgets) for interleaved 3D layers, crowd, and traffic-flow rendering
- three.js + @react-three/fiber + @react-three/drei for 3D billboard meshes
- @huggingface/transformers for in-browser ML (saliency)

**AI & vision**

- OpenAI — `gpt-4o` vision for attention scoring, `gpt-image-1` for creative generation
- Google Street View imagery for real-scene attention testing

**Automation & data**

- Playwright — site screenshots and headless PDF report rendering
- Open-Meteo — live weather / current conditions
- SF Planning General Advertising Sign Program records + Accela permit portal for real inventory
- SF roads & pedestrian-count datasets (prefetched at build)
- Fiber AI for account enrichment
- Orange Slice for the outbound / sales-ops workflow

---

## Getting Started

```bash
# 1. Configure environment
cp .env.local.example .env.local   # Mapbox, OpenAI, Fiber AI, Orange Slice keys

# 2. Install dependencies
npm install

# 3. Run the dev server
npm run dev
```

Open **http://localhost:3000**.

The app is resilient to missing keys: opportunity scoring, the 3D scene, and creative generation fall back to bundled data, heuristic saliency, and SVG mockups. Add keys in `.env.local` to enable live enrichment, `gpt-image-1` creative, VLM attention scoring, and Street View compositing.

### Useful scripts

| Command | Purpose |
| --- | --- |
| `npm run dev` | Start the local dev server |
| `npm run build` | Prefetch map/pedestrian data and build for production |
| `npm run convex:dev` | Run the Convex backend locally |
| `node scripts/scrape-billboards.mjs` | Re-pull the live SF billboard inventory |

---

## Billboard data

Real San Francisco billboard inventory ships in [`data/`](data/), scraped from SF Planning's **General Advertising Sign Program (GASP)** — the city's registry of permitted billboards. The current dataset contains **559 signs with exact WGS84 coordinates**.

| File | Use |
| --- | --- |
| `data/sf-billboards.geojson` | Mapbox-ready `FeatureCollection` of points |
| `data/sf-billboards.csv` | Spreadsheet / analysis format |

Each sign carries its street address, permit status and lifecycle dates, city permit number with Accela deep-links, and assigned planner contact. GASP is *physical inventory*, not a rental marketplace — bookable metadata (dimensions, impressions, CPM, pricing) would come from commercial OOH platforms or seller-provided data, and the app is explicit about which fields are estimated.

Refresh the data at any time (no API key required):

```bash
node scripts/scrape-billboards.mjs
```

---

## Repository layout

```
app/
  components/      3D map, billboard meshes, crowd & traffic layers, flows
  lib/             visibility, saliency, attention, creative, campaign report
  api/             company-brief, opportunities, creative, vision, streetview, PDF
  map/ vision/ sightline/   feature routes
convex/            realtime backend functions
data/              scraped SF billboard inventory + enrichment caches
scripts/           data prefetch, scraping, build tooling
public/            static map data and creative assets
docs/              design notes and screenshots
```

---

## Roadmap

- **Measurement layer** — brand-search lift, QR / short-code response, geofenced conversion, and holdout-market experiments to move from modeled attention to attributed ROAS.
- **National inventory** — extend beyond SF GASP to commercial OOH marketplaces.
- **CRM push** — direct sync of the staged outbound queue into HubSpot, Salesforce, and Attio.

---

## License

Released under the [MIT License](LICENSE).

---

![Peel — launch your physical ad campaigns where your buyers won't miss them](docs/assets/banner.png)

<div align="center">

Built for the physical GTM era — where the best channel is the one your competitors forgot.

</div>
