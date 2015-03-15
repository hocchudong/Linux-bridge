# Linux-bridge
Doc lại kiến thức tìm hiểu được về Linux bridge
- [Mục lục]
- [1. Khái niệm - ứng dụng](#kn-ud)
- [2. Kiến trúc](#kt)
- [3. Các thành phần](#tp)
- [4. Chức năng của một switch ảo do Linux bridge tạo ra](#cnsw)
- [5. Lab](#lab)


<a name="kn-ud"></a>
### 1. Khái niệm - ứng dụng
Linux bridge là một phần mềm đươc tích hợp vào trong nhân Linux để giải quyết vấn đề ảo hóa phần network trong các máy vật lý. Về mặt logic Linux bridge sẽ tạo ra một con switch ảo để cho các VM kết nối được vào và có thể nói chuyện được với nhau cũng như sử dụng để ra mạng ngoài

Ngoài ra khi tìm hiểu Linux bridge còn có một số thuật ngữ như:
- Port : tương tự như cổng của một con switch thật
- Bridge: tương đương với từ switch
- tap : được hiểu ở lớp 2
- FDB : Forwading database
<a name="kt"></a>
### 2. Kiến trúc
Kiến trúc của Linux bridge như hình sau:

<img class="image__pic js-image-pic" src="http://i.imgur.com/xob7ljQ.png" alt="" id="screenshot-image">

*theo nguồn của NTT*
<a name="tp"></a>
### 3. Các thành phần
Trong kiến trúc trên có các thành phần:
- Tap : có thể hiểu nó là một giao diên mạng để các máy ảo có thể giao tiếp được với bridge và nó nằm trong nhân kernel. Tap hoat động ở lớp 2 trong mô hình OSI
- fd (forward data): dùng để chuyển tiếp data từ máy ảo

<a name="cnsw"></a>
### 4. Chức năng của một switch ảo do Linux bridge tạo ra
- STP: là tính năng chống loop gói tin trong switch
- VLan: là tính năng rất quan trọng trong một switch
- FDB: là tính năng chuyển gói tin theo database được xây dựng giúp tăng tốc độ của switch

<a name="lab"></a>
### 5. Lab

**Chuẩn bị**

Một máy Ubuntu có cài KVM và có 2 card mạng là eth0 và eth1. Việc cài đặt KVM có thể làm theo hướng dẫn sau [đây](http://www.server-world.info/en/note?os=Ubuntu_14.04&p=kvm&f=1) hoặc chạy scrip **setup-kvm**

<img class="image__pic js-image-pic" src="http://i.imgur.com/mxtaM58.png" alt="" id="screenshot-image">

**Trường hợp 1**: Tạo ra một bridge và gán port eth1 cho bridge đó ( sử dụng câu lệnh `brctl` )

Bước 1: Tạo một bridge có tên là hoainam

```
# brctl addbr <Tên bridge>
VD: # brctl addbr hoainam
```

Bước 2: Gán port eth1 cho bridge đó

```
# brctl addif <tên bridge> <tên port gán>
VD: # brctl addif hoainam eth1
```
Bước 3: Comment card mạng eht1 trong file `/etc/network/interface` như hình sau:

<img class="image__pic js-image-pic" src="http://i.imgur.com/zE4p2qh.png" alt="" id="screenshot-image">

Bước 4: Thêm dòng sau vào trong file `/etc/network/interface` như sau 

```
auto hoainam
iface hoainam inet dhcp
bridge_ports eth1
bridge_stp off # kich hoat che do STP trong bridge
bridge_fd 0 
bridge_maxwait 0
```
Bước 5: Khởi động lại internet

```
# ifdown -a && ifup -a
```

Bước 6: Kiếm tra lại hoạt động của bridge

Dùng lệnh sau `brctl show` kết quả hiển thị như hình vẽ đồng nghĩa với việc gán card thành công

<img class="image__pic js-image-pic" src="http://i.imgur.com/FIGMjK7.png" alt="" id="screenshot-image">

Khi hoàn thành xong việc cài đặt sơ đồ logic trong máy có thể như hình sau:

<img class="image__pic js-image-pic" src="http://i.imgur.com/PlRbegR.png" alt="" id="screenshot-image">

Để chắc chắn hơn bạn hay tạo một máy ảo và dùng card `hoainam` để khi khởi động máy ảo có nhận dải ip đúng với dải của card eth1 không. Nếu đúng thì việc tạo một bridge trong host thành công

**Trường hợp 2**: Gắn 2 port eth0 và eht1 vào trong cùng một bridge **hoainam**

- Các bước thực hiện

Bước 1: Ngắt card mạng eth0 ra khỏi bridge là br0
(Khi cài đặt KVM-QEMU chúng ta đã làm các bước tạo một bridge là br0 và gắn nó vào card mạng eth0 nên bây giờ cần rút card eth0 ra khỏi bridge br0 bằng câu lệnh )

```
# brctl delif <tên bridge> <tên card mạng cần rút> 
VD: #brctl br0 eth0
```

Bước 2: Gắn card mạng eth0 vào bridge **hoainam**

```
# brctl addif <tên bridge> <tên card muốn cắm>
VD: # brctl addif hoainam eth0
```

Bước 3: Kiểm tra

Dùng câu lệnh `brctl show` để kiểm tra nếu kết quả hiển thị như hình sau thì đồng nghĩa với việc đã cắm 2 card mạng vào một bridge thành công

<img class="image__pic js-image-pic" src="http://i.imgur.com/9TxA1Wn.png" alt="" id="screenshot-image">

Sơ đồ vật lý:

<img class="image__pic js-image-pic" src="http://i.imgur.com/TB4P6CI.png" alt="" id="screenshot-image">

- Thông tin thêm

Sau khi thực hiện việc gắn 2 card vào một bridge thành công, ta lưu tâm đến một tham số là priority cho port. Có nghĩa là port nào có chỉ số priority cao hơn thì khi máy ảo gắn bridge đó vào thì nó sẽ nhận được IP của port có priority cao hơn

Câu lệnh để gán thông số priority cho port

```
# brctl setportprio <tên bridge> <tên port> <chỉ số priority>
```

VD: Gán chỉ số priority cho port cắm eth0 cao hơn eth1
```
# brctl setportprio hoainam eth0 3
# brctl setportprio hoainam eth1 1
```
Thực hiện kiểm tra

Lúc này trong hệ thống Lab của tôi có dải IP như sau:

|Cổng | Dải IP |
|-----|--------|
|eth0 | 10.10.30.0/24 |
|eth1 | 10.10.10.0/24 |
|eth2 | 10.10.20.0/24 |

Cài đăt máy ảo chỉnh phần mạng chọn bridge **hoainam** 

<img class="image__pic js-image-pic" src="http://i.imgur.com/LCj5vYp.png" alt="" id="screenshot-image">

Theo câu lệnh đã thực hiện thì khi chọn máy ảo được khởi động nó sẽ nhận IP của dải eth0 do ta đã gán priority cao hơn. Và đây là kết quả 

<img class="image__pic js-image-pic" src="http://i.imgur.com/wsgJmih.png" alt="" id="screenshot-image">



