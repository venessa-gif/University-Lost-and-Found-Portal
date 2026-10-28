# 🎒 University Lost & Found Portal

A simple web app where students report **lost** and **found** items, search all reports, and let **Google Gemini (Flash)** suggest matches. Built with **Python, Streamlit, SQLite, Pandas and Pillow**.

## Features

| Page | What it does |
| --- | --- |
| 🏠 Home | Dashboard with totals, a chart of items per category, and the latest reports |
| 🔴 Report Lost Item | Form with optional photo. Photos can be auto-described by Gemini |
| 🟢 Report Found Item | Same form for items someone has found |
| 🔎 Browse & Search | Filter by type, status and category, plus keyword search |
| 🤖 AI Match Finder | Gemini compares a lost report with found reports (and the other way round) and gives a confidence score with a reason |
| 🛠️ Admin | Password-protected. Mark items as returned, re-open or delete them, and download everything as CSV |

## Project structure

```
.
├── app.py             # the whole application (heavily commented)
├── requirements.txt   # Python packages
└── README.md
```

`app.py` is organised in six sections: settings, database functions, image helper, AI functions, pages, and the main menu.

## How it works (short explanation)

- **Streamlit** draws the pages. The sidebar menu decides which page function runs.
- **SQLite** stores reports in one file, `lost_found.db`. All SQL uses `?` placeholders, which protects against SQL injection.
- **Pillow** shrinks uploaded photos to at most 800 px and saves them as JPEG inside the database.
- **Pandas** counts items for the dashboard and creates the CSV export.
- **Gemini** is used in two places: describing a photo, and finding matches between reports. Contact details are never sent to the AI, only the item descriptions.

## Run on your computer

1. Install Python 3.10 or newer.
2. Open a terminal in the project folder and run:

   ```bash
   python -m venv venv
   source venv/bin/activate        # Windows: venv\Scripts\activate
   pip install -r requirements.txt
   ```

3. Get a free Gemini API key at <https://aistudio.google.com/apikey>.
4. Create a file `.streamlit/secrets.toml` (create the `.streamlit` folder first):

   ```toml
   GEMINI_API_KEY = "your-gemini-key"
   ADMIN_PASSWORD = "choose-a-strong-password"
   ```

5. Start the app:

   ```bash
   streamlit run app.py
   ```

## Settings

| Secret | Required | Meaning |
| --- | --- | --- |
| `GEMINI_API_KEY` | For AI features | Your Google AI Studio key |
| `ADMIN_PASSWORD` | For the Admin page | If it is missing, the Admin page stays disabled |
| `GEMINI_MODEL` | No | Defaults to `gemini-3.5-flash`. Set it if you want another Flash model |

Also edit `UNIVERSITY_NAME` near the top of `app.py`.

## Deploy on Streamlit Community Cloud

1. Push the project to a GitHub repository (see the steps below).
2. Go to <https://share.streamlit.io> and sign in with GitHub.
3. Click **Create app**, select your repository, branch `main`, main file `app.py`.
4. Open **Advanced settings → Secrets** and paste:

   ```toml
   GEMINI_API_KEY = "your-gemini-key"
   ADMIN_PASSWORD = "choose-a-strong-password"
   ```

5. Click **Deploy**.

### Push to GitHub using the website

1. On github.com click **+ → New repository**, name it, click **Create repository**.
2. Click **uploading an existing file**, drag in `app.py`, `requirements.txt` and `README.md`, then click **Commit changes**.
3. Click **Add file → Create new file**, name it `.gitignore`, paste the lines below, and commit:

   ```
   lost_found.db
   .streamlit/secrets.toml
   venv/
   __pycache__/
   ```

Never upload your API key or `secrets.toml`.

## ⚠️ Important limitation: data is not permanent on Streamlit Cloud

Streamlit Community Cloud does not keep files permanently. When the app restarts or is redeployed, `lost_found.db` is reset and **all reports are lost**. This is fine for a demo or a semester project. For real use, move the data to an external database (for example Supabase or Turso).

## Privacy and safety notes

- Names and contact details entered in reports are visible to everyone using the app. Use university emails instead of personal numbers where possible.
- Photos and item descriptions are sent to Google's Gemini API when you use the AI features.
- AI matches are only suggestions. Always verify ownership (for example by asking for a detail not visible in the report) before handing an item over.

## Troubleshooting

| Problem | Fix |
| --- | --- |
| "The Gemini API key is not set" | Add `GEMINI_API_KEY` to your secrets and restart the app |
| "Model name was not found" | Set `GEMINI_MODEL` to a current Gemini Flash model |
| Admin page says it is disabled | Add `ADMIN_PASSWORD` to your secrets |
| "Rate limit reached" | Wait a minute and try again |
