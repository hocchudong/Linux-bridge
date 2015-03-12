# Linux-bridge
Doc lại kiến thức tìm hiểu được về Linux bridge
- [Mục lục]
- [1. Khái niệm - ứng dụng](#kn-ud)
- [2. Kiến trúc](#kt)


### 1. Khái niệm - ứng dụng
Linux bridge là một phần mềm đươc tích hợp vào trong nhân Linux để giải quyết vấn đề ảo hóa phần network trong các máy vật lý. Về mặt logic Linux bridge sẽ tạo ra một con switch ảo để cho các VM kết nối được vào và có thể nói chuyện được với nhau cũng như sử dụng để ra mạng ngoài

Ngoài ra khi tìm hiểu Linux bridge còn có một số thuật ngữ như:
- Port : tương tự như cổng của một con switch thật
- Bridge: tương đương với từ switch
- tap : được hiểu ở lớp 2
- FDB : Forwading database

### 2. Kiến trúc
Kiến trúc của Linux bridge như hình sau:

<img class="image__pic js-image-pic" src="http://i.imgur.com/xob7ljQ.png" alt="" id="screenshot-image">

*theo nguồn của VTT*

### 3. Các thành phần
### 4. Chức năng của một switch ảo do Linux bridge tạo ra
- STP: là tính năng chống loop gói tin trong switch
- VLan: là tính năng rất quan trọng trong một switch
- FDB: là tính năng chuyển gói tin theo database được xây dựng giúp tăng tốc độ của switch

### 4. Lab

#### 4.1: Chuẩn bị

Một máy Ubuntu có cài KVM và có 2 card mạng là eth0 và eth1. Việc cài đặt KVM có thể làm theo hướng dẫn sau [đây](http://www.server-world.info/en/note?os=Ubuntu_14.04&p=kvm&f=1) hoặc chạy scrip **setup-kvm**




