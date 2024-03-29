<div style="text-align:center;">
  <h1>TUGAS BESAR IOT "PARKING SLOT RECOMMENDATION"</h1>
</div>

<div style="text-align:center;">
  <img src="https://hackmd.io/_uploads/Hk-k3NYwa.png" alt="Logo_Tel-U" width="300">
</div>
<div style="text-align:center;">
    <h1>
    Kelompok Starboy:
    </h1>
</div>
<div style="text-align:center;">
  <p> 
    Alif Firmansyah-1103204105
  </p>
    <p>
    Dimas Putra Mahendra-1103200076
    </p>
    <p>
     Ikhsar Sulaeman-1103200089
    </p>
    <p>
    Muhammad Fariq Taqi Pasai-1103204193
    </p>
    <p>
   </p>
</div>

# Pengenalan Proyek

<div style="text-align:justify;">
<p>
    Pada proyek tugas besar ini kami membuat sebuah sistem parking slot recommendation menggunakan ESP332 dan sensor infrared untuk mendeteksi apakah ada mobil yang sedang terparkir atau tidak. Untuk jaringan protocol menggunakan protocol MQTT
</p>
    

</div>

**Waktu Mengerjakan Proyek**
<div style="text-align:justify;">
Pengerjaan proyek kami ini dilakukan selama 2 minggu dikarenakan ada beberapa hal yang membuat proyek ini memakan waktu yang cukup lama seperti membeli alat yang akan digunakan, memasang alat, mencari codingan, membuat server broker mqtt, dan implementasi rangkaian yang dibuat.
   
</div>

# Latar Belakang

* Alasan memilih topik
<div style="text-align:justify;">
    Kami memilih topik ini karena kami sendiri merasakan seberapa sulitnya untuk mencari parkiran dalam sebuah gedung atau pusat perbelanjaan, untuk mencari tempat parkir yang kosong itu membutuhkan waktu yang cukup lama dan konsumsi bbm yang berlebih. Maka dari itu kami membuat proyek ini dengan tujuan agar mencari parkir yang kosong dengan mudah tanpa membuang waktu yang cukup lama.
    
</div> 

