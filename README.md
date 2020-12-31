# LAPTOP_PRICE_PREDICTION

## Đồ án cuối kỳ môn Khoa học Dữ liệu

#### Giáo viên: Hoàng Xuân Trường

### Đề tài: Dự đoán giá laptop

---

## 1. Thông tin nhóm 

| Tên  |MSSV|
|-|-:|
| Đặng Thái Gia Thuận |1712173|
| Lê Kinh Luân |1612355|

## 2. Thông tin đồ án

#### 2.1. Câu hỏi được đặt ra là gì?
- Dựa vào các thuộc tính, đặc trưng cơ bản của laptop như CPU, RAM, kích thước,… để dự đoán giá của sản phẩm trên thị trường.
- Đầu vào của bài toán là tập các thuộc tính, đặc trưng cơ bản của laptop
- Đầu ra của bài toán là giá trị dự đoán được từ những thuộc tính, đặc trưng ở đầu vào.
- Bài toán sẽ được giải quyết bằng phương pháp Linear Regressin (hồi quy tuyến tính).
#### 2.2. Nếu trả lời được câu hỏi thì có ý nghĩa gì?
Giải quyết bài toàn này sẽ giúp cho người dùng dễ dàng thẩm định được giá trị của laptop, cùng với đó lời giải của bài toán cung cấp cho chúng ta mối quan hệ giữa các đặc trưng của laptop đến giá cả của nó trên thị trường.
#### 2.3. Cách thức thu nhập dữ liệu như thế nào? (từ đâu, parse HTML hay API, tham khảo từ đâu,...) 
* Nguồn dữ liệu về các thuộc tính, đặc trưng của laptop được lấy từ trang web thương mại của FPTShop (https://fptshop.com.vn). 
* Môi trường dùng để thu thập dữ liệu bao gồm:
  * Ngôn ngữ lập trình Python 
  * Thư viện thu thập dữ liệu Requests
  * Thư viện xử lý dữ liệu Pandas, bs4
* Dữ liệu được thu thập bằng việc gọi API:
  * Thu thập tên, giá, đường dẫn của các sản phẩm laptop: 
    * Đường dẫn: “https://fptshop.com.vn/apiFPTShop/Product/GetProductList?brandAscii=&url=https:%2F%2Ffptshop.com.vn%2Fmay-tinh-xach-tay%3Ftrang%3D” + i
    * Trong đó i là số trang (i từ 1 đến 10)
  * Thu thập các thuộc tính, đặc trưng của laptop: 
    * Đường dẫn: “https://fptshop.com.vn/apiFPTShop/Product/GetProductDetail?name=” + url
    * Trong đó url là đường dẫn đến trang sản phẩm.
#### 2.4. Tổng quan dữ liệu thu nhập được có bao nhiêu dòng, cột và cột cần dự đoán là gì. 
- Dữ liệu sau khi thu thập được gồm 224 hồ sơ (records) về laptop. Sau khi lọc bỏ những dữ liệu thiếu (NaN), dữ liệu còn lại bao gồm 185 hồ sơ.
- Mỗi hồ sơ bao gồm 115 trường dữ liệu về thuộc tính và đặc trưng của sản phẩm. Trong đó, bao gồm 1 cột dữ liệu về giá (price) cần được dự đoán, một cột về tên của sản phẩm, một cột chứa đường dẫn sản phẩm, các cột còn lại là thuộc tính, đặc trưng của sản phẩm.
- Tuy nhiên dữ liệu chưa hoàn toàn sạch. Trong dữ liệu tồn tại các giá trị “đang cập nhật” thể hiện một giá trị chưa biết của giá trị đó. Với từng kiểu dữ liệu, cần một phương pháp xử lý phù hợp để thay thế giá trị này bằng giá trị khác.
#### 2.5. Với mỗi cột dữ liệu thu thập có ý nghĩa gì, kiểu dữ liệu và ví dụ.
- Về mặt ý nghĩa các cột có thể chia thành:
  + CPU: 
    - Gồm các trường dữ liệu: Tốc độ CPU, CPU, Tốc độ BUS, Loại CPU, Tốc độ tối đa, Hãng CPU, Bộ nhớ đệm, Tên đầy đủ (CPU), Số luồng, Số lõi.
    - Đây là trường dữ liệu mô tả các thông số kĩ thuật của CPU, trường “Tên đầy đủ (CPU)” có thể đại diện cho nhóm này.
 
|    <!-- -->       | <!-- --> |
|-------------------|----------|
| **Tốc độ CPU**    | 2.30 GHz |
| **CPU**           | Ryzen 7  |
| **Tốc độ BUS**    | 8 GT/s   |
| **Loại CPU**      | 3750H    |
| **Tốc độ tối đa** | 4.00 GHz |
- Ví dụ: Với laptop “Acer Aspire Gaming A715-41G-R150/R7-3750H”, có các thông số kĩ thuật sau:
  + RAM: 
    - Gồm các trường dữ liệu: RAM, Loại RAM, Số khe cắm rời, Tốc độ RAM, Dung lượng nâng cấp tối đa, Số RAM onboard, Tên đầy đủ (RAM), Số khe RAM còn lại.
    - Đây là trường dữ liệu mô tả các thông số kĩ thuật của RAM, trường "Tên đầy đủ (RAM)" có thể đại diện cho nhóm này.
    
|     <!-- -->       | <!-- --> |               <!-- -->         |       <!-- -->     |
|--------------------|----------|--------------------------------|--------------------|
| **RAM**            |   8 GB   | **Số RAM onboard**             |          0         |
| **Loại RAM**       |   DDR4   | **Tên đầy đủ (RAM)**           | 8 GB DDR4 2666 MHz |
| **Số khe cắm rời** |    2     | **Số khe RAM còn lại**         |          1         |
| **Tốc độ RAM**     | 2933 MHz | **Dung lượng nâng cấp tối đa** |        32 GB       |
- Ví dụ với laptop “Asus TUF Gaming FX506LI-HN039T/i5-10300H”, có các thông số kĩ thuật sau:
  + Màn hình
    - Gồm các trường dữ liệu: Màn hình cảm ứng, Công nghệ màn hình, Màn hình, Độ phân giải, Tần số quét, Loại cảm ứng, Tấm nền, Tên đầy đủ (Màn hình), Độ tương phản, Độ sáng, Tỷ lệ màn hình, Độ phủ màu, Kiểu màn hình.
    - Đây là trường dữ liệu mô tả các thông số kĩ thuật của màn hình, trường “Tên đầy đủ (Màn hình)” có thể đại diện cho nhóm này.
    
|          <!-- -->         |                      <!-- -->                   |      <!-- -->     | <!-- --> |
|---------------------------|-------------------------------------------------|-------------------|----------|
| **Màn hình cảm ứng**      |                        Không                    | **Loại cảm ứng**  |   Không  |
| **Công nghệ màn hình**    |                     LED Backlit                 | **Tấm nền**       |    IPS   |
| **Màn hình**              |                        15.6"                    | **Kiểu màn hình** |    LED   |
| **Độ phân giải**          |                  1920 x 1080 Pixel              | **Độ tương phản** |  600:1   |
| **Tần số quét**           |                        60 Hz                    | **Độ sáng)**      | 250 nits |
| **Tỷ lệ màn hình**        |                        16:09                    | **Độ phủ màu**    | 45% NTSC |
| **Tên đầy đủ (Màn hình)** |            "15.6"" Anti-glare LED-backlit"      |                   |          | 

