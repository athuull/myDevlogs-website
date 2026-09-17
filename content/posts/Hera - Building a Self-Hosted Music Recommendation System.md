---
title: "Hera - Building a Self-Hosted Music Recommendation System"
date: 2026-09-17
draft: false
tags: ["Java", "SpringBoot", "Docker", "Self-Hosting", "DevOps", "Music"]
---

For a while, I’ve been meaning to step away from proprietary music streaming services like Spotify and Apple Music. Having already used Jellyfin to stream movies and TV shows across my home network, self-hosting my music seemed like the logical next step.

I had a spare ThinkPad laptop lying around that I wasn't using much, so I hosted **Navidrome** on it using CasaOS and paired it with the **Symfonium** app on my phone to stream music anywhere. For grabbing tracks, I found an awesome app called **Downtify**, which leverages `yt-dlp` to download songs along with their artwork.

While this setup handled playback and storage effortlessly, one major problem remained: **the recommendation system**. The main thing that kept me returning to commercial streaming services was algorithmic discovery like Spotify's *Discover Weekly*. Without it, discovering new music and keeping a self-hosted library fresh meant manually searching for, vetting, and downloading tracks one by one.

To bridge that gap, I built **Hera**—a self-hosted music recommendation system and automated downloading service.

---

## What is Hera?

Hera connects your listening habits to your local media server. It integrates with the [Last.fm API](https://www.last.fm/api/authentication) to analyze your music tastes, derive tailored recommendations, and automatically download them into your local library through Downtify on a configurable schedule.

With Hera running in the background, it can wake up every night, discover new tracks matching what you've been listening to, and pull them into your library without requiring any manual effort.

### Key Features

- **Last.fm-Powered Recommendations:** Discovers tracks based on your recent scrobbles, top artists, and musical tags/genres.
- **Automated Scheduling:** Configurable cron job to automatically run discovery and download batches (e.g., pulling 10 new tracks every night).
- **Library Guard (Deduplication):** Actively inspects your mounted media storage and download history so you never re-download tracks you already own.
- **Audit History:** Maintains a clear log of downloaded, skipped, and failed tracks.
- **Manual Grabber & Search:** Allows searching for specific songs or artists, or pasting Spotify/YouTube Music links to download on demand.
- **Live Web Dashboard:** A responsive UI with WebSocket support for streaming live download progress and backend worker activity.

---

## Dashboard in Action

Here is a look at the web dashboard running in production on my server:

### 1. Discovery & Recommendation Hub
The dashboard allows you to select your discovery strategy (`Hybrid`, recent scrobbles, or top artists), set candidate limits, trigger manual searches, or paste Spotify / web links for one-click downloading:

![Hera Dashboard - Music Discovery & Recommendations](https://raw.githubusercontent.com/athuull/hera/main/Apps/Hera/screenshot-1.png)

### 2. Live WebSocket Download Tracking
When downloads are queued—either manually or via the nightly cron scheduler—real-time progress bars track the active audio stream download, batch progress, and worker activity:

![Hera Download Progress & WebSocket Feed](https://raw.githubusercontent.com/athuull/hera/main/Apps/Hera/screenshot-2.png)

### 3. Audit History & Deduplication Logs
Hera keeps a full audit log of every track processed. Notice the status badges showing `[success]`, `[failed]`, and `[skipped]`—demonstrating the library guard preventing duplicates from being downloaded:

![Hera Audit History & Status Logs](https://raw.githubusercontent.com/athuull/hera/main/Apps/Hera/screenshot-3.png)

### 4. Local Music Library Overview
You can also inspect the indexed local tracks directly from the web UI, complete with artist metadata and cover artwork:

![Hera Local Library Browser](https://raw.githubusercontent.com/athuull/hera/main/Apps/Hera/screenshot-4.png)

---

## Deployment with Docker Compose

Hera is fully containerized and designed to sit right next to Downtify and Navidrome in your home server stack.

Here is a sample `docker-compose.yml` to get started:

```yaml
services:
  hera:
    image: athuull/hera:latest
    container_name: hera
    restart: unless-stopped
    ports:
      - "8080:8080"
    environment:
      - SPRING_PROFILES_ACTIVE=prod
      - LASTFM_API_KEY=your_lastfm_api_key
      - LASTFM_USERNAME=your_lastfm_username
      - CRON_SCHEDULE=0 0 2 * * *  # Runs nightly at 2:00 AM
      - DOWNTIFY_API_URL=http://downtify:8000
    volumes:
      - /path/to/music:/music:rw
      - ./config:/app/config
    depends_on:
      - downtify

  downtify:
    image: downtify/downtify:latest
    container_name: downtify
    restart: unless-stopped
    volumes:
      - /path/to/music:/music:rw
```

Once running, navigate to `http://localhost:8080` to configure your Last.fm credentials and recommendation thresholds.

---

## Conclusion

Combining Navidrome, Symfonium, and Downtify gave me full ownership of my music library, and Hera solved the missing piece: intelligent, hands-free music discovery.

I've been running and testing this setup on CasaOS, and Hera is also listed on the Unraid Community Apps. I'm actively working on new features and improvements, and would love to hear feedback or suggestions from anyone running their own music server!

Source code for the app: https://github.com/athuull/hera
