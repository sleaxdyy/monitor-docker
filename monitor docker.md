## docker-compose.yml
```
version: '3.8'

networks:
  monitoring:
    driver: bridge
    
volumes:
  prometheus_data: {}

services:
  node-exporter:
    image: prom/node-exporter:latest
    container_name: node-exporter
    restart: unless-stopped
    volumes:
      - /proc:/host/proc:ro
      - /sys:/host/sys:ro
      - /:/rootfs:ro
    command:
      - '--path.procfs=/host/proc'
      - '--path.rootfs=/rootfs'
      - '--path.sysfs=/host/sys'
      - '--collector.filesystem.mount-points-exclude=^/(sys|proc|dev|host|etc)($$|/)'
    expose:
      - 9100
    networks:
      - monitoring

  prometheus:
    image: prom/prometheus:latest
    container_name: prometheus
    restart: unless-stopped
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
      - prometheus_data:/prometheus
    command:
      - '--config.file=/etc/prometheus/prometheus.yml'
      - '--storage.tsdb.path=/prometheus'
      - '--web.console.libraries=/etc/prometheus/console_libraries'
      - '--web.console.templates=/etc/prometheus/consoles'
      - '--web.enable-lifecycle'
    expose:
      - 9090
    networks:
      - monitoring
```
## grafana docker compose
```
version: '3.8'
services:
  prometheus-bot:
    image: quangtran1701/prometheus-bot:latest
#    network_mode: "host"
    volumes:
      - ./telegrambot/:/etc/telegrambot/
      - ./telegrambot/config.yaml:/config.yaml
      - ./telegrambot/template.tmpl:/template.tmpl
    container_name: prometheus-bot
    restart: unless-stopped
    ports:
      - "9087:9087"
    networks:
      - monitor-net

#  cadvisor:
#    image: gcr.io/google-containers/cadvisor:latest
#    network_mode: "host"
#    container_name: cadvisor
#    privileged: true
#    volumes:
#      - /:/rootfs:ro
#      - /var/run:/var/run:rw
#      - /sys:/sys:ro
#      - /var/lib/docker/:/var/lib/docker:ro
#    restart: unless-stopped

  grafana:
    image: grafana/grafana:8.2.5
    container_name: grafana
#    network_mode: "host"
    volumes:
      - ./grafana_data:/var/lib/grafana
      - ./grafana/provisioning:/etc/grafana/provisioning
      - ./grafana/provisioning/dashboards:/etc/grafana/provisioning/dashboards
      - ./grafana/provisioning/datasources:/etc/grafana/provisioning/datasources
      - /var/log/grafana:/var/log/grafana
    environment:
#      - GF_SECURITY_ADMIN_USER=${ADMIN_USER:-admin}
#      - GF_SECURITY_ADMIN_PASSWORD=${ADMIN_PASSWORD:-admin}
      - GF_SECURITY_ADMIN_USER=${ADMIN_USER}
      - GF_SECURITY_ADMIN_PASSWORD=${ADMIN_PASSWORD}
      - GF_SERVER_DOMAIN=${SERVER_DOMAIN}
      - GF_USERS_ALLOW_SIGN_UP=false
      - GF_SMTP_ENABLED=${SMTP_ENABLED}
      - GF_SMTP_HOST=${SMTP_HOST}
      - GF_SMTP_USER=${SMTP_USER}
      - GF_SMTP_PASSWORD=${SMTP_PASSWORD}
      - GF_SMTP_SKIP_VERIFY=${SMTP_SKIP_VERIFY}
      - GF_SMTP_FROM_ADDRESS=${SMTP_FROM_ADDRESS}
      - GF_SMTP_FROM_NAME=${SMTP_FROM_NAME}
    user: "991:987"
    restart: unless-stopped
    ports:
      - "3000:3000"
    networks:
      - monitor-net

  ping_exporter:
    image: quangtran1701/ping_exporter:latest
    container_name: ping_exporter
#    environment:
#      - CMD_FLAGS="--metrics.rttunit=both"
    volumes:
      - ./ping:/config:ro
    restart: unless-stopped
    ports:
      - "9427:9427"
    networks:
      - monitor-net

#  pushgateway:
#    image: prom/pushgateway:latest
#    network_mode: "host"
#    container_name: pushgateway
#    restart: unless-stopped

networks:
  monitor-net:
    driver: bridge
```
