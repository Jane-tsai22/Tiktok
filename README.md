# 🇧🇪 Belgian Politicians TikTok Scraper & Corpus Builder

## 📌 Project Overview
This project provides an automated pipeline to collect TikTok video data, audience comments, and speech transcriptions from Belgian politicians, focusing on the seven main Flemish parties (PVDA, Groen, Vooruit, CD&V, Open VLD, N-VA, Vlaams Belang). 

The tool collects video metadata (views, likes, post dates), captions, audio transcripts, and audience comments within specific timeframes. 

## 🎯 Key Features
* **Auto-Grouping:** Reads a list of politicians, filters them, and automatically groups them by political party for streamlined batch scraping.
* **Smart Date Filtering:**
    * Allows custom `start_date` and `end_date` boundary settings.
    * **Cost Optimization:** Because TikTok profiles display videos chronologically, the script automatically stops fetching once it reaches videos older than your target `start_date`, saving API quota.
* **Dual Scraping Strategy:**
    1. **Official Accounts:** Scrapes all historical videos from politicians' profile URLs.
    2. **Keyword Search:** Tracks mentions and posts for politicians without an official account.
* **Deep Comment Extraction:** Leverages RapidAPI (`tiktok-video-feature-summary`) with pagination logic and incremental saving (resume from breakpoint) to collect complete comment sections for every video.
* **Cloud-Grade Speech-to-Text (Google Cloud STT V2):** 
    * Replaces unreliable auto-captions with **Google Cloud Speech-to-Text V2**.
    * Features **Audio Chunking Pipeline**: Automatically converts video audio via `yt-dlp` / `ffmpeg` and chunks long audio files to optimize API processing and maintain high transcription accuracy across Belgian Dutch (Flemish) and French dialects.
* **Clean Excel Output:** Formats and exports data into Excel (`.xlsx`) files, preventing formatting errors, corrupted line breaks, and broken emojis.

## 🛠️ Tech Stack
* **Language:** Python 3.8+
* **Data Processing & Analytics:** `pandas`, `openpyxl`, `datetime`, `os`, `glob`
* **API & Scraping:**
    * `requests` (for HTTP API interactions)
    * `python-dotenv` (for secure environment variable management)
    * **Video & Comment API:** [RapidAPI](https://rapidapi.com/) (`tiktok-video-feature-summary.p.rapidapi.com`)
* **Audio Extraction & Speech Recognition:**
    * `yt-dlp` & `ffmpeg` (Audio extraction, formatting, and time-based chunking)
    * **Google Cloud Speech-to-Text API (V2)** (Enterprise-grade multilingual transcription)

## 📁 Workflow

### Phase 1: Data Preparation
The script reads your master Excel file containing politician profiles. It uses Pandas to split the list by political party and generates standalone input files (e.g., `Vooruit_with_account.xlsx`).

### Phase 2: Batch Video Scraping
Using the `glob` module, the script iterates through each party's spreadsheet to collect post data:
1. Filters posts strictly within the defined date window (e.g., `2019-05-27` to `2024-06-09`).
2. Handles pagination automatically via cursors.
3. Implements rate-limiting (`time.sleep`) and error handling to manage HTTP limits.
4. Outputs raw metadata (`video_id`, `url`, `caption`, `views`, `likes`) into `[PartyName]_official.xlsx`.

### Phase 3: Deep Data Extraction & NLP Corpus Building
1. **Comment Scraping:** 
   Feeds the collected video URLs into RapidAPI (`tiktok-video-feature-summary`). Runs paginated requests to harvest user comments, reply counts, and like counts, exporting them to `[PartyName]_official_comments.xlsx`.
2. **Audio Chunking & Transcription (Google Cloud STT V2):** 
   Extracts audio streams from videos using `yt-dlp`. Long audio files are dynamically split into smaller chunks, sent to **Google Cloud Speech-to-Text V2**, and stitched back together to build precise speech transcripts for linguistic analysis.

## 🚀 Quick Start

### 1. Install Dependencies
```bash
pip install pandas requests python-dotenv openpyxl google-cloud-speech