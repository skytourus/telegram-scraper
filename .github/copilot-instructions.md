# Copilot Instructions for telegram-scraper

## Project Overview
- **Purpose:** Scrape messages and media from Telegram channels using Telethon, with support for QR code and phone authentication, media downloads, and data export (CSV/JSON).
- **Main entrypoint:** `telegram-scraper.py` (single-file, menu-driven CLI app)
- **Key class:** `OptimizedTelegramScraper` encapsulates all logic (auth, scraping, media, export, state, menu).

## Architecture & Data Flow
- **State:** User config and progress are stored in `state.json` (API keys, channels, scrape options, last scraped message IDs).
- **Per-channel storage:**
  - SQLite DB: `./<channel_id>/<channel_id>.db` (messages, media info)
  - Media: `./<channel_id>/media/` (unique filenames: `{message_id}-<basename>.<ext>`)
  - Exports: `./<channel_id>/<channel_id>.csv` and `.json`
- **Batching:** Messages and media are processed in batches for performance.
- **Concurrency:** Media downloads use up to 5 concurrent tasks (configurable).

## Developer Workflows
- **Install dependencies:** `pip install -r requirements.txt`
- **Run app:** `python telegram-scraper.py`
- **First run:** Prompts for API credentials and authentication method (QR code recommended).
- **Menu navigation:** All actions (scrape, export, add/remove channels, etc.) are via interactive menu.
- **Continuous scraping:** Option `[C]` in menu; runs in a loop, checking for new messages every 60s.
- **Export:** `[E]` in menu exports all scraped data for each channel.

## Project-Specific Patterns & Conventions
- **Channel selection:** Users can select channels by number or full ID; supports multi-select and 'all'.
- **Media handling:**
  - Only non-WebPage media is downloaded.
  - Unique filenames prevent overwrites.
  - Retry logic for failed downloads (with exponential backoff).
- **Database:**
  - WAL mode and indexes for performance.
  - Schema: see `get_db_connection()` in `telegram-scraper.py`.
- **Error handling:**
  - Graceful recovery from network/rate-limit errors.
  - Progress and state are saved frequently.
- **No external config files** beyond `state.json` and per-channel folders.

## Integration Points
- **Telethon**: Telegram API client (see `requirements.txt`).
- **qrcode**: For QR code login (ASCII art in terminal).

## Examples
- **Add channels:** `[L]` in menu, then select by number or ID.
- **Scrape:** `[S]` in menu, select channels, scraping starts with progress bars.
- **Export:** `[E]` in menu, exports to CSV/JSON per channel.

## Key Files
- `telegram-scraper.py`: All logic (auth, scraping, export, menu, state, DB, media)
- `requirements.txt`: Python dependencies
- `README.md`: User-facing documentation, usage, and features

---

**For AI agents:**
- Use the menu-driven CLI for all workflows; do not bypass the menu logic.
- When modifying scraping, media, or export logic, update both the DB and file output conventions as described above.
- Preserve state and error handling patterns.
- Reference the README for user-facing explanations and workflow details.
