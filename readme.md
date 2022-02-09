# Belajar Microservices (Programmer Zaman Now)

### Kenapa Perlu Belajar Microservices?
- Kekinian
- Ekosistem Pendukung
- Banyak digunakan di big company
- Sudah jadi bare minimum knowledge untuk senior engineer

## Arsitektur Monolith

### Apa itu arsitektur monolith?
- Single Deployment Unit
- Semua fitur dalam satu aplikasi besar (monolith)

**Ketika develop aplikasi, selalu mulai dari monolith dahulu**

### Kelebihan arsitektur monolith
- Mudah di-develop
- Mudah di-deploy (tinggal deploy 1 aplikasi saja)
- Mudah di-test
- Mudah di-scale

**Apabila team masih kecil, lebih baik menggunakan monolith**

### Kekurangan (problem) arsitektur monolith
- Mengintimidasi developer baru yang bergabung
- Scaling development dengan banyak developer agak menyulitkan
- Butuh kontrak panjang dengan tech stack yang digunakan (akan sulit ganti-ganti tech stack)
- Scaling pada bagian tertentu tidak bisa dilakukan (sekali scaling harus scaling satu aplikasi, tidak bisa per-fitur)
- Running monolith sangat berat (apabila skala sudah besar)

## Arsitektur Microservices

### Apa itu arsitektur microservices?
- Kumpulan aplikasi kecil yang saling bekerja sama (terintegrasi)
- Tiap aplikasi fokus mengerjakan satu pekerjaan dengan baik
- Independent Deployability, tiap aplikasi dapat di-deploy dan diubah tanpa tergantung dengan aplikasi lain
- Setiap komponen pada sistem dibuat dalam service
- Komunikasi antar service paling umum melalui network-call (HTTP, RestAPI, gRPC)

### Diagram sederhana microservice
![Microservice Diagram](/img/diagram-sederhana-microservices.png)

### Kelebihan arsitektur microservice
- Mudah dimengerti, karena tiap service ukurannya relatif kecil
- Lebih mudah di-develop, maintain, test, dan deploy
- Lebih mudah untuk berganti teknologi
- Tiap-tiap service bisa pakai teknologi yang berbeda-beda baik dalam sisi aplikasi maupun database
- Mudah di-scale sesuai kebutuhan
- Bisa dikerjakan dalam tim-tim kecil (untuk tiap-tiap service)

### Problem arsitektur microservice
- Distributed system
- Komunikasi antar system yang prone to error
- Testing interaksi/integrasi antar service akan lebih sulit

### Pembagian aplikasi microservice
- Split berdasarkan business domain (pakai Domain Driven Design)

### Seberapa kecil aplikasi microservice?
- Single Responsibility
- Sekecil mungkin sehingga bisa dimengerti satu orang
- Bisa dikerjakan sejumlah X developer


### Perbedaan monolith dan microservice
| Monolith      | Microservices |
| ----------- | ----------- |
| Simplicity      | Partial Development and Deployment       |
| Consistency   | Availability        |
| Easy to refactor   | Multiple Platform        |
|    | Easy to scale        |


## Database per Service

### Decentralized Database
- Tiap service direkomendasikan untuk memiliki database masing-masing
- Karena tiap service memiliki kegunaan yang berbeda-beda, sehingga lebih bagus jika pakai database sesuai kebutuhan
- Satu database hanya untuk digunakan satu service

## Mengapa harus Database per Service?
- Memastikan independensi tiap service (tidak ada ketergantungan antar service)
- Tiap service bisa menggunakan aplikasi dengan database sesuai kebutuhan
- Service tidak perlu tahu kompleksitas internal database service lain


## Shared Database
**Ketika develop microservice tidak ada standar baku, implementasinya akan berbeda-beda termasuk di shared database ini**a baru migrasi dari monolith ke microservice

### Kapan harus shared database?
- Shared database cocok jika kita baru migrasi dari monolith >
- Ketika kita bingung memecahkan data antar service
- Ketika dikejar waktu, sehingga belum ada waktu untuk buat API


## NoSQL (Not Only SQL)

### Jenis-jenis NoSQL
- Document Oriented Database (MongoDB)
- Key-Value Database (Redis)
- Column Families Database (Apache Cassandra)
- Graph Database (Neo4J)
- Search Database (Elasticsearch)
- Time Series Database (InfluxDB)

