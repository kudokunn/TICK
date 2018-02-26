## 1: Giới thiệu:
TICK là một bộ các công cụ để thực hiện monitor hệ thống, thiết bị mạng, service...

TICK bao gồm 4 thành phần: Telegraf, InfluxDB, Chronograf, Kapacitor. Chức năng của từng thành phần:
- Telegraf: được cài đặt trên agent để thu thập dữ liệu từ agent đó
- InfluxDB: là một database để lưu trữ dữ liêu từ Telegraf gửi về
- Chronograf: được dùng để hiển thị dữ liệu từ InfluxDB
- Kapacitor: thực hiện theo dõi dữ liệu trên InfluxDB và hỗ trợ tạo cảnh báo
## 2: Cài đặt
- Server: Centos 7
- IP Server: 192.168.1.100
- Mỗi thành phần trong TICK có thể được cài đặt riêng trên từng server hoặc cài đặt chung trên một server. Trong bài hướng dẫn sẽ hướng dẫn cài đặt TICK trên một server.

### 2.1 Thêm TICK Stack Repository
- Tạo file influxdata.repo
           sudo vi /etc/yum.repos.d/influxdata.repo
- Thêm nội dung vào file:

           [influxdb]
           name = InfluxData Repository - RHEL $releasever
           baseurl = https://repos.influxdata.com/rhel/$releasever/$basearch/stable
           enabled = 1
           gpgcheck = 1
           gpgkey = https://repos.influxdata.com/influxdb.key
