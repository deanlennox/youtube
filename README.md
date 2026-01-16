# yt-dlp Docker + Jellyfin Setup

This repository contains the configuration for automatically downloading YouTube content using yt-dlp in Docker, ready for playback via Jellyfin.

Manual Run:

Update yt-dlp:
docker compose pull

Download new content (removes container after running):
docker compose run --rm yt-dlp

Optional: Grab content from last N days
--playlist-start/end
# can be replaced with
--dateafter now-8days   # downloads content from the last 8 days

Automation (Nightly):

Edit crontab:
crontab -e

Pull latest Docker image at 01:00:
0 1 * * * cd /home/dean/node/youtube && docker compose pull >> yt-dlp-pull.log 2>&1

Download newest content nightly at 02:00 (max runtime 5h):
0 2 * * * cd /home/dean/node/youtube && timeout 5h docker compose run --rm yt-dlp >> yt-dlp.log 2>&1

Notes:
- Runs once per night inside the 01:00–07:00 window.
- Maximum runtime is 5 hours; any partial downloads (.part files) are resumed automatically the following night.
- Archive file (archive.txt) ensures no duplicate downloads.

Git Workflow:

Stage and commit changes:
git add .
git commit -m "Describe changes here"
git push origin main

Notes:
- Exclude media, archive, and log files from Git using a .gitignore:
media/
archive.txt
*.log
- Only track configuration and scripts in Git.

Folder Structure (Example):
/home/dean/node/youtube
 ├── docker-compose.yml
 ├── channels.txt
 ├── .gitignore
 ├── README.md
 ├── archive.txt      # optional, not tracked in Git
 └── media/           # downloaded videos, not tracked in Git

Tips:
- Use consistent folder paths in Docker Compose and crontab (/home/dean/node/youtube).
- Keep archive.txt intact to prevent re-downloading old videos.
- Partial .part files are safe; yt-dlp resumes downloads automatically.