### Kenapa harus tahu NoSQL
- Agar bisa disesuaikan dengan kebutuhan microservice
- Bisa mencari alternatif lebih banyak perihal olah data
- Mempercepat dalam proses penulisan atau pencarian

### Contoh Kasus
- Product Service -> MongoDB
- Catalog Service -> Elasticsearch
- Order Service -> Postgresql
- Member Service -> Neo4J
- Activity Service -> InfluxDB


## Remote Procedure Invocation (Komunikasi Microservice)
- **Biasa disebut juga RPC (Remote Procedure Call)**
- **Dibutuhkan ketika suatu service butuh data service lain**

### Komunikasi antar service
- Idealnya komunikasi dilakukan melalui RPI/RPC
- Tidak direkomendasikan untuk komunikasi langsung melalui database

### Contoh Remote Procedure Invocation
- Restful API (HTTP)
- gRPC
- Apache Thrift
- SOAP
- Java RMI
- Corba (Common Object Request Broker Architecture)
- etc.

### Keuntungan menggunakan RPC
- Sederhana dan mudah
- Biasa digunakan untuk komunikasi request-response
- Biasa digunakan untuk proses Sync (sinkronus)


## Messaging

### Problem di Komunikasi RPI/RPC
- Proses lama, jika butuh komunikasi lebih dari dua service maka akan saling tunggu
- Mengirim data yang sama berkali-kali ke beberapa service
- Membuat paralel process menjadi sangat rumit

**Messaging adalah proses yang dilakukan secara async untuk mengatasi problem komunikasi menggunakan RPI/RPC seperti di atas**

### Komunikasi dengan cara Messaging
- Messaging menggunakan proses asynchronous (tidak bisa diterapkan ke komunikasi yang butuh response langsung)
- Async artinya dilakukan tanpa harus menunggu proses selesai (tidak ditunggu)
- Dalam async, kadang tidak peduli balasan dari service yang dituju
- Biasanya komunikasi messaging butuh message channel sebagai jembatan untuk mengirim dan menerima data
- Direkomendasikan menggunakan aplikasi **Message Broker** untuk melakukan management Message Channel
- Message Broker: Redis(PubSub), Apache Kafka, RabbitMQ, NSQ, Google PubSub, AWS SQS, etc.

### Diagram Komunikasi menggunakan Messaging
![Diagram Komunikasi Message Broker](/img/diagram-komunikasi-message-broker.png)

#### Penjelasan :
1. Dalam message broker di diagram, terdapat 3 message channel (email, sms, order)
2. Order service hanya perlu mengirim ke message channel
3. Secara otomatis akan diteruskan ke service lain secara async
4. Satu message channel bisa mengirim ke beberapa channel, sehingga dari order service tidak perlu mengirim data yang sama berkali-kali, cukup satu kali ke satu message channel, dan diteruskan ke beberapa service 

### Kesimpulan Messaging
- Messaging diperlukan jika response dari service tidak diperlukan oleh end-user secara langsung, sehingga bisa dilakukan secara asynchronous

## Type of Microservices
- Stateless Microservice
- Persistence Microservice
- Aggregation Microservice

### Stateless Microservice
- Pada umumnya tidak memiliki database
- Digunakan untuk melakukan tugas sederhana
- Biasa digunakan juga sebagai utility service lain
- Tidak dependent dengan service lain
Contoh :
- Email Service (Berguna hanya untuk mengirim email)
- SMS Service (Berguna hanya untuk mengirim SMS)

### Persistence Microservice
- Pada umumnya memiliki database
- Bisa juga disebut sebagai Master Data Service
- Biasa digunakan untuk mengolah data di database (CRUD)
Contoh :
- Customer Service (mengolah data customer)
- Product Service (mengolah data product)
- Order Service (mengolah data order)

### Aggregation Microservice
- Tergantung/dependent dengan service lain
- Biasa digunakan sbeagai pusat business logic aplikasi
- Boleh memiliki database ataupun tidak
- Tidak bisa berdiri sendiri, dependent dengan service lain
- Melakukan orkestrasi terhadap service-service lain
Contoh :
- Cart Service (Ada beberapa orkestrasi ke service lain ketika cart service dijalankan)
- Payment Service

#### Contoh Kasus Aggregation (Orkestrasi ke service lain)
![Contoh Kasus Aggregation](/img/contoh-kasus-aggregation.png)
1. Ketika melakukan pemesanan barang, akan terjadi beberapa penembakan ke service lain
2. Get product untuk mengecek ada atau tidaknya produk
3. Get customer untuk mengecek alamat pengiriman customer
4. Tembak ke order service
5. Tembak ke payment service
6. etc.


