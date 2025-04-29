🔌 Hệ Thống Đô Thị Thông Minh Sử Dụng LoRa Mesh
Mô tả ngắn gọn: Hệ thống giám sát và thu thập dữ liệu môi trường trong khu đô thị thông minh, sử dụng mạng cảm biến không dây LoRa Mesh để truyền dữ liệu từ các node cảm biến đến Gateway. Gateway sẽ gửi dữ liệu lên backend thông qua MQTT, backend lưu vào cơ sở dữ liệu MySQL và cung cấp API phục vụ giao diện dashboard.

📑 Mục Lục
Giới thiệu
Thông số kỹ thuật
Danh sách linh kiện
Sơ đồ nguyên lý và PCB
Hướng dẫn lắp ráp
Lập trình firmware
Cách sử dụng
Kiểm thử
Ảnh/Video demo
Đóng góp
Giấy phép
👋 Giới Thiệu
Dự án hệ thống đô thị thông minh với khả năng thu thập dữ liệu từ các cảm biến môi trường như nhiệt độ, độ ẩm, ánh sáng, âm thanh qua mạng LoRa Mesh. Các node cảm biến sử dụng ESP32 để thu thập và truyền dữ liệu tới Gateway, nơi dữ liệu được gửi tiếp đến MQTT Broker và backend để lưu trữ trong cơ sở dữ liệu MySQL.

📐 Thông Số Kỹ Thuật
Thành phần	Thông tin
Node cảm biến	ESP32 + Cảm biến DHT11, BH1750, âm thanh
Gateway	ESP32 có LoRa, kết nối WiFi
Backend	Server Flask, kết nối MQTT Broker
CSDL	MySQL
🧰 Danh Sách Linh Kiện
Tên linh kiện	Số lượng	Ghi chú
ESP32 DevKit v1	1	Vi điều khiển chính
Cảm biến DHT11	1	Cảm biến nhiệt độ, độ ẩm
Cảm biến BH1750	1	Cảm biến ánh sáng
Cảm biến âm thanh	1	Cảm biến âm thanh
Gateway ESP32	1	Thiết bị thu thập dữ liệu
🔧 Sơ Đồ Nguyên Lý và PCB
📎 Schematic (PDF)
📎 PCB Layout (Gerber)
📎 File thiết kế (Eagle / KiCad)
Hình minh họa sơ đồ nguyên lý hoặc board PCB có thể nhúng ngay tại đây:

Schematic

🔩 Hướng Dẫn Lắp Ráp
Hàn các linh kiện nhỏ trước: điện trở, tụ điện
Hàn vi điều khiển hoặc socket
Kiểm tra ngắn mạch bằng đồng hồ
Cấp nguồn thử, kiểm tra dòng tiêu thụ
Lập trình firmware để kiểm tra
💻 Lập Trình Firmware
Ngôn ngữ: C++ (Arduino) / MicroPython / PlatformIO
Tải firmware: firmware/main.ino hoặc src/main.py
Cách nạp:
platformio run --target upload
📖 Cách Sử Dụng
Cài đặt MQTT Broker (Mosquitto)
sudo apt update
sudo apt install mosquitto mosquitto-clients
Chạy Flask Backend
pip install flask paho-mqtt mysql-connector-python
Cài Database MySQL
Cài đặt MySQL:
sudo apt update
sudo apt install mysql-server
Cấu hình MySQL:
sudo mysql_secure_installation
Đăng nhập vào MySQL:
mysql -u root -p
Tạo cơ sở dữ liệu và bảng sensor_data:
CREATE DATABASE iot;
USE iot;

CREATE TABLE sensor_data (
  id INT AUTO_INCREMENT PRIMARY KEY,
  node_id VARCHAR(50),
  temperature FLOAT,
  humidity FLOAT,
  light INT,
  sound INT,
  timestamp DATETIME
);
Ví dụ code nhận dữ liệu MQTT bằng Python
import paho.mqtt.client as mqtt
import mysql.connector
import json

def on_message(client, userdata, msg):
    data = json.loads(msg.payload)
    print(f"Received data: {data}")
    # Lưu vào MySQL
    conn = mysql.connector.connect(host="localhost", user="root", password="password", database="iot")
    cursor = conn.cursor()
    sql = "INSERT INTO sensor_data (node_id, temperature, humidity, light, sound, timestamp) VALUES (%s, %s, %s, %s, %s, NOW())"
    val = (data['node_id'], data['temperature'], data['humidity'], data['light'], data['sound'])
    cursor.execute(sql, val)
    conn.commit()
    cursor.close()
    conn.close()

client = mqtt.Client()
client.connect("localhost", 1883)
client.subscribe("city/data/#")
client.on_message = on_message
client.loop_forever()
Topic dữ liệu: city/data/<node_id>
Dữ liệu mẫu
{
  "node_id": "node1",
  "temperature": 30.5,
  "humidity": 65,
  "light": 200,
  "sound": 35
}
🛠️ Kiểm Thử
Test MQTT Gateway
mosquitto_pub -h localhost -t city/data/test -m '{"node_id": "test", "temperature": 25}'
Giám sát log nhận/gửi dữ liệu tại Gateway và Backend.
Kiểm tra dữ liệu lưu trữ trong MySQL:
SELECT * FROM sensor_data;
📸 Ảnh/Video Demo
Sẽ cập nhật sau khi hoàn thành hệ thống thực tế.

🤝 Đóng Góp
Fork repo và gửi pull request.
Góp ý cải tiến thêm chức năng mới.
📜 Giấy Phép
Thoải mái sử dụng cho mục đích cá nhân, giáo dục hoặc thương mại.
Yêu cầu giữ nguyên tên tác giả gốc khi phát hành lại.

👨‍💻 Tác Giả
Hoàng Quốc Toàn - B21DCDT221 - Nhóm trưởng
Đào Bá Thọ - B21DCDT217
Tạ Quang Trường - B21DCDT026
Vương Tuấn Minh - B21DCDT153