# Teori Dasar
**1. Alat yang dibutuhkan**
* ESP32
![esp32](https://hackmd.io/_uploads/r1NTAksv6.jpg)
<div style="text-align:justify;">
Penggunaan ESP32 pada proyek ini memungkinkan pembuatan sistem parkir yang lebih efisien dengan memberikan rekomendasi tempat parkir kepada pengguna berdasarkan informasi real-time. Ini dapat membantu mengoptimalkan penggunaan ruang parkir dan meminimalkan waktu yang dihabiskan untuk mencari tempat parkir.
</div>


* Infrared Sensor
![Infrared](https://hackmd.io/_uploads/By-4lgjva.jpg)

<div style="text-align:justify;">
    sensor infrared pada proyek ini pengelola parkir atau pengguna dapat dengan cepat mengetahui slot mana yang kosong dan menghindari pemborosan waktu yang dapat terjadi akibat mencari tempat parkir yang tersedia secara manual.
</div>
    

* Kabel Jumper
![kabel jumper](https://hackmd.io/_uploads/Bk1bGesvp.jpg)

<div style="text-align:justify;">
Kabel jumper pada proyek Parking Slot Recommendation berfungsi sebagai penghubung antara komponen-komponen elektronik yang terlibat dalam sistem. Kabel jumper digunakan untuk mengamankan aliran listrik dan data antara sensor-sensor, kontroler, dan perangkat elektronik lainnya yang terlibat dalam merekomendasikan slot parkir.
</div>
    


**2. Koneksi yang digunakan**
* Wifi
    <div style="text-align:justify;">
     kegunaan WiFi sangat penting untuk mendukung fungsionalitas rekomendasi slot parkir. WiFi memungkinkan perangkat-perangkat yang terintegrasi, seperti sensor parkir atau kamera, untuk terhubung ke jaringan dan berkomunikasi secara nirkabel. Dengan koneksi WiFi, data mengenai ketersediaan slot parkir, informasi kendaraan, dan metrik lainnya dapat dikumpulkan secara real-time.
    </div>
    
* MQTT
    <div style="text-align:justify;">
    Dengan menggunakan MQTT proyek Parking Slot Recommendation dapat mencapai integrasi yang efisien dan andal antara berbagai perangkat yang terlibat, memastikan pengiriman dan penerimaan data dengan cepat dan hemat energi.
    </div>
    
**3. Source Code Program**
Source code dari proyek "PARKING SLOT RECOMMENDATION"
```
#include <esp_now.h>
#include <WiFi.h>

uint8_t broadcastAddress[] = {0xFC, 0xB4, 0x67, 0x4E, 0xF8, 0x0C};
const int pinSensorIR = 15;
int ssensor1 ;

typedef struct struct_message {
    int sensor1 ;
} struct_message;
struct_message kirim;
//

esp_now_peer_info_t peerInfo;

void OnDataSent(const uint8_t *mac_addr, esp_now_send_status_t status) {
  Serial.print("\r\nLast Packet Send Status:\t");
  Serial.println(status == ESP_NOW_SEND_SUCCESS ? "Delivery Success" : "Delivery Fail");
}
void read(){

}
 
void setup() {
  Serial.begin(115200);
    pinMode(pinSensorIR, INPUT);



  WiFi.mode(WIFI_STA);

  if (esp_now_init() != ESP_OK) {
    Serial.println("Error initializing ESP-NOW");
    return;
  }
  esp_now_register_send_cb(OnDataSent);

  memcpy(peerInfo.peer_addr, broadcastAddress, 6);
  peerInfo.channel = 0;  
  peerInfo.encrypt = false;
  
  if (esp_now_add_peer(&peerInfo) != ESP_OK){
    Serial.println("Failed to add peer");
    return;
  }
}
 
void loop() {
    ssensor1 = digitalRead(pinSensorIR) ;
    if (ssensor1 == HIGH){
      ssensor1 = 0;
    }
    else{
      ssensor1 = 1;
    }
  kirim.sensor1 = ssensor1;
  Serial.print(ssensor1);
  delay(2000);
  
  esp_err_t result = esp_now_send(broadcastAddress, (uint8_t *) &kirim, sizeof(kirim));
   
  if (result == ESP_OK) {
    Serial.println("Sent with success");
  }
  else {
    Serial.println("Error sending the data");
  }
  delay(2000);
}
```
<div style="text-align:justify">
ESP32 membaca status sensor IR yang dapat digunakan untuk mendeteksi keberadaan kendaraan di tempat parkir. Untuk melakukan rekomendasi tempat parkir, ESP-NOW digunakan untuk mengirimkan data sensor tersebut ke node lain dalam jaringan.
    
</div>

```
#define RXp2 16
#define TXp2 17
#include <Arduino.h>
#include <WiFi.h>
#include <PubSubClient.h>

const char *ssid = "BIZZNET 4G"; 
const char *password = "ikhsar_sulaeman";  
const char *mqtt_broker = "broker.emqx.io";
const char *topic = "projectiot/tubesnihhh";
const char *mqtt_username = "tubesbesarrr";
const char *mqtt_password = "adadehhh";
const int mqtt_port = 1883;

WiFiClient espClient;
PubSubClient client(espClient);
uint32_t counter;
char str[80];
char paket[80];

void callback(char *topic, byte *payload, unsigned int length) {
 Serial.print("Message arrived in topic: ");
 Serial.println(topic);
 Serial.print("Message:");
 for (int i = 0; i < length; i++) {
     Serial.print((char) payload[i]);
 }
 Serial.println();
 Serial.println("-----------------------");
}

void setup() {
  Serial.begin(115200);
  Serial2.begin(115200, SERIAL_8N1, RXp2, TXp2);

 WiFi.begin(ssid, password);
 while (WiFi.status() != WL_CONNECTED) {
     delay(500);
     Serial.println("Connecting to WiFi..");
 }
 Serial.println("Connected to the WiFi network");

 client.setServer(mqtt_broker, mqtt_port);
 client.setCallback(callback);
 while (!client.connected()) {
     String client_id = "esp32-client-";
     client_id += String(WiFi.macAddress());
     Serial.printf("The client %s connects to the public mqtt broker\n", client_id.c_str());
     if (client.connect(client_id.c_str(), mqtt_username, mqtt_password)) {
         Serial.println("Public emqx mqtt broker connected");
     } else {
         Serial.print("failed with state ");
         Serial.print(client.state());
         delay(2000);
     }
 }
 client.publish(topic, "Hi EMQX I'm esp ^^");
 client.subscribe(topic);
}


void loop() {

  client.loop();   
  int sensor = Serial2.readString().toInt();
  sprintf(str, "%i", sensor );
 counter++;
 client.publish(topic, str);
 delay(2000);
  
}


```   


<div style="text-align:justify;">
membaca data dari sensor, mengirimkannya ke server MQTT, dan menerima rekomendasi atau melakukan proses lebih lanjut berdasarkan data tersebut.
</div>

```
#include <esp_now.h>
#include <WiFi.h>
int rsensor1;

typedef struct struct_message {
    int sensor1 ;
} struct_message;
struct_message terima;
//
void OnDataRecv(const uint8_t * mac, const uint8_t *incomingData, int len) {
  memcpy(&terima, incomingData, sizeof(terima));
  Serial.print("Bytes received: ");
  Serial.println(len);
  rsensor1 = terima.sensor1 ;
  Serial.print("sensor :");
  Serial.println(rsensor1);
}
 
void setup() {
  Serial.begin(115200);
  
  WiFi.mode(WIFI_STA);

  if (esp_now_init() != ESP_OK) {
    Serial.println("Error initializing ESP-NOW");
    return;
  }
  
  esp_now_register_recv_cb(OnDataRecv);
}
 
void loop() {
  Serial.println(rsensor1);
  delay(2000);
}
```
<div style="text-align:justify;">
ESP32 pada perangkat penerima digunakan untuk menerima data dari perangkat pengirim. Nilai sensor yang diterima kemudian ditampilkan di Serial Monitor.
</div>


**4. Tampilan Node Red**

![node-red](https://hackmd.io/_uploads/S1UVeg3PT.png)


**5. Tampilan Broker MQTT**

Berikut ini adalah tampilan untuk MQTT Broker.
![mqtt](https://hackmd.io/_uploads/Hy3u9loD6.jpg)



**6. Design Alat**
* Design Schematic
    ![image](https://hackmd.io/_uploads/SyUvZToDT.png)


* Design Alat
    Receiver ESPNow + Gateway MQTT
    ![image](https://hackmd.io/_uploads/Sk60W6jDa.png)
    
    ESP Sensor
    ![image](https://hackmd.io/_uploads/Bye-GaovT.png)
    
**7. Laporan Pengeluaran Biaya dan Pengadaan Alat**
    
<div style="text-align:center;">
    
| No  | Nama Barang                | Jumlah Barang | Harga   |
| --- | -------------------------- | ------------- | ------- |
| 1   | ESP32 | 3 Pcs        | 180.000  |
| 2   | Infrared | 1 Pcs        | 10.200  |
| 3  | Kabel Jumper               | 1 Pcs       | 6.000   |
|     | Total                      | 5 Pcs        | 196.200 |
</div>

# Cara Kerja Alat
<div style="text-align:justify;">
    ESP32 dengan sensor infrared membaca data dan mengirim status parkir melalui ESP-NOW, kemudian informasi itu akan diterima ke server MQTT. Server MQTT memungkinkan sistem untuk memberikan rekomendasi tempat parkir atau memberikan informasi status parkir kepada pengguna.
</div>

# Video Demonstrasi
<iframe width="560" height="315" src="https://www.youtube.com/embed/Y_GR52sozts?si=taSY4DHOuGypitpu" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>
    
# Kesimpulan
<div style="text-align:justify;">
    Dengan adanya sistem IoT ini pengguna kendaraan tidak perlu khawatir lagi jika tidak bisa menemukan tempat parkir yang kosong. Sistem ini bisa memandu pengendara untuk mencari dan memakirkan kendaraannya dengan efektif dan efisien.
</div>
    
# Saran
<div style="text-align:justify">
    Saran untuk peneliti berikutnya, agar selalu memeriksa apakah WiFi terhubung atau tidak, karena sistem ini membutuhkan WiFi untuk bekerja dengan baik.