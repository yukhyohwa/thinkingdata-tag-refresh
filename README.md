# thinkingdata-tag-refresh

Repository: [yukhyohwa/thinkingdata-tag-refresh](https://github.com/yukhyohwa/thinkingdata-tag-refresh).

Automatically navigates to the ThinkingData tag management page, logs in if needed (using a persistent browser session), and clicks the **刷新 (Refresh)** button.

Designed to be run as a **Windows Scheduled Task**.

---

## Quick Start

### 1. First-time Setup

Double-click `setup.bat` — it will:
- Create a Python virtual environment (`venv/`)
- Install `playwright` and `python-dotenv`
- Download the Chromium browser

### 2. Configure Credentials

Edit `.env` and fill in your credentials:

```env
TA_URL=http://8.211.141.76:8993/
TA_USER=your_username
TA_PASS=your_password
TAG_URL=http://8.211.141.76:8993/#/tag/tag/1?currentProjectId=16
SESSION_DIR=C:/path/to/thinkingdata-tag-refresh/ta_session
```

### 3. First Login (save session)

Run the following once so the script can save your browser session:

```bat
venv\Scripts\python.exe main.py --show
```

The browser will open visibly, log you in, and save the session. After this, subsequent runs will skip the login automatically.

### 4. Test Headless Mode

```bat
venv\Scripts\python.exe main.py
```

### 5. Schedule with Windows Task Scheduler

In **Task Scheduler**, create a new task:

| Field | Value |
|---|---|
| **Program/script** | `C:\path\to\thinkingdata-tag-refresh\run_refresh.bat` |
| **Start in** | `C:\path\to\thinkingdata-tag-refresh\` |
| **Trigger** | Set your preferred schedule (e.g. daily, hourly) |
| **Run whether user is logged on or not** | ✅ Highly Recommended (Runs in background) |

After moving or renaming an existing checkout, update both the executable path and working directory of its existing scheduled task. Windows may require an administrator account to edit the task. A directory junction from the previous location can keep that task working until its paths are updated.

> [!IMPORTANT]
> **Prevention of Hanging Tasks:**
> To ensure the task doesn't get stuck if an instance fails to close:
> 1. Go to the **Settings** tab.
> 2. Set "If the task is already running..." to **"Stop the existing instance"**.
> 3. Check "Stop the task if it runs longer than" and set it to **1 hour**.

---

## CLI Options

```
python main.py             # Headless (default)
python main.py --show      # Open visible browser (for debugging)
python main.py --login     # Force re-login (clears old session)
python main.py --login --show  # Force re-login with visible browser
```

---

## Troubleshooting

- **Task Status is "Running" but no progress** → Check if `run_refresh.bat` contains a `pause` command (it should have been removed for automated tasks).
- **Refresh button not found** → Check `logs/` or run with `--show` to see what the page looks like.
- **Login fails** → Run `python main.py --login --show` to observe the login process visually.
- **Session expired** → Run `python main.py --login` to clear the old session and re-authenticate.

## Local scheduled-task compatibility

The existing Windows task still points to `thinkingdata-tag-refresher/run_refresh.bat`; changing that task was denied by Windows permissions. A local directory junction named `thinkingdata-tag-refresher` points to this checkout at `thinkingdata-tag-refresh`, keeping that task and older virtual-environment launchers usable. New tasks and documentation should use `thinkingdata-tag-refresh`.
