# BÁO CÁO KIỂM THỬ API BẰNG APACHE JMETER

## 1. Giới thiệu và mục tiêu

Apache JMeter là công cụ mã nguồn mở phục vụ kiểm thử tải và đo lường hiệu năng. Bài thực hành sử dụng JMeter để gửi HTTP GET tới API thử nghiệm, kiểm tra phản hồi và phân tích số lượng mẫu, thời gian phản hồi, tỷ lệ lỗi và thông lượng.

Mục tiêu: tạo Test Plan, cấu hình Thread Group, gửi HTTP Request, kiểm tra mã HTTP, đọc Summary Report và lưu bằng chứng thực nghiệm vào repository này.

## 2. Môi trường và đối tượng kiểm thử

| Thành phần | Thông tin |
|---|---|
| Công cụ | Apache JMeter 5.6.3 |
| Môi trường chạy | Máy cá nhân, Java JDK (chưa ghi nhận cấu hình phần cứng) |
| API | DummyJSON |
| Endpoint | `https://dummyjson.com/products/1` |
| HTTP Method | GET |
| Mã phản hồi mong đợi | 200 OK |

> Đây là API công khai; không suy rộng kết quả này thành năng lực chịu tải của một hệ thống sản xuất. Không thực hiện kiểm thử tải lớn vào dịch vụ bên thứ ba khi chưa được phép.

## 3. Kịch bản kiểm thử

Test Plan gồm Thread Group, HTTP Request (GET Product), Summary Report và View Results Tree. Trong kịch bản `JMeter_API_Test.jmx` có Response Assertion kiểm tra HTTP 200. File JMX trên repo hiện lưu cấu hình mẫu ban đầu (10 users); ảnh chụp của lần thử dưới đây thể hiện cấu hình đã thay đổi **trên máy**. Cần xuất/lưu lại file JMX sau khi chỉnh nếu muốn repo khớp hoàn toàn với lần chạy.

### 3.1. Cấu hình thử nghiệm mức 1.000 threads

| Thông số | Giá trị |
|---|---:|
| Number of Threads (users) | **1.000** |
| Ramp-up period | **100 giây** |
| Loop Count | **5** |
| Tổng số lượt request theo lý thuyết | **5.000** |

JMeter tăng dần thread trung bình khoảng 10 threads/giây trong giai đoạn ramp-up. Với một HTTP Request mỗi vòng và các vòng lặp hoàn thành, tổng số lần lấy mẫu dự kiến là 1.000 × 5 = 5.000. Đây **không đồng nghĩa** có 1.000 request đồng thời tại mọi thời điểm.

## 4. Kết quả thực nghiệm mức 1.000 / 100

Từ ảnh JMeter sau khi chạy: đồng hồ hiển thị `00:01:40`, trạng thái `0/1000` (không còn thread hoạt động). Trong View Results Tree, mẫu được chọn trả về **Response code: 200**, **Response message: OK**.

### 4.1. Số liệu Summary Report đã quan sát

**Quan trọng:** Summary Report chưa được xóa dữ liệu trước mỗi lần chạy, nên đang chứa nhiều nhãn sampler/lần chạy. Không thể coi dòng TOTAL 33.800 là kết quả riêng của cấu hình 1.000 threads.

| Nhãn mẫu | # Samples | Average (ms) | Min (ms) | Max (ms) | Std. Dev. (ms) | Error % | Throughput |
|---|---:|---:|---:|---:|---:|---:|---:|
| GET Product 1 | 3.049 | 123 | 47 | 8.524 | 295,35 | 0,00% | 7,2/sec |
| GET Product | 25.751 | 122 | 47 | 98.433 | 838,02 | 0,00% | 46,9/sec |
| GET Producttt | **5.000** | **97** | **47** | **2.440** | **68,33** | **0,00%** | **49,8/sec** |
| **TOTAL (nhiều lượt chạy)** | **33.800** | **118** | **47** | **98.433** | **737,35** | **0,00%** | **30,3/sec** |

**Đối chiếu:** Nhãn `GET Producttt` có 5.000 Samples, trùng tổng request lý thuyết của bài 1.000 × 5. Tuy nhiên, chỉ từ ảnh Summary Report chưa chứng minh chắc chắn đây là *toàn bộ và chỉ* dữ liệu của lần chạy 1.000 threads. Các thông số **97 ms, 0%, 49,8/sec** là số liệu của **dòng GET Producttt**, chỉ dùng làm kết quả riêng cho lần chạy này sau khi xác nhận tên sampler hoặc xuất tệp JTL tương ứng.

### 4.2. Phân tích và giới hạn kết quả

