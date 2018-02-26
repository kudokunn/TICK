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
- Tạo file influxdata.repo: sudo vi /etc/yum.repos.d/influxdata.repo
- Thêm nội dung vào file:

           [influxdb]
           name = InfluxData Repository - RHEL $releasever
           baseurl = https://repos.influxdata.com/rhel/$releasever/$basearch/stable
           enabled = 1
           gpgcheck = 1
           gpgkey = https://repos.influxdata.com/influxdb.key
### 2.2 Cài đặt InfluxDB
- Thực hiện các câu lệnh để cài đặt influxdb:
sudo yum install influxdb
sudo systemctl start influxdb
sudo systemctl enable influxdb
 
### 2.3 Cài đặt và cấu hình Telegraf
- Cài đăt: sudo yum install telegraf
- Cấu hình: sudo vi /etc/telegraf/telegraf.conf
- Xác định vị trí của [outputs.influxdb] và cấu hình urls=IP's Influxdb. Ở đây influxdb và telegraf được cài trên cùng một server nên urls được cài đặt bằng localhost

                      [[outputs.influxdb]]
                            ## The full HTTP or UDP endpoint URL for your InfluxDB instance.
                            ## Multiple urls can be specified as part of the same cluster,
                            ## this means that only ONE of the urls will be written to each interval.
                            # urls = ["udp://localhost:8089"] # UDP endpoint example
                            urls = ["http://localhost:8086"] # required
                            ## The target database for metrics (telegraf will create it if not exists).
                            database = "telegraf" # required
                            
 - Khởi đông dịch vụ telegraf: sudo systemctl start telegraf && sudo systemctl enable telegraf
 ### 2.4: Cài đặt Kapacitor:
 - Cài đăt: sudo yum install kapacitor
 - Cấu hình: sudo vi /etc/kapacitor/kapacitor.conf
 - Xác định  vị trí [[influxdb]] và cấu hình urls = IP's Influxdb
 
                      # Multiple InfluxDB configurations can be defined.
                      # Exactly one must be marked as the default.
                      # Each one will be given a name and can be referenced in batch queries and InfluxDBOut nodes.
                      [[influxdb]]
                        # Connect to an InfluxDB cluster
                        # Kapacitor can subscribe, query and write to this cluster.
                        # Using InfluxDB is not required and can be disabled.
                        enabled = tru
                        default = true
                        name = "localhost"
                        urls = ["http://localhost:8086"]
                  
- Khởi động dịch vụ: sudo systemctl daemon-reload && sudo systemctl start kapacitor
### 2.5: Cài đặt và cấu hình Chronograf
- Tải và cài đặt:

           wget https://dl.influxdata.com/chronograf/releases/chronograf-1.2.0~beta3.x86_64.rpm
           sudo yum localinstall chronograf-1.2.0~beta3.x86_64.rpm
- Khởi động dịch vụ: sudo systemctl start chronograf
- Mở port 8888 của dịch vụ chronograf trên firewall: 
           
           sudo firewall-cmd --zone=public --permanent --add-port=8888/tcp
           sudo firewall-cmd --reload
           
