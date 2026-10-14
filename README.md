# Báo cáo kiểm thử API bằng Apache JMeter

## 1. Giới thiệu

Apache JMeter là công cụ mã nguồn mở dùng để kiểm thử tải và đánh giá hiệu năng của ứng dụng, dịch vụ web và API. Trong bài thực hành này, sử dụng JMeter để gửi nhiều HTTP request đến API thử nghiệm, kiểm tra mã phản hồi và thu thập các chỉ số hiệu năng.

## 2. Mục tiêu

- Làm quen với giao diện và các thành phần cơ bản của Apache JMeter.
- Tạo Test Plan, Thread Group và HTTP Request.
- Kiểm tra phản hồi bằng Response Assertion.
- Theo dõi kết quả bằng View Results Tree và Summary Report.
- Lưu kịch bản kiểm thử và báo cáo trên GitHub.

## 3. Công cụ và đối tượng kiểm thử

- Apache JMeter: https://jmeter.apache.org/
- Java JDK: môi trường chạy JMeter.
- API thử nghiệm: DummyJSON
- Endpoint: `https://dummyjson.com/products/1`
- Phương thức: `GET`
- Mã HTTP mong đợi: `200`

> Lưu ý: API công khai phụ thuộc vào kết nối mạng và tình trạng dịch vụ tại thời điểm chạy. Kết quả thực tế có thể khác nhau.

## 4. Cấu hình kịch bản

| Thông số | Giá trị |
|---|---|
| Number of Threads (users) | 10 |
| Ramp-Up Period | 10 giây |
| Loop Count | 5 |
| HTTP Method | GET |
| Path | `/products/1` |
| Assertion | Response Code bằng `200` |
| Số request dự kiến | 50 |

Với một HTTP Request trong mỗi vòng lặp, số request dự kiến là 10 người dùng × 5 vòng lặp = 50 request. Đây là số lượng dự kiến theo cấu hình, không phải số liệu hiệu năng đo được.

## 5. Cấu trúc kịch bản

Kịch bản `JMeter_API_Test.jmx` bao gồm:
1. **Test Plan** – kế hoạch kiểm thử.
2. **Thread Group** – cấu hình người dùng ảo và số vòng lặp.
3. **HTTP Request** – gửi yêu cầu GET đến DummyJSON.
4. **Response Assertion** – kiểm tra mã phản hồi là 200.
5. **Summary Report** – tổng hợp các chỉ số.
6. **View Results Tree** – xem chi tiết từng phản hồi.

## 6. Hướng dẫn chạy

1. Cài đặt Java JDK và tải Apache JMeter từ trang chính thức.
2. Mở JMeter, chọn **File → Open** và mở `JMeter_API_Test.jmx`.
3. Kiểm tra cấu hình Thread Group và HTTP Request.
4. Nhấn **Start** để chạy kịch bản.
5. Xem **View Results Tree** để kiểm tra phản hồi và Assertion.
6. Xem **Summary Report** để ghi lại số liệu thực tế.
7. Chụp ảnh các màn hình và lưu vào thư mục `images/`.

Đối với bài kiểm thử tải lớn, nên chạy JMeter ở chế độ dòng lệnh thay vì GUI để giảm ảnh hưởng của giao diện đến kết quả đo.

## 7. Ảnh minh họa

Sau khi chạy kịch bản trên máy, thêm ảnh chụp thật vào thư mục `images/` với các tên sau. Các ảnh này cần được bổ sung trước khi nộp bài.

### 7.1. Cấu hình Thread Group

![Cấu hình Thread Group](images/01-thread-group.png)

### 7.2. Cấu hình HTTP Request

![Cấu hình HTTP Request](images/02-http-request.png)

### 7.3. Kết quả View Results Tree

![Kết quả View Results Tree](images/03-view-results-tree.png)

### 7.4. Summary Report

![Summary Report](images/04-summary-report.png)

## 8. Kết quả kiểm thử thực tế

Điền các giá trị từ Summary Report sau khi chạy kịch bản. Không điền số liệu ước lượng.

| Chỉ số | Kết quả |
|---|---|
| Samples | Chưa đo |
| Average (ms) | Chưa đo |
| Min (ms) | Chưa đo |
| Max (ms) | Chưa đo |
| Throughput | Chưa đo |
| Error % | Chưa đo |

### Nhận xét

- Số request thực tế: cập nhật theo Summary Report.
- Tình trạng phản hồi HTTP và Assertion: kiểm tra trong View Results Tree.
- Thời gian phản hồi và Throughput: đánh giá dựa trên số liệu đo được.
- Tỷ lệ lỗi: xác định nguyên nhân nếu có request thất bại.
- Kết luận: chỉ đưa ra sau khi chạy kiểm thử và xem kết quả thực tế.

## 9. Kết luận

Bài thực hành này giúp làm quen với quy trình xây dựng Test Plan, cấu hình người dùng ảo, gửi HTTP request, kiểm tra phản hồi và đọc báo cáo hiệu năng bằng Apache JMeter. Cần bổ sung ảnh chụp và số liệu thực tế sau khi chạy kịch bản để hoàn thiện báo cáo nộp bài.

## 10. Tài liệu tham khảo

- Apache JMeter: https://jmeter.apache.org/
- Getting Started: https://jmeter.apache.org/usermanual/get-started.html
- Building a Web Test Plan: https://jmeter.apache.org/usermanual/build-web-test-plan.html
- DummyJSON: https://dummyjson.com/