- Ví dụ với laptop “Asus TUF Gaming FX506LI-HN039T/i5-10300H”, có các thông số kĩ thuật sau: 
  + Đồ hoạ
    - Gồm các trường dữ liệu: Card đồ họa, Card rời, Có Card rời, Có Card onbroad, Hãng (Card onbroad), Model (Card onbroad), Model (Card rời), Xung nhịp tối đa (Card onbroad), Xung nhịp (Card onbroad), Đồ họa, Xung nhịp (Card rời), Xung nhịp tối đa (Card rời), Card onbroad, Bộ nhớ đồ họa (Card rời), Kiểu bộ nhớ, Hãng (Card rời), Tên đầy đủ (Card rời), Tổng số khe cắm SSD/HDD, Tốc độ đọc/ghi, Tốc độ quay HDD, Có SSD, Số khe SSD/HDD còn lại, Dung Lượng HDD, Ổ cứng, Dung lượng SSD, Kiểu HDD, Chuẩn M2, Kiểu SSD,Có HDD. 
    - Đây là trường dữ liệu mô tả các thông số kĩ thuật của card đồ hoạ. “Model (Card onbroad)” và “Model (Card rời)” là hai trường dữ liệu đại diện cho nhóm này.
  + Khác
    - Gồm các trường dữ liệu: Hỗ trợ công nghệ Optane, Khe đọc thẻ nhớ, Bluetooth, Số cổng Gigabit Ethernet, Số cổng HDMI, Số cổng VGA, Wireless, Số cổng Type-C, Số cổng USB 2.0, Webcame, Số cổng 3.5 mm headphone/speaker jack, Số cổng DC-in jack, Số cổng USB 3.0, Số cổng Thunderbolt, Số cổng Card reader, Senser, Công nghệ âm thanh, Số Loa, Nhận diện khuôn mặt, Mở khóa vân tay (Finger Print), Mật khẩu, Ổ đĩa quang, Loại ổ đĩa quang, Bàn phím số, Công nghệ đèn bàn phím, Kiểu bàn phím, TouchPad, Loại đèn bàn phím, Màu đèn LED, PIN có thể tháo rời, Loại PIN, PIN (Cell), Power Supply, Type, Version, Hệ điều hành, Chất liệu mặt bên ngoài cùng, Chất liệu khung màn hình, Width (mm), Tản nhiệt, Height (mm), Bản lề (Hinge / Kickstand), Trọng lượng (kg), Chất liệu mặt lưng, Kích thước (mm), Chất liệu mặt bàn phím + kê tay,Model Series, PN, Thời gian bảo hành, Xuất xứ,Năm ra mắt,Phụ kiện kèm theo (bút / bàn phím/ cáp chuyển…).
    - Đây là các trường dữ liệu đại diện mô tả các thiết bị ngoại vi và bảo mật của laptop.