## Service Orchestration
- Cara aggregation microservice dengan microservice lain, jika menggunakan RPC, maka dinamakan Service ORchestration Pattern
- Dalam Service Orchestration Pattern, Aggregation Microservice bertugas untuk mengatur alur/flow dari business logic sistem

### Contoh service orchestration
![Service Orchestration](/img/contoh-service-orchestration.png)
- Dalam satu transaction service, akan terjadi beberapa orkestrasi ke service-service lain

### Keuntungan Service Orchestration
1. Mudah dibuat, kode business logic akan terpusat di Aggregation Microservice
2. Mudah dimengerti, karena business logic akan terpusat di Aggregation Microservice

### Kekurangan Service Orchestration
1. Aggregation Microservice terlalu dependent dengan service lain
2. Aggregation Microservice akan lebih almbat karena harus terkoneksi dengan service lain
3. Aggreagtion Microservice akan prone to error jika terdapat error di service lain
4. Jika perlu microservice baru, perlu dilakukan perubahan di Aggregation Microservice


## Service Coreography
- Komunikasi antar aggregation service dengan service lain menggunakan messaging dalam service coreography
- Jika di Service Orchestration Pattern, Aggregation Microservice adalah service yang sangat kompleks dan bertugas untuk mengatur semua flow dari business logic sistem, sedangkan dalam Service Coreography, semua Microservices dituntut untuk menjadi pintar, tidak hanya diperintah oleh Aggregation Microservices
- Tidak ada lagi perintah direct dari Aggregation Microservice ke service lain, melainkan melalui messaging
- Buisiness logic tidak akan terpusat di Aggregation Microservice lagi

### Diagram UML Contoh Service Coreography
![Service Coreography](/img/service-coreography.png)

- Tidak akan ada lagi perintah direct daari Aggregation Microservice
- Semua perubahan akan dikirimkan lewat message channel **(by Event)**, ketika terjadi perubahan di salah satu service, service lain yang membutuhkan perubahan tersebut boleh mengambil perubahan lewat message channel. Jadi tidak perlu memakai perintah

### Keuntungan Service Coreography
- Tidak perlu membuat perintah baru, perintah yang digunakan hanya publish dan subscribe
- Aggregation Microservice akan lebih cepat, karena tidak perlu komunikasi dengan service lain
- Jika ada microservice baru, Aggregation Microservice tidak perlu diubah
- Lebih independent dan memiliki skalabilitas yang fleksibel

### Kekurangan Service Coreography
- Lebih sulit debugging ketika terjadi masalah, karena logic business tersebar di beberapa service
- Karena business logic terdistribusi, maka akan sulit untuk memahaminya, tidak terpusat di Aggregation Microservice, harus cek satu-satu service
- Terlalu dependent dengan message broker


## API Gateway
- API Gateway adalah sebuah microservice yang mengatur routing antar service

### Masalah Mengekspos Microservices
- Semua service bisa diakses dari luar
- Jika butuh Autentikasi, harus diimplementasikan di semua service
- Rawan terjadi kebocoran data karena service bisa diakses dari luar

### Apa itu API Gateway?
- API Gateway bertugas sebagai middleware atau gerbang dari luar ke dalam
- Luar adalah akses internet, Dalam adalah aplikasi microservice
- API Gateway bertugas sebagai proxy server ke semua aplikasi microservice
-Aplikasi microservice yang ada di dalam hanya bisa diakses melalui API Gateway

### Keuntungan API Gateway
- Lebih aman dari sisi security, karena hanya satu gerbang
- Service tidak perlu implementasi Autentikasi, cukup dilakukan di API Gateway
- Bisa digunakan sebagai load balancer
- Bisa digunakan sebagai rate limiter
- Bisa dipakai sebagai pengaman sehingga error dari service yang tidak terekspos

### Contoh API Gateway
- NGINX
- Apache HTTPD
- Kong
- Netflix Zuul
- Spring Cloud Gateway


## Authentication dan Authorization
- Dalam API Gateway kita bisa mengatur autentikasi dan otorisasi

|   **Authentication**    | **Authorization** |
| ----------- | ----------- |
| Memvalidasi kredensial untuk pemilik identitas      | Authorization adalah proses yang dilaakukan setelah proses Authentication       |
| Contohnya adalah proses login   | Memvalidasi apakah pemilik identitas memiliki hak akses untuk mengakses resource yang diminta        |
|    | Contohnya adalah Access-Control List, dll.        |

