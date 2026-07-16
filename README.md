# 🇧🇪 Belgian Politicians TikTok Scraper & Corpus Builder


## 📌 Project Overview
This project provides an automated tool to collect TikTok data from Belgian politicians, with a focus on the seven main Flemish parties (PVDA, Groen, Vooruit, CD&V, Open VLD, N-VA, Vlaams Belang). 

By combining **RapidAPI** and **Apify**, this scraper bypasses common anti-bot blocks. It accurately collects video metadata (views, likes, post dates), captions, and comments within specific timeframes. This creates a high-quality, multilingual dataset for Natural Language Processing (NLP), sentiment analysis, and topic modeling.

## 🎯 Key Features
* **Auto-Grouping:** Reads an Excel list of politicians, filters them, and automatically groups them by political party for easier scraping.
* **Smart Date Filtering:**
    * Allows you to set a `start_date` and `end_date`.
    * Optimizes API costs: Because TikTok videos are sorted chronologically, the script automatically stops searching an account once it hits videos older than your start date, saving API tokens.
* **Dual Scraping Strategy:**
    1. **Official Accounts:** Uses the TikTok API to scrape all historical videos from a politician's profile.
    2. **Keyword Search:** Uses keyword tracking for politicians who do not have an official account.
* **Clean Excel Output:** Exports data directly to clean, formatted Excel (`.xlsx`) files. This prevents the formatting errors and broken emojis commonly seen in CSV files.
* **Advanced Transcription (Whisper STT):** Avoids unreliable cloud-based auto-captions. Instead, it uses a local Python workflow (`yt-dlp` + `OpenAI Whisper large-v3`) to handle background music and the bilingual nature (Dutch/French) of Belgian politics.

## 🛠️ Tech Stack
* **Language:** Python 3.8+
* **Data Processing:** `pandas`, `datetime`, `csv`, `os`, `glob`
* **API & Scraping:**
    * `requests` (for HTTP requests)
    * `python-dotenv` (for secure API key management)
    * [RapidAPI](https://rapidapi.com/) (using `tiktok-api6`)
    * [Apify](https://apify.com/) (using `API Dojo / Tiktok Profile Scraper` to get video URLs cheaply)
* **Audio Transcription (Optional):** `yt-dlp` (to extract audio), `openai-whisper` (for highly accurate, multi-language transcription)

## 📁 Workflow

### Phase 1: Data Preparation
The script reads your master Excel list of politicians. It uses Pandas `groupby` to split the list and create separate Excel files for each political party (e.g., `Vooruit_with_account.xlsx`).

### Phase 2: Batch Scraping
Using the `glob` module, the script automatically finds all the party Excel files in your folder and scrapes them one by one. 
The scraper will:
1. Check if the video's publish date falls within your target range (e.g., `2019-05-27` to `2024-06-09`).
2. Handle page navigation (pagination) automatically.
3. Manage API rate limits (HTTP 429 errors) with built-in pause and retry limits.
4. Save the `video_id`, `url`, `caption`, `views`, and `likes` into a clean output file like `[PartyName]_official.xlsx`.

### Phase 3: Deep Data Extraction (Recommended)
1. **Comments:** Feed the video URLs collected in Phase 2 into Apify's `TikTok Comments Scraper` to gather audience reactions.
2. **Transcription:** Pass the video URLs to `yt-dlp` to download the audio. Then, use OpenAI's `Whisper large-v3` to transcribe the speech. *Tip: Provide a custom `initial_prompt` with Belgian political terms to improve accuracy for local dialects and names.*

## 🚀 Quick Start

### 1. Install Dependencies
```bash
pip install pandas requests python-dotenv openpyxl