- Về mặt giá trị các cột dữ liệu có thể chia thành:
  - Các trường dữ liệu có giá trị là giá trị và đơn vị: các trường dữ liệu này cần được tiền xử lý bằng phương pháp loại bỏ đơn vị, giữ lại giá trị của trường dữ liệu này. Với các giá trị “đang cập nhật”, giá trị trung bình của tập các giá trị còn lại sẽ được dùng nhằm thay thế.
    - Ví dụ với laptop “Asus TUF Gaming FX506LI-HN039T/i5-10300H” có trường “RAM” có giá trị là “8 GB”. Sau khi tiền xử lý, trường “RAM” sẽ chỉ còn giá trị là 8.
  - Các trường dữ liệu dạng có không: Với các trường dữ liệu, các giá trị “có” sẽ được chuyển thành  giá trị 1, các giá trị “không” sẽ được chuyển thành giá trị 0. Các giá trị “đang cập nhật” sẽ được thay thế bằng giá trị 0 hoặc 1 tuỳ vào giá trị có số lượng nhiều nhất.
    - Ví dụ với laptop “Asus TUF Gaming FX506LI-HN039T/i5-10300H” có trường dữ liệu “Có card onboard” nhận giá trị là “Có”. Sau khi tiền xử lý giá trị của trường này là 1.
  - Các trường dữ liệu có giá trị là một mã số kĩ thuật. Để xử lý các giá trị này, chúng tôi sẽ sử dụng một thang đo chuẩn để đánh giá lại giá trị này bằng 1 giá trị khác phù hợp hơn.
    - Ví dụ với laptop “Asus TUF Gaming FX506LI-HN039T/i5-10300H” có trường dữ liệu “Tên đầy đủ CPU” nhận giá trị là “Intel Core i5-10300H”. Chúng tôi dựa vào thang điểm trên trang “https://www.cpubenchmark.net” để thay thế giá trị 8854 cho giá trị cũ.
    
