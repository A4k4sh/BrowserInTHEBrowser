Setup Browser:
docker run -d \
    --name=firefox-setup \
    -p 80:5800 \
    -v ~/firefox_data:/config:rw \
    --shm-size 2g \
    jlesage/firefox
--------------------------------
Essential commands
docker start firefox-setup
docker stop firefox-setup
docker rm firefox-setup
----------------------------------------
Configure to Sites:
docker run -d \
    --name=firefox-kiosk \
    -p 80:5800 \
    -e FF_OPEN_URL="https://gmail.com"
    -e FF_KIOSK=1
    -v ~/firefox_data:/config:rw \
    --shm-size 2g \
    jlesage/firefox

docker run -d \
    --name=firefox-kiosk \
    -p 80:5800 \
    -e FF_OPEN_URL="https://web.whatsapp.com/"
    -e FF_KIOSK=1
    -v ~/firefox_data:/config:rw \
    --shm-size 2g \
    jlesage/firefox

Command for Hide Panel 
docker exec firefox-kiosk sed -i 's/<\/head>/<style>#noVNC_control_bar, #noVNC_control_bar_handle, .noVNC_panel { display: none !important; }<\/style><\/head>/' /opt/noVNC/index.html

iptables -I DOCKER-USER ! -i lo -p tcp --dport 5800 -j DROP

ssh -L 8080:localhost:80 root@192.168.1.14

shortcut key = alt + shift + O or K = for logs
--------------------------------------------------------------------------------------






