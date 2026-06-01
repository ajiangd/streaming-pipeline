# Streaming Pipeline

A fully deployed educational site explaining how adaptive bitrate streaming works — built on real AWS infrastructure as a learning project ahead of a software engineering internship at a streaming platform.

**Live site → [d14tpktysc56ha.cloudfront.net](https://d14tpktysc56ha.cloudfront.net) **

---

## What this is

Modern streaming platforms don't serve a single video file. They encode video at multiple quality levels, slice it into small segments, and let the player pick the right quality in real time based on available bandwidth. This is called **Adaptive Bitrate Streaming (ABR)**.

This project explains how that works — and then demonstrates it live using a real HLS stream delivered through an AWS pipeline I built from scratch.

---

## The live demo

The demo page streams a real video using HLS.js through an actual S3 + CloudFront pipeline. You can:

- Watch **quality level switches** happen in real time as the ABR algorithm responds to bandwidth
- Lock to a specific rendition (**1080p / 720p / 360p**) and watch the segment log update
- See the **quality history chart** track which rendition HLS.js chose over time
- See the **segment size chart** show how higher quality segments are physically larger files
- Watch the **live stats panel** update every second — bitrate, buffer health, bandwidth estimate, dropped frames

---

## Tech stack

| Layer | Technology |
|---|---|
| Video transcoding | AWS MediaConvert |
| Origin storage | AWS S3 (static website hosting) |
| CDN | AWS CloudFront (400+ edge locations) |
| Streaming protocol | HLS (HTTP Live Streaming) |
| Player | HLS.js |
| Frontend | Vanilla HTML / CSS / JS |

---

## AWS architecture

```
Source MP4
    ↓
AWS MediaConvert
    → 1080p @ 5 Mbps  ─┐
    → 720p  @ 2.5 Mbps ─┼→ S3 bucket (origin) → CloudFront CDN → Browser / HLS.js
    → 360p  @ 800 Kbps ─┘
         ↑
    6-second fMP4 segments
    Master .m3u8 playlist
```

The video is transcoded into three renditions with 6-second segments. CloudFront sits in front of S3 as the CDN layer — the browser never talks directly to the origin. HLS.js fetches the master playlist, reads the available renditions, and runs the ABR algorithm to pick the right quality on every segment fetch.

---

## Pages

| Page | Description |
|---|---|
| `/index.html` | Landing page — scrolling explainer of how video streaming works |
| `/abr.html` | Deep dive into Adaptive Bitrate Streaming |
| `/hls.html` | How HLS works — .m3u8 playlists, segments, Apple ecosystem |
| `/dash.html` | How DASH works — MPD manifests, codec agnosticism, comparison table |
| `/cmaf.html` | How CMAF unifies HLS and DASH — encode once, deliver everywhere |
| `/demo.html` | Live HLS stream with real-time stats, charts, and segment log |

---

## What I learned

- How the full ABR pipeline works end-to-end — from encoding to CDN to player
- The difference between HLS, DASH, and CMAF and when each is used
- Setting up AWS S3 for static website hosting and navigating IAM permissions
- Configuring CloudFront as a CDN layer in front of S3, including cache invalidation
- Running AWS MediaConvert jobs to produce multi-rendition HLS output
- Using the HLS.js API — `FRAG_LOADING`, `LEVEL_SWITCHED`, `autoLevelCapping`, `nextLevel`
- Debugging real CDN issues: 504 origin timeouts, Block Public Access conflicts, cache invalidation

---

## Running locally

No build step needed — open any `.html` file directly in a browser. The demo page streams from the live CloudFront URL so it requires an internet connection.

```bash
git clone https://github.com/ajiangd/streaming-pipeline
cd streaming-pipeline
open index.html
```

---

## Project structure

```
streaming-pipeline/
├── index.html     # Landing page
├── abr.html       # ABR explainer
├── hls.html       # HLS explainer
├── dash.html      # DASH explainer + protocol comparison table
├── cmaf.html      # CMAF explainer
├── demo.html      # Live demo player
└── README.md
```