#### 2.6. Tự đánh giá đồ án (kết quả, thiếu sót, cần làm gì phát triển thêm,..).
- Do các đặc trưng sản phẩm quá nhiều (hơn 100 trường dữ liệu), điều này là một thử thách rất lớn cho tiến trình tiền xử lý dữ liệu. Nên chúng tôi sẽ giữ lại 25 giá trị cột dữ liệu đầu tiên để tiến hành xử lý các giá trị đặc trưng thành một vector đặc trưng của từng sản phẩm. Các đặc trưng cụ thể mà chúng tôi sử dụng là: Tên đầy đủ (CPU), Số luồng, Số lõi, RAM, Số khe cắm rời, Tốc độ RAM, Dung lượng nâng cấp tối đa, Số RAM onboard, Số khe RAM còn lại, Màn hình, Độ phân giải, Tần số quét, Độ sáng, Có Card rời, Có Card onbroad, Xung nhịp tối đa (Card onbroad), Bộ nhớ đồ họa (Card rời), Tổng số khe cắm SSD/HDD, Có SSD, Số khe SSD/HDD còn lại, Có HDD, Số cổng Gigabit Ethernet, Số cổng HDMI, Số cổng VGA
- Mình sẽ phân chia bộ dữ liệu này thành 2 nhóm train và test nhờ vào hàm train_test_split của thư viện sklearn với tỉ lệ tập test là: 0.2. Sau khi nhiều lần chạy thử nghiệm, kết quả trung bình theo đánh giá rmse là: 2824189.5807544095
- Đây là một kết quả có thể chấp nhận được.
- Để cải thiện kết quả của mô hình, thì chúng tôi cần:
  + Chọn lọc các trường dữ liệu sử dụng để xây dựng vector đặc trưng phù hợp hơn.
  + Sử dụng các thang đo có độ chính xác cao hơn.
  + Cải thiện việc xử lý các dữ liệu bị thiếu hoặc “đang cập nhật”.
  + Thu thập thêm nhiều dữ liệu để huấn luyện và kiểm thử.
  
#### 2.7. Phân công công việc. 
|         **Giai đoạn**         |           **Người thực hiện**            |      **Công việc**     | **Kết quả** |
|---------------------------|-------------------------------------------------|-------------------|----------|
| Chọn đề tài      |                       Thuận                    | **Loại cảm ứng**  |   Không  |
|  <!-- -->    |                    Luân                 | **Tấm nền**       |    IPS   |
| **Màn hình**              |                        15.6"                    | **Kiểu màn hình** |    LED   |
| **Độ phân giải**          |                  1920 x 1080 Pixel              | **Độ tương phản** |  600:1   |
| **Tần số quét**           |                        60 Hz                    | **Độ sáng)**      | 250 nits |
| **Tỷ lệ màn hình**        |                        16:09                    | **Độ phủ màu**    | 45% NTSC |
| **Tên đầy đủ (Màn hình)** |            "15.6"" Anti-glare LED-backlit"      |                   |          | 
#### 2.8. Hướng dẫn chạy các file notebook (tất cả các quy trình, cả code thu nhập dữ liệu) 
Em đã đính kèm theo file notebook: 
