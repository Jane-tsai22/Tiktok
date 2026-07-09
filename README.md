# TikTok Data Scraper & Cleaner


## Key Features

* **Hashtag Search:** Scrape videos in bulk using a list of targeted hashtags.
* **Safe Saving:** Saves raw data line-by-line into a `.jsonl` file so you never lose progress if the connection drops.
* **Language Filter:** Uses `langdetect` to keep only videos in your target language (e.g., French `fr` or Dutch `nl`).
* **Secure API Keys:** Uses a `.env` file to keep your API key safe and hidden from GitHub.

---

## Requirements

Make sure you have these Python packages installed:

`pip install requests pandas langdetect python-dotenv`

---

## How to Use

### 1. Set Up Your API Key
Rename the `.env.example` file to `.env` and paste your RapidAPI key inside:
`RAPIDAPI_KEY=your_api_key_here`

### 2. Step 1: Scrape Data
Run the scraping section in the Jupyter Notebook. It will search TikTok using your hashtags and save all the raw data into a JSONL file (like `french_videos_raw.jsonl`).

### 3. Step 2: Filter & Clean
Run the cleaning section. The code will read the JSONL file, filter for the correct language, remove duplicates, and save a clean spreadsheet (like `final_pure_french_1000_for_apify.csv`).

---

## File Structure

* `*.ipynb`: The main Jupyter Notebook containing the code.
* `.env.example`: A template showing where to put the API key.
* `.gitignore`: Prevents sensitive files (like `.env`) and large files from being uploaded to GitHub.
* `*_raw.jsonl`: The raw data downloaded from TikTok (ignored by GitHub to save space).
* `*.csv`: The final, cleaned data ready for analysis.

---

## Notes
* **Rate Limits:** The script automatically pauses and retries if the TikTok API blocks you for making too many requests (`429 Too Many Requests`).
* **Language Detection:** Videos with only emojis, links, or numbers in the description might be skipped to keep the text data clean.