- Các dòng được chụp đều có tỷ lệ lỗi **0,00%** theo thống kê JMeter; mẫu kiểm tra trong View Results Tree cũng nhận **HTTP 200 OK**.
- Ở dòng `GET Producttt`, thời gian trung bình là **97 ms**, lớn nhất **2.440 ms**; thời gian phản hồi không đồng đều giữa mọi request.
- Dòng TOTAL có thời gian tối đa **98.433 ms**, nhưng đây là dữ liệu **cộng dồn nhiều lượt**; không gán giá trị này riêng cho bài 1.000 threads.
- Cần chạy lại với **Run → Clear All** trước mỗi lượt, lưu tệp kết quả riêng, để so sánh chính xác giữa các cấu hình.

## 5. Ảnh minh họa thực tế

Các ảnh đã được tải lên thư mục `images/` của repository. Đường dẫn dưới đây sử dụng **tên file thực tế trên GitHub**, không cần đổi tên.

### 5.1. Cấu hình Thread Group — 1.000 threads, ramp-up 100 giây

Ảnh thể hiện số người dùng ảo, thời gian ramp-up và số vòng lặp của lần thực hành.

![Cấu hình Thread Group 1000 threads](images/thread_group.png)

### 5.2. Cấu hình HTTP Request

Ảnh thể hiện HTTP Request được sử dụng để thử nghiệm API.

![Cấu hình HTTP Request](images/http.png)

### 5.3. Kết quả View Results Tree

Quan sát từng request và phản hồi. Ở mẫu đã kiểm tra: **HTTP 200 / OK**.

![View Results Tree - phản hồi API](images/view.png)

### 5.4. Ảnh kết quả bổ sung (get.png)

Ảnh minh họa kết quả mà sinh viên đã tải lên với tên `get.png`. Khi đối chiếu trực tiếp, nếu đây là ảnh Summary Report thì sử dụng nó làm bằng chứng cho bảng thống kê mục 4; nếu đây là ảnh HTTP Request/GET thì cần bổ sung ảnh Summary Report riêng.

![Ảnh kết quả JMeter get.png](images/get.png)

> **Lưu ý quan trọng về bảng thống kê:** Ảnh Summary Report trước đó được chia sẻ trong cuộc trò chuyện có dòng TOTAL 33.800 Samples cộng dồn nhiều lượt chạy. Báo cáo không khẳng định 33.800 Samples là kết quả riêng của cấu hình 1.000 threads.

## 6. Hướng dẫn tái lập kết quả và lưu bằng chứng

1. Mở `JMeter_API_Test.jmx` trong JMeter; kiểm tra tên sampler và cấu hình endpoint.
2. Cấu hình Thread Group theo mức tải cần thử, lưu thành tệp JMX riêng (ví dụ `JMeter_1000_users.jmx`).
3. Trước lần đo mới, xuất dữ liệu cũ nếu cần rồi chọn **Run → Clear All**.
4. Chạy kiểm thử trên dịch vụ có quyền thử tải; khi kết thúc, kiểm tra `0/N`, Summary Report và lỗi request.
5. Xuất kết quả từng mức tải riêng (`.jtl` hoặc `.csv`), lưu ảnh và gắn vào `images/`.
6. Với kiểm thử tải cao, chạy **non-GUI**, không bật View Results Tree để tránh tăng bộ nhớ và sai lệch kết quả.

## 7. Kế hoạch kiểm thử tiếp theo

| Mức kiểm thử | Threads | Ramp-up | Loop Count | Samples lý thuyết | Tình trạng |
|---|---:|---:|---:|---:|---|
| Ban đầu | 10 | 10 giây | 5 | 50 | Đã thử; kết quả cũ được trao đổi trong chat |
| Trung bình | 1.000 | 100 giây | 5 | 5.000 | Đã chạy; cần tách rõ dữ liệu từng lượt |
| Tải lớn (nếu được cấp phép) | 10.000 | 1.000 giây | 5 | 50.000 | **Chưa có kết quả — không tự điền số liệu** |

## 8. Kết luận

Đã thực hành cấu hình mức tải 1.000 threads, 100 giây ramp-up và 5 vòng lặp, quan sát phản hồi HTTP 200 và báo cáo Summary Report. Các dòng được chụp có Error 0,00%. Cần tách dữ liệu từng lần chạy trước khi kết luận định lượng về mức tải 1.000 threads và so sánh với mức 10.000 threads. Các ảnh minh họa hiện đã được chèn từ thư mục `images/`; cần đối chiếu nhãn và dữ liệu từng lượt chạy trước khi kết luận hiệu năng.

## 9. Tài liệu tham khảo

- [Apache JMeter](https://jmeter.apache.org/)
- [Getting Started](https://jmeter.apache.org/usermanual/get-started.html)
- [Building a Web Test Plan](https://jmeter.apache.org/usermanual/build-web-test-plan.html)
- [DummyJSON](https://dummyjson.com/)
