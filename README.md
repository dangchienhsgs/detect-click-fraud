## Xác định fraud click bằng mô hình lai

### Lý thuyết

  1. Cách tạo fraud clicks
  
  - Sử dụng  botnets (Do malware chạy nền trong máy tính và tự động click vào quảng cáo)
  
  - Chèn quảng cáo ẩn dưới dạng khác để lừa người dùng click
  
  - Trả tiền cho nhân công để click
  
  2. Cách tiếp cận
  
  Để có thể đối phó với nhiều loại lừa đảo, ta phải xác định được các thuộc tính của publisher dựa vào thống kê các chỉ số trung bình, độ lệch chuẩn, số lượng từ các lượt xem khác nhau và các thời gian khác nhau của publisher. Với các thuộc tính trên sẽ xác định được các thuộc tính tách bạch nhất để xây dựng bộ phân loại hiệu quả. Ta tách thuộc tính publisher theo các cách sau.

### Phương pháp tách thuộc tính của publisher

  1. Thống kê IP
  
    Đơn giản nhất click lừa đảo có thể đến thử các quảng cáo trên cùng địa chỉ IP. Để chắc chắn hơn thì ta nên thống kê theo IP của subnetwork (IP của subnetwork thu được bằng cách làm tròn giá trị IP/1000). Ta thống kê ra các chỉ số của 1 IP như sau:

    - ***avg_ip_sec***: Trung bình số lượt truy cập trong 1s
    - ***std_ip_sec***: Độ lệch chuẩn của trung bình lượt truy cập trong 1s
    - ***count_ip_sec***: Tổng số lượt truy cập (> 2) trong 1s.
    Tương tự với đơn vị thời gian phút, giờ, ngày. Ta có thêm 9 thuộc tính tương tự như thế.

  2. Thống kê theo User Agent

    Một số publisher lừa đảo có thể dùng các địa chỉ IP khác nhau truy cập vào các thời gian khác nhau nhưng chung 1 User Agent. Ví dụ chỉ có 1 loại User Agent truy cập vào publisher trong suốt 1 quãng thời gian, điều này rất đáng ngờ. Để bắt được loại click này, ta sắp xếp click theo thứ thự thời gian ưu tiên trước, rồi đến user agent. Sau khi sắp xếp, với mỗi dòng, nếu user agent dòng tiếp theo giống hệt dòng đó thì ta xóa dòng tiếp theo đi. Từ đó ta có được tỉ lệ của số lượng dòng sau khi xóa so với số lượng dòng ban đầu với mỗi publisher. Thuộc tính này gọi là thuộc tính tuần tự - đặt tên là ***agent1***. Giá trị ví dụ (agent1=8/11). Mặc khác ta sắp xếp các dòng ban đầu chỉ theo user agent và thực hiện cách xóa tương tự, thu được chỉ số ***agent2*** có cách tính như ***agent1***

  3. Thống kê theo campaign

    Có một số click có thể là lừa đảo nếu nó truy cập vào cùng 1 campaign với cùng địa chỉ IP và User Agent. Điều này rất đáng ngờ. Ta tìm ra các thuộc tính theo campaign như ***avg_cid_sec**, ***std_cid_sec***, ***count_cid_sec***,... tương tự như với địa chỉ IP.

  4. Thống kê theo Agent + IP

    Do với một địa chỉ IP, có thể có nhiều người được thuê click (clickers) tại các thời điểm khác nhau, vì vậy IP không thể dùng để xác định một clicker nào đó. Nhưng tổ hợp IP + Agent có thể dùng để xác định một clicker (Ví dụ clicker này thích dùng Chrome, clicker kia dùng Safari), nên tuy cùng 1 IP nhưng ta có thể phân tách theo Agent để xác định riêng rẽ từng clicker. Tương tự ta tính y như IP và Campaign, chỉ khác là thống kê theo IP + Agent để suy ra được các chỉ số ***avg_ipagent_sec***, ***std_ipagent_sec***, ...

  5. Lọc thuộc tính

    Sử dụng thuật toán Backward Feature Elimination, ý tưởng là xây dựng cây quyết định, các điểm cuối cùng của cây là các thuộc tính không ảnh hưởng nhiều đến kết quả, các thuộc tính này có thể được loại bỏ không xét đến.

### Mô hình Lấy mẫu 1 tập validation trong tập training

  1. Training data với bộ training sau khi đã bỏ đi tập validation
     Tối ưu hóa hệ số trên validation error

  2. Thêm tập validation vào trainning set

  3. Trainning lại trên tập đã được thêm

  4. Predict và Test

### Các thuật toán có thể sử dụng trong mô hình

  Với thuật toán phân loại hỗ trợ validation có rất nhiều. Với bộ data trên, ta có kết quả tham khảo của các thuật toán sau:


  | Thuật toán          |  KQ   |
  | ------------------- |:-----:|
  | FT Tree             | 36.3% |
  | RandomForest        | 47.7% |
  | REPTree             | 35.8% |
  | LADTree             | 37%   |
  | NBTree              | 37.9% |
  | RandomsubSpace      | 38.9% |
  | RotationForest      | 42.9% |
  | BayesNet            | 33.7% |
  | TreeEnsemble        | 49.3% |
  | RPROP               | 48.3% |
  | Blending            | 52.3% |
