Firefox Kiosk Docker Setup

Overview and Prerequisites
This document explains how to run the jlesage/firefox Docker image for a persistent Firefox instance and for kiosk-style deployments. It covers persistent storage, kiosk startup options, a UI hide tweak, secure remote access, essential container lifecycle commands, and security best practices.

Prerequisites
- Docker installed and running on the host.
- A host directory for persistent configuration, e.g., ~/firefox_data.
- Recommended shared memory for Firefox: --shm-size 2g.
- Administrative access for SSH and firewall configuration when required.

Quick Start Basic Container
Run a persistent Firefox container with the noVNC web UI exposed on the host:
docker run -d \
  --name=firefox-setup \
  -p 80:5800 \
  -v ~/firefox_data:/config:rw \
  --shm-size 2g \
  jlesage/firefox

Notes
- **Data persistence**: ~/firefox_data stores the Firefox profile, extensions, and settings.
- **Port mapping**: Host port 80 maps to container port 5800 (noVNC web UI).
- **Shared memory**: --shm-size 2g reduces crashes on heavy pages.

Kiosk Mode Examples
Start Firefox in kiosk mode and open a specific URL on launch.

Gmail kiosk
docker run -d \
  --name=firefox-kiosk \
  -p 80:5800 \
  -e FF_OPEN_URL="https://gmail.com" \
  -e FF_KIOSK=1 \
  -v ~/firefox_data:/config:rw \
  --shm-size 2g \
  jlesage/firefox

WhatsApp Web kiosk
docker run -d \
  --name=firefox-kiosk \
  -p 80:5800 \
  -e FF_OPEN_URL="https://web.whatsapp.com/" \
  -e FF_KIOSK=1 \
  -v ~/firefox_data:/config:rw \
  --shm-size 2g \
  jlesage/firefox

Tip
If host port 80 is in use, change the host side of the mapping (for example -p 8080:5800).

UI Customization and Remote Access
Hide noVNC control bar
To hide the noVNC control bar and panel in the container web UI, inject a small CSS snippet into the container index.html. Run this on the host against a running container:
docker exec firefox-kiosk sed -i 's/<\/head>/<style>#noVNC_control_bar, #noVNC_control_bar_handle, .noVNC_panel { display: none !important; }<\/style><\/head>/' /opt/noVNC/index.html

Access via SSH port forwarding
Use SSH local port forwarding to access a remote host’s noVNC UI without exposing the port publicly:
ssh -L 8080:localhost:80 root@192.168.1.14
Then open http://localhost:8080 on your local machine.

Essential Container Commands and Troubleshooting
- **Start container**
  docker start firefox-setup
- **Stop container**
  docker stop firefox-setup
- **Remove container**
  docker rm firefox-setup

Troubleshooting tips
- If pages fail to load or Firefox crashes, increase --shm-size or check container logs:
  docker logs firefox-kiosk
- If the web UI is unreachable, verify host firewall rules and that the container is running:
  docker ps
- If configuration changes are not persistent, confirm the host volume path and permissions.

Security and Responsible Use
- **Do not expose the noVNC port to the public Internet** without authentication and TLS.
- Prefer **SSH tunnels**, **VPNs**, or a reverse proxy with authentication (TLS + basic auth or OAuth) for remote access.
- Restrict access to trusted IP addresses using your host firewall or cloud security groups.
- Do not use this setup to capture keystrokes, intercept credentials, deny service to others, or perform any activity that violates privacy, policy, or law.
- If you need to restrict access for legitimate administrative reasons, implement allowlists and logging and consult your security team.

Contributing and License
- Contributions should improve security, reproducibility, and documentation.
- When submitting changes, include reproducible steps and avoid adding content that could enable misuse.
- Add a license file appropriate for your project (for example MIT or Apache 2.0).

