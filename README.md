# Giới thiệu về Kubernetes, khái niệm cơ bản và thực hành ngay trên trình duyệt Web
Trong xã hội kinh doanh hiện đại, tăng trưởng kinh doanh và tăng trưởng khách hàng là một mục tiêu tối thượng quan trọng nhất. Áp lực tăng trưởng và cạnh tranh khốc liệt khiến các công ty liên tục cải tiển sản phẩm, quy trình nhằm cung cấp nhiều dịch vụ, tính năng cho khách hàng và đồng thời luôn cố gắng cắt giảm chi phí. Đây chính là tiền đề của việc cải tiến, thay đổi công nghệ. Trong lĩnh vực công nghệ phần mềm, các kiến trúc thay đổi từ monolithic sang microservice, từ cồng kềnh to lớn sang chia nhỏ nhẹ nhàng linh hoạt hơn, nhằm đáp ứng nhu cầu phát triển rất nhanh của tổ chức. Nhưng việc này xuất hiện một bài toán mới cần giải quyết:
Chuẩn hóa cách thức chạy ứng dụng, nghĩa là ứng dụng chạy được mỗi nơi mà không bị phụ thuộc nhiều vào môi trường
Làm thế nào để triển khai ứng dụng nhanh
Làm thế nào để mở rộng (scale) ứng dụng để đáp ứng tải
Làm thế nào để quản lý tài nguyên phần cứng (CPU, RAM,…) hiệu quả …

Container và Kubernetes chính là lời giải cho những vấn đề trên. Container giúp chuẩn hóa ứng dụng, chiếm rất ít tài nguyên máy tính, giúp ứng dụng có thể chạy được nhiều môi trường miễn là môi trường đó được hỗ trợ Container, mà đây là một tính năng mặc định đã có từ lâu trên máy chủ chạy Linux. Docker là một công ty rất nổi tiếng đã đưa ra một chuẩn cho việc sử dụng Container. Còn Kubernetes giúp cho việc triển khai ứng dụng nhanh, scale nhanh, quản lý các container và quản lý tài nguyên phần cứng hiệu quả, giảm mạnh chi phí vận hành hạ tầng, nâng cao năng suất. Tiền thân của Kubernetes chính là Borg, là hệ thống nội bộ của Google dùng để quản lý, triển khai hàng nghìn ứng dụng của mình trên khắp các data center nằm ở mọi nơi trên thế giới. Năm 2014, Google đã đưa Kubernetes thành một dự án cộng đồng và trở nên rất nổi tiếng sau đó. Kubernetes hiện tại là một dự án mã nguồn mở trọng điểm viết bằng ngôn ngữ Golang của tổ chức Cloud Native Computing Foundation, bao gồm rất nhiều hãng và tập đoàn lớn cùng tham gia phát triển trong đó có Google, CoreOS, Mesosphere, Red Hat, Twitter, Huawei, Intel, Cisco, IBM, Docker, Univa, VMWare…
Tóm tắt bài viết
Kiến trúc của Kubernetes
Cài đặt Kubernetes bằng Minikube
Triển khai một ứng dụng vào Kubernetes
Khám phá thêm về ứng dụng vừa cài đặt
Phơi ứng dụng bằng Service Kubernetes
Mở rộng ứng dụng
Nâng cấp phiên bản ứng dụng

Kiến trúc của Kubernetes
Ở góc độ đơn giản nhất, Kubernetes gồm 2 thành phần chính đó là Master và Worker. Master là thành phần điều khiển các Worker, triển khai các ứng dụng trên Woker còn Worker là nơi ứng dụng chạy trên đó. Master và Worker có thể chạy trên máy chủ thật hoặc là máy chủ ảo (VM). Kubernetes có thể biến hàng nghìn máy chủ trong một data center thành một điểm triển khai ứng dụng đơn nhất. Các lập trình viên chỉ cần viết ứng dụng và các file mô tả ứng dụng của mình, ví dụ như ứng dụng này chạy đơn, hay chay đôi, hay chạy nhiều các bản sao của mình trên máy chủ sau đó đưa bản mô tả này cho Master, Master sẽ điều khiển các Worker để chạy các ứng dụng này.

![k8s models](https://raw.githubusercontent.com/sontn/k8sbasic/master/images/k8sgeneral1.PNG)
Chi tiết hơn kiến trúc của Kubernetes
Master node bao gồm 4 thành phần chính:

* Kubernetes API Server: là thành phần giúp các thành phần khác liên lạc nói chuyện với nhau. Lập trình viên khi triển khai ứng dụng sẽ gọi API Kubernetes API Server này.
* Scheduler: Thành phần này lập lịch triển khai cho các ứng dụng, ưng dụng được đặt vào Worker nào để chạy
* Controler Manager: Thành phần đảm nhiệm phần quản lý các Worker, kiểm tra các Worker sống hay chết, đảm nhận việc nhân bản ứng dụng…
* Etcd: Đây là cơ sở dữ liệu của Kubernetes, tất cả các thông tin của Kubernetes được lưu trữ cố định vào đây.

Worker node bao gồm 3 thành phần chính:

* Container runtime: Là thành phần giúp chạy các ứng dụng dưới dạng Container. Thông thường người ta sử dụng Docker.
* Kubelet: đây là thành phần giao tiếp với Kubernetes API Server, và cũng quản lý các container
* Kubernetes Service Proxy: Thành phần này đảm nhận việc phân tải giữa các ứng dụng

Một thành phần quan trọng cuối cùng, công cụ này không nằm trong cụm Kubernetes, nó nằm ở phía ngoài, tên là kubectl, là công cụ cho phép các lập trình viên đẩy các ứng dụng mô tả triển khai vào cụm Kubernetes, cũng như là cho phép các quản trị viên có thể quản trị được cụm Kubernetes.
