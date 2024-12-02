# Docker Compose Configuration for Media and Automation Services

This `docker-compose.yml` file sets up multiple services for managing and automating media, including tools for downloading, organizing, streaming, and monitoring. Below is a detailed guide on each service and how to configure them.

---

## **Prerequisites**
1. **Docker and Docker Compose**: Ensure you have Docker and Docker Compose installed on your system.
2. **Environment Variables**: Create a `.env` file in the same directory as this `docker-compose.yml` file and define the following variables:
   ```env
   PUID=1000             # Your user ID
   PGID=1000             # Your group ID
   TIMEZONE=Europe/London # Your time zone
   PLEX_CLAIM=claim-token # Replace with your Plex claim token (optional)
   ```

3. **Volumes**: Ensure the host paths (`/volume1/docker/...` and `/volume1/data/...`) exist and are accessible.

---

## **Services**

### **1. Watchtower**
Automatically updates your Docker containers.
- **Image**: `containrrr/watchtower`
- **Volumes**: 
  - `/var/run/docker.sock:/var/run/docker.sock`

---

### **2. Bazarr**
Subtitle management for your movies and TV shows.
- **Image**: `lscr.io/linuxserver/bazarr:latest`
- **Ports**: `6767:6767`
- **Volumes**:
  - `/volume1/docker/bazarr/config:/config`
  - `/volume1/data/media/movies:/movies`
  - `/volume1/data/media/tv:/tv`

---

### **3. Deluge**
A lightweight torrent client.
- **Image**: `lscr.io/linuxserver/deluge:latest`
- **Ports**:
  - `8112:8112` (Web UI)
  - `6881:6881` (Torrent traffic, TCP/UDP)
- **Volumes**:
  - `/volume1/docker/deluge/config:/config`
  - `/volume1/data/torrents:/downloads`

---

### **4. Prowlarr**
Indexer manager for your media automation.
- **Image**: `lscr.io/linuxserver/prowlarr:latest`
- **Ports**: `9696:9696`
- **Volumes**:
  - `/volume1/docker/prowlarr/config:/config`

---

### **5. Jackett**
Bridges various torrent indexers with apps like Radarr and Sonarr.
- **Image**: `lscr.io/linuxserver/jackett:latest`
- **Ports**: `9117:9117`
- **Volumes**:
  - `/volume1/docker/jackett/config:/config`
  - `/volume1/data/torrents/blackhole:/downloads`

---

### **6. Plex**
Media server for streaming your content.
- **Image**: `lscr.io/linuxserver/plex:latest`
- **Network Mode**: `host` (for better performance)
- **Devices**: 
  - `/dev/dri:/dev/dri` (for hardware acceleration)
- **Volumes**:
  - `/volume1/docker/plex/config:/config`
  - `/volume1/data/media/tv:/tv`
  - `/volume1/data/media/movies:/movies`

---

### **7. Tautulli**
Monitor Plex server usage.
- **Image**: `lscr.io/linuxserver/tautulli:latest`
- **Ports**: `8181:8181`
- **Volumes**:
  - `/volume1/docker/tautulli/config:/config`

---

### **8. Plex Meta Manager**
Enhance Plex with custom metadata.
- **Image**: `lscr.io/linuxserver/plex-meta-manager:latest`
- **Environment**:
  - `PMM_CONFIG=/config/config.yml`
  - `PMM_TIME=03:00` (optional: scheduled run time)
- **Volumes**:
  - `/volume1/docker/plex/config:/config`

---

### **9. Radarr**
Movie collection manager for automated downloading.
- **Image**: `lscr.io/linuxserver/radarr:latest`
- **Ports**: `7878:7878`
- **Volumes**:
  - `/volume1/docker/radarr/config:/config`
  - `/volume1/data:/data`

---

### **10. Sonarr**
TV series collection manager for automated downloading.
- **Image**: `lscr.io/linuxserver/sonarr:latest`
- **Ports**: `8989:8989`
- **Volumes**:
  - `/volume1/docker/sonarr/config:/config`
  - `/volume1/data:/data`

---

### **11. Home Assistant**
Home automation platform.
- **Image**: `lscr.io/linuxserver/homeassistant:latest`
- **Network Mode**: `host`
- **Volumes**:
  - `/volume1/docker/homeassistant/config:/config`

---

## **Usage**
1. Start the containers with:
   ```bash
   docker-compose up -d
   ```
2. Access the services through their respective ports (e.g., Plex at `http://<host-ip>:32400`).

## **Notes**
- Adjust file permissions to ensure the `PUID` and `PGID` match your system's user/group.
- Update services automatically with Watchtower or manually as needed.