# monitor-docker
## telegram-bot
- Thêm token của bot telegram vào file config.yaml trong `/telegram-bot/config.yaml`
```
telegram_token: "<bot telegram token>"

# ONLY IF YOU USING TEMPLATE required for test
template_path: "template.tmpl"
time_zone: "Asia/Ho_Chi_Minh"
split_token: "|"

# ONLY IF YOU USING DATA FORMATTING FUNCTION, NOTE for developer: important or test fail
time_outdata: "02/01/2006 15:04:05"
split_msg_byte: 4000
```
## grafana
- Khai báo dữ liệu cho các biến trong file `.env`
```
ADMIN_USER=admin
ADMIN_PASSWORD=admin
SERVER_DOMAIN=<server ip address>
SMTP_ENABLED=true
SMTP_HOST=smtp.gmail.com:587
SMTP_USER=<gmail address>
SMTP_PASSWORD=<Access token Gmail>
SMTP_SKIP_VERIFY=false
SMTP_FROM_ADDRESS=<gmail address>
SMTP_FROM_NAME=Grafana
```
- Kiểm tra ID User 
```
id -u
```
- Thay ID User vào `User` trong file `docker-compose.yml`
## prometheus
- Chỉnh sửa rules cho alert trong `rules.yml`
- Chỉnh sửa cấu hình cho jobs trong `prometheus.yml`
## alertmanager
- Chỉnh sửa cấu hình cho `alertmanager.yml`:
```
global:
  smtp_smarthost: 'smtp.gmail.com:587'
  smtp_from: '<gmail address>'
  smtp_auth_username: '<gmail address>'
  smtp_auth_password: '<OAToken>'
```
- Thêm ID Group telegram để gửi cảnh báo về Group
```
webhook_configs:
      - url: 'http://171.244.139.240:9087/alert/<group telegram id>'
        send_resolved: true
```
## node-exporter
- Chạy node-exporter:
```
docker-compose -f node-exporter.yml up -d
```