### Auth Service
- Auth Service adalah sebuah microservice yang mengatur autentikasi dan otorisasi
- Biasa ditaruh di sebelah API Gateway

### Diagram Auth Service
![Auth Service](/img/auth-service.png)

### Diagram Integrasi dengan Auth Service
![Integrasi Auth Service](/img/integrasi-auth-service.png)
- Setelah Auth Service approve, maka akan dikembalikan ke API Gateway, dan API Gateway akan meneruskan request ke service yang diperlukan, jika service yang diperlukan memerlukan data autentikasi, maka service akan mengirim request lagi ke Auth Service, hal ini cukup berat

### API Gateway sebagai Middleware
![API Gateway as Middleware](/img/api-gateway-as-middleware.png)
- Jika ada request dari internet, API Gateway akan meneruskan ke Auth Service untuk autentikasi, jika berhasil, hasil response dari Auth Service juga mengandung data autentikasi, sehingga API Gateway bisa mengirimkan data tadi ke request yang diperlukan
- Response biasanya berbentuk encrypted token seperti JWT

### Teknologi Pendukung Auth
- Secure Cookie
- OAuth
- JWT
- Basic Auth
- API Key


## Backend for Frontend
- Dalam aplikasi microservice besar, biasanya terdapat beberapa frontend yang mengakses aplikasi microservice kita, baik itu dari mobile, web, maupun third party.

### Permasalahan Tiap Frontend
- Tiap frontend punya mekanisme autentikasi berbeda
- Kecepatan bandwidth tiap frontend berbeda
- API yang dibuthkan tiap frontend beda
- Semua kebutuhan frontend harus diimplementasikan di satu API Gateway

### Apa itu Backend for Frontend?
- Backend for Frontend adalah menyediakan backend khusus untuk frontend tertentu
- Biasanya satu backend akan melayani satu frontend secara spesific
- Makin banyak jenis frontend, makin banyak pula backend yang dibuat

### Diagram Backend for Frontend
![Backend for Frontend](/img/backend-for-frontend.png)
- Tiap-tiap frontend memiliki API sendiri-sendiri
- Authentikasi akan terjadi di masing-masing API

### Keuntungan Backend for Frontend
- Pengembangan backend bisa terisolasi satu sama lain, team bisa dipisah per frontend
- Logic untuk frontend tidak tercampur di satu backend

### GraphQL sebagai Alternative Backend for Frontend
- GraphQL adalah query language untuk API
- GraphQL bisa digunakan untuk memanipulasi response API secara runtime
- Frontend bebas menentukan data apa saja yang ingin didapat
- Backend hanya perlu menyediakan data lengkap, dan Frontend bisa memiliki kebebasan untuk menentukan data apa saja yang diinginkan

### Diagram GraphQL sebagai Alternative Backend for Frontend
![GraphQL as Alternative](/img/graphql-as-alternative.png)

### Kekurangan Menggunakan GraphQL
- Membutuhakn development GraphQL Server di Backend
- Membutuhkan development GraphQL Client di Frontend


## CQRS (Command Query Responsibility Segregation)
- CQRS adalah proses membedakan operasi Command dan operasi Query
- Operasi command adalah operasi mengubah data (Create, Update, Delete)
- Operasi Query Adalah operasi mengambil data (Get, Search)
- Dalam CQRS, biasanya service atau db dibedakan untuk kebutuhan command dan query

### Diagram CQRS
![CQRS](/img/cqrs.png)

### Keuntunga CQRS
- Bisa memilih database berbeda sesuai kebutuhan untuk proses command dan query
- Membedakan model untuk command dan query di aplikasi akan lebih mudah dibandingkan dengan menggunakan model yang sama untuk semua operasi
- Performa aplikasi akan lebih baik, karena kita memisah antara component untuk command dan query

### CQRS Menggunakan Messaging
![CQRS Messaging](/img/cqrs-messaging.png)
- Command dan Query bisa dipisah menjadi servce yang berbeda
- Kedua service tersebut dihubungkan dengan message broker, by Event
- Ketika ada perubahan data lewat Command, akan dikirim ke message channel, dan data di message channel akan diteruskan ke consumer, dan akan diteruskan lagi ke Elasticsearch untuk nanti digunakan sebagai Query

### Keuntungan CQRS menggunakan Messaging
- Aplikasi Command dan Query terpisah, sehingga bisa dikerjakan oleh team yang berbeda
- Aplikasi Command tidak perlu pusing memikirkan struktur data aplikasi Query, hanya cukup mengirim datanya ke message broker
- Scaling aplikasi bisa sesuai dengan kebutuhan, baik Command atau Query
- Jika aplikasi Query sedang error, data dari Aplikasi Command akan tetap aman tersimpan di Message Broker
- Mekanisme retry akan lebih mudah dilakukan jika melalui message broker

## Server Side Discovery
- Membuat server khusu sebagai router atau load balancer ke service yang akan dipakai
- Client hanya butuh terkoneksi ke router atau load balancer
- Jika jumlah node/service bertambah atau berkurang, router yang hanya perlu diubah, client tidak perlu diubah

### Diagram Server Side Discovery
![SSD](/img/ssd.png)

### Contoh Router atau Load Balancer
- NGINX
- Apache HTTPD
- Traefik
- HProxy

### Kekurangan Server Side Discovery
- Membutuhkan development router atau load balancer di Backend, jika banyak node/service, maka development router akan semakin banyak
- Agar tidaak terjadi single point of failure, maka router harus di-setup sebanyak 2 instance 
- Cost akan lebih tinggi, karena 1 service harus menjalankan 2 router
#### Diagram
![Kekurangan SSD](/img/kekurangan-ssd.png)

## Client Side Discovery
**Client di sini adalah service yang melakukan request**
- CSD adalah solusi agar client bisa terkoneksi ke service yang akan dipakai, dan tahu service mana yang harus dipakai
- Tidak perlu ada router atau load balancer untuk berkomunikasi dengan service lain
- Semua logic untuk distribusi traffic harus dilakukan di client atau service yang melakukan request

### Kekurangan Client Side Discovery
- Client harus tahu lokasi semua service
- Jika jumlah node service bertambah atau berkurag, client harus diubah untuk mengetahui lokasi barunya
- Jika client salah implementasi logic untuk load balancer, maka traffic ke service yang dituju bisa tidak merata pembagiannya

 
## Service Registry
- Service Registry adalah sebuah service yang menyediakan informasi service yang akan dipakai
- Service Registry bisa membantu client untuk mengetahui lokasi service yang akan dipakai
- Semua service akan meregistrasikan alamat lokasinya di service registry ketika pertama kali nyala
- Semua service akan laporan ke service registry jika akan berhenti beroperasi, sehingga service registry akan menghilangkan informasi service tersebut agar tidak mendapat traffic dari client/service yang bertanya

### Diagram Registrasi ke Service Registry
![Registrasi Service Registry](/img/registrasi-service-registry.png)
- Ketika nyala, service akan registrasikan dirinya ke Service Registry

### Diagram bertanya ke Service Registry
![Bertanya Service Registry](/img/bertanya-service-registry.png)
- Service client akan bertanya ke service registry untuk mengetahui lokasi service yang akan dipakai
- Untuk menghindari berat ketika bertanya, bisa diberika periode waktu saja, bertanya tiap beberapa periode waktu saja

### Health Check Service Registry
![Healtch Check Service Registry](/img/health-check-service-registry.png)
- Secara regular service registry akan melakukan pengecekan ke service yang terdaftar, jika service yang terdaftar tidak aktif, maka service registry akan menghapus informasi tersebut

### Contoh Aplikasi Service Registry
- Hashicorp Consul
- Netflix Eureka


## Centralized Configuration
- Konfigurasi adalah sesuatu yang tidak asing lagi saat membuat aplikasi
- Tiap aplikasi biasanya memiliki konfigurasi, seperti konfigurasi database, dsb.
- Dimana sebaiknya menyimpan konfigurasi? Agar mudah di-maintain dan digunakan oleh aplikasi kita?

### Dimana menyimpan konfigurasi?
- Database
- File
- Environment Variable

### Apa itu Centralized Configuration?
- Centralized Configuration adalah pattern dimana kita menyimpan konfigurasi dalam sebuah service, dan kita mengakses konfigurasi dari service tersebut
- Service yang butuh konfigurasi akan bertanya ke palikasi tersebut untuk mendapatkan data konfigurasinya

### Skema Aplikasi Centralized Configuration
- PlantUML

### Contoh Aplikasi Centralized Configuration
- Hashicorp Consul
- Hashicorp Vault
- Etcd
- Zookeeper
- Doozerd