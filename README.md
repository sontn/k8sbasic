# Giới thiệu về Kubernetes, khái niệm cơ bản và thực hành ngay trên trình duyệt Web
Trong xã hội kinh doanh hiện đại, tăng trưởng kinh doanh và tăng trưởng khách hàng là một mục tiêu tối thượng quan trọng nhất. Áp lực tăng trưởng và cạnh tranh khốc liệt khiến các công ty liên tục cải tiển sản phẩm, quy trình nhằm cung cấp nhiều dịch vụ, tính năng cho khách hàng và đồng thời luôn cố gắng cắt giảm chi phí. Đây chính là tiền đề của việc cải tiến, thay đổi công nghệ. Trong lĩnh vực công nghệ phần mềm, các kiến trúc thay đổi từ monolithic sang microservice, từ cồng kềnh to lớn sang chia nhỏ nhẹ nhàng linh hoạt hơn, nhằm đáp ứng nhu cầu phát triển rất nhanh của tổ chức. Nhưng việc này xuất hiện một bài toán mới cần giải quyết:
Chuẩn hóa cách thức chạy ứng dụng, nghĩa là ứng dụng chạy được mỗi nơi mà không bị phụ thuộc nhiều vào môi trường
Làm thế nào để triển khai ứng dụng nhanh
Làm thế nào để mở rộng (scale) ứng dụng để đáp ứng tải
Làm thế nào để quản lý tài nguyên phần cứng (CPU, RAM,…) hiệu quả …

Container và Kubernetes chính là lời giải cho những vấn đề trên. Container giúp chuẩn hóa ứng dụng, chiếm rất ít tài nguyên máy tính, giúp ứng dụng có thể chạy được nhiều môi trường miễn là môi trường đó được hỗ trợ Container, mà đây là một tính năng mặc định đã có từ lâu trên máy chủ chạy Linux. Docker là một công ty rất nổi tiếng đã đưa ra một chuẩn cho việc sử dụng Container. Còn Kubernetes giúp cho việc triển khai ứng dụng nhanh, scale nhanh, quản lý các container và quản lý tài nguyên phần cứng hiệu quả, giảm mạnh chi phí vận hành hạ tầng, nâng cao năng suất. Tiền thân của Kubernetes chính là Borg, là hệ thống nội bộ của Google dùng để quản lý, triển khai hàng nghìn ứng dụng của mình trên khắp các data center nằm ở mọi nơi trên thế giới. Năm 2014, Google đã đưa Kubernetes thành một dự án cộng đồng và trở nên rất nổi tiếng sau đó. Kubernetes hiện tại là một dự án mã nguồn mở trọng điểm viết bằng ngôn ngữ Golang của tổ chức Cloud Native Computing Foundation, bao gồm rất nhiều hãng và tập đoàn lớn cùng tham gia phát triển trong đó có Google, CoreOS, Mesosphere, Red Hat, Twitter, Huawei, Intel, Cisco, IBM, Docker, Univa, VMWare…
## Tóm tắt bài viết

* Kiến trúc của Kubernetes
* Cài đặt Kubernetes bằng Minikube
* iển khai một ứng dụng vào Kubernetes
* Khám phá thêm về ứng dụng vừa cài đặt
* Phơi ứng dụng bằng Service Kubernetes
* Mở rộng ứng dụng
* Nâng cấp phiên bản ứng dụng


# Kiến trúc của Kubernetes
Ở góc độ đơn giản nhất, Kubernetes gồm 2 thành phần chính đó là Master và Worker. Master là thành phần điều khiển các Worker, triển khai các ứng dụng trên Woker còn Worker là nơi ứng dụng chạy trên đó. Master và Worker có thể chạy trên máy chủ thật hoặc là máy chủ ảo (VM). Kubernetes có thể biến hàng nghìn máy chủ trong một data center thành một điểm triển khai ứng dụng đơn nhất. Các lập trình viên chỉ cần viết ứng dụng và các file mô tả ứng dụng của mình, ví dụ như ứng dụng này chạy đơn, hay chay đôi, hay chạy nhiều các bản sao của mình trên máy chủ sau đó đưa bản mô tả này cho Master, Master sẽ điều khiển các Worker để chạy các ứng dụng này.

![k8s models](https://raw.githubusercontent.com/sontn/k8sbasic/master/images/k8sgeneral1.PNG)

## Chi tiết hơn kiến trúc của Kubernetes
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
![k8s models](https://raw.githubusercontent.com/sontn/k8sbasic/master/images/k8sgeneral2.PNG)

# Thực hành Kubernetes

## Cài đặt Kubernetes bằng Minikube

Trong bài viết này, chúng tôi sử dụng chương trình Minikube, là chương trình thiết kế để giúp cho người mới tiếp cận được những khái niệm cơ bản nhất của Kubernetes. Minikube không được sử dụng cho môi trường chạy sản phẩm thật. Các bạn có thể tham khảo thêm bài viết này cho môi trường chạy thật.

https://medium.com/vinid/c%C3%A0i-%C4%91%E1%BA%B7t-kubernetes-k8s-high-availability-b%E1%BA%B1ng-kubeadm-cho-production-7f4b55f8722b

Chúng ta có thể tự cài Minikube trên máy tính, bản thân Minikube là một máy ảo trong đó cài sẵn Kubernetes, vì vậy máy tính phải cài đặt chương trình ảo hóa để hỗ trợ, ví dụ như KVM hoặc VirtualBox. Trong bài viết này chúng tôi sử dụng luôn trình duyệt để chạy lệnh. Chúng tôi sử dụng Katacoda, một công cụ giúp học công nghệ mới ngay trên trình duyệt, rất hữu ích để bắt đầu học vì chúng ta không cần phải cài đặt môi trường lên, Katacoda đã hỗ trợ việc đó. Hiện tại, Oreilly đã mua lại Katacoda.

**Các bạn nhấn vào link ở dưới đây để thực hành nhé**

https://kubernetes.io/docs/tutorials/kubernetes-basics/create-cluster/cluster-interactive/

Lệnh đầu tiên chúng ta cần sử dụng để bật được Minikube lên là

`minikube start`

Sau khi khởi động Minikube thành công, tiếp theo chúng ta sử dụng câu lệnh dưới thông tin tổng quát của cụm Kubernetes

`kubectl cluster-info`

Để xem thông tin của các máy Master và Node được tham gia vào cụm Kubernetes, chúng ta sử dụng lệnh

`kubectl get nodes`

Như vậy là chúng ta đã có những thao tác rất cơ bản vào Kubernetes thông qua Minikube.

## Triển khai một ứng dụng vào Kubernetes

Như đã nói ở trên, lập trình viên sẽ sử dụng các file mô tả ứng dụng, được viết dưới định dạng của file YAML, để từ đó triển khai ứng dụng. Tuy nhiên để đơn giản hóa, chúng ta có thể dùng lệnh trực tiếp để deploy ứng dụng vào cụm Kubernetes. Trong bài này, chúng tôi sử dụng ứng dụng mẫu từ Google, gcr.io/google-samples/kubernetes-bootcamp:v1 để triển khai.

**Các bạn nhấn vào link ở dưới đây để thực hành nhé**

https://kubernetes.io/docs/tutorials/kubernetes-basics/deploy-app/deploy-interactive/

Câu lệnh như sau:

`kubectl create deployment kubernetes-bootcamp --image=gcr.io/google-samples/kubernetes-bootcamp:v1`

Để kiểm tra ứng dụng đã được deploy thành công chưa, chúng ta sử dụng lệnh

`kubectl get deployments`

Để kiểm tra ứng dụng đã hoạt động chưa, chúng ta truy cập vào ứng dụng bằng cách mở thêm 1 terminal nữa, và gõ lệnh

`kubectl proxy`

Mục đích lệnh này sẽ tạo một đường ống tắt tới ứng dụng vừa deploy. Quay trở lại Terminal đầu, chúng ta gõ lệnh

`curl http://localhost:8001/version`

Nếu có dữ liệu JSON trả về tức là ứng dụng đã hoạt động.

## Khám phá thêm về ứng dụng vừa cài đặt
Trong Kubernetes, Pod là khái niệm cơ bản và quan trọng nhất. Bản thân Pod có thể chứa 1 hoặc nhiều hơn 1 container. Pod chính là nơi ứng dụng được chạy trong đó. Pod là các tiến trình nằm trên các Worker Node. Bản thân Pod có tài nguyên riêng về file system, cpu, ram, volumes, địa chỉ network…

**Các bạn nhấn vào link ở dưới đây để thực hành nhé**

https://kubernetes.io/docs/tutorials/kubernetes-basics/explore/explore-interactive/

Để liệt kê các pod, chúng ta sử dụng câu lệnh

`kubectl get pods`

Để xem chi tiết hơn các pods đang chạy, ví dụ như pod chạy image gì, có biến môi trường nào, có khai báo tài nguyên … chúng ta sử dụng câu lệnh

`kubectl describe pods`

Muốn xem logs của ứng dụng đang chạy, chúng ta sử dụng câu lệnh

`kubectl logs $POD_NAME`

trong đó $POD_NAME chính là tên pod của ứng dụng cần xem log

Đi sâu hơn vào trong ứng dụng, để chạy một lệnh nằm trên ứng dụng chúng ta sử dụng lệnh

`kubectl exec $POD_NAME env`

Để khởi tạo một bash shell của pod ứng dụng, chúng ta sử dụng lệnh

`kubectl exec -it $POD_NAME bash`

Lúc này trong ta đã đi vào bên trong ứng dụng, chúng ta kiểm tra ứng dụng đang chạy bằng câu lệnh

`curl localhost:8080`

Để thoát khỏi pod ứng dụng, chúng ta sử dụng lệnh

`exit`

## Phơi ứng dụng bằng Service Kubernetes

Bản thân Pod rất dễ bị dừng hoặc bị restart vì nhiều lý do. Do vậy IP của Pod cũng thay đổi liên tục. Các ứng dụng nếu kết nối với nhau bằng IP của Pod sẽ thiếu ổn định. Đó là lý do tính năng Service của Kubernetes ra đời, cho phép đại diện cho Pod bằng Service, Service có IP cố định, kết nối xuống Pod. Ngoài ra Service còn làm nhiệm vụ Load Balancing cho các ứng dụng có nhiểu bản sao, có nhiều Pod được nhân bản. Để kết nối được Service với Pod, Kubernetes dùng thêm khái niệm là Label và Selector. Label là cách đặt tên cho ứng dụng để cho người quản trị, lập trình dễ vận hành ứng dụng hơn trong môi trường có hàng trăm, hàng nghìn ứng dụng chạy trên Kubernetes. Selector là từ khóa sẽ dùng để móc nối Service vào Labels của Pod.

**Các bạn nhấn vào link ở dưới đây để thực hành nhé**

https://kubernetes.io/docs/tutorials/kubernetes-basics/expose/expose-interactive/

### Tạo Service
Kiểm tra ứng dụng đang chạy

`kubectl get pods`

Kiểm tra các Service đã được tạo

`kubectl get services`

Phơi ứng dụng Pod bằng Service thông qua lệnh

`kubectl expose deployment/kubernetes-bootcamp --type="NodePort" --port 8080`

Kiểm tra lại Service vừa tạo

`kubectl get services`

Xem chi tiết hơn về Service

`kubectl describe services/kubernetes-bootcamp`

Xem cổng (port) được public trên minikube

`export NODE_PORT=$(kubectl get services/kubernetes-bootcamp -o go-template='{{(index .spec.ports 0).nodePort}}')`

Sử dụng lệnh curl để kiểm tra xem việc sử dụng Service đã thành công chưa

`curl $(minikube ip):$NODE_PORT`

### Sử dụng Labels

Xem Labels của ứng dụng

`kubectl describe deployment`

Kiểm tra lại Labels trên Pod

`kubectl get pods -l run=kubernetes-bootcamp`

Kiểm tra lại Labels trên Service

`kubectl get services -l run=kubernetes-bootcamp`

Lấy tên Pod đang chạy

`export POD_NAME=$(kubectl get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}')`

Đánh Labels cho Pod

`kubectl label pod $POD_NAME app=v1`

Kiểm tra lại Labels đã đánh được chưa

`kubectl describe pods $POD_NAME`

hoặc bằng lệnh

`kubectl get pods -l app=v1`

### Xóa Service

Đôi khi chúng ta cũng cần phải xóa Service đi vì lý do tạo sai hoặc không cần dùng nữa. Chúng ta sử dụng câu lệnh

`kubectl delete service -l run=kubernetes-bootcamp`

Kiểm tra lại Service đã bị xóa

`kubectl get services`

## Mở rộng ứng dụng
Đây chính là một trong những phần rất hay của Kubernetes. Chúng ta sẽ scale ứng dụng bằng cách tác động vào Deployment của Kubernetes. Bản thân Deployment của Kubernetes sẽ gọi đến replicas controller để nhân bản hoặc co ứng dụng lại.

**Các bạn nhấn vào link ở dưới đây để thực hành nhé**

https://kubernetes.io/docs/tutorials/kubernetes-basics/scale/scale-interactive/

Kiểm tra ứng dụng đang chạy

`kubectl get deployments`

Ứng dụng đang chạy với 1 bản sao, giờ chúng ta scale ứng dụng lên thành 4 bản sao

`kubectl scale deployments/kubernetes-bootcamp --replicas=4`

Kiểm tra lại bằng câu lệnh

`kubectl get pods -owide`

Như vậy là chúng ta scale được thành công. Như đã nói ở trên, Service có chức năng Load Balancer của Kubernetes, chúng ta sẽ thử xem Service có hoạt động không. Kiểm tra lại Service

`kubectl describe services/kubernetes-bootcamp`

Kiểm tra giá trị NODE_PORT

`export NODE_PORT=$(kubectl get services/kubernetes-bootcamp -o go-template='{{(index .spec.ports 0).nodePort}}')`

Thử tạo nhiều request, chúng ta sẽ thấy nhiều kết quả khác nhau. Đó chính là do Service đã load balancing giữa các Pod

`curl $(minikube ip):$NODE_PORT`
### Co nhỏ ứng dụng

Để giảm các bản sao của ứng dụng, chúng ta chỉ cần đưa điều chỉnh số replicas là xong

`kubectl scale deployments/kubernetes-bootcamp --replicas=2`

Kiểm tra lại Pod

`kubectl get pod -owide`

## Nâng cấp phiên bản ứng dụng
Đây là một trong những tính năng tiêu biểu của Kubernetes. Với thành phần Deployment, Kubernetes sẽ hỗ trợ việc nâng cấp phiên bản mà không ảnh hưởng nhiều tới trải nghiệm người dùng. Phiên bản mới sẽ thay thế dần phiên bản cũ cho đến khi thay thế toàn bộ thì phiên bản cũ sẽ dừng và ứng dụng sẽ chạy hoàn toàn với phiên bản mới. Trong trường hợp việc nâng cấp bị lỗi, Kubernetes có thể roll back lại phiên bản chạy ổn định phía trước.

**Các bạn nhấn vào link ở dưới đây để thực hành nhé**

https://kubernetes.io/docs/tutorials/kubernetes-basics/update/update-interactive/

Kiểm tra ứng dụng hiện tại đang chạy bằng Deployment

`kubectl get deployments`

Kiểm tra ứng dụng đang chạy bằng cách liệt kê Pod

`kubectl get pods`

Xem phiên bản của ứng dụng

`kubectl describe pods`

Update phiên bản ứng dụng

`kubectl set image deployments/kubernetes-bootcamp kubernetes-bootcamp=jocatalin/kubernetes-bootcamp:v2`

Kiểm tra lại ứng dụng có chạy hay không

`kubectl get pods`

### Xác nhận lại việc cập nhật ứng dụng

Kiểm tra lại Service để xem thông tin NODE_PORT

`kubectl describe services/kubernetes-bootcamp`

Lưu giá trị của NODE_PORT

`export NODE_PORT=$(kubectl get services/kubernetes-bootcamp -o go-template='{{(index .spec.ports 0).nodePort}}')`

Thử tạo request xem ứng dụng có trả về thông tin của phiên bản mới

`curl $(minikube ip):$NODE_PORT`

Nếu thấy v2, tức là ứng dụng đã được cập nhật phiên bản mới thành công

Kiểm tra lại phiên bản ứng dụng một lần nữa

`kubectl describe pods`

### Quay trở lại phiên bản cũ nếu cập nhật phiên bản mới không thành công

Kịch bản bài này là ứng dụng chúng ta đang cần nâng cấp ứng dụng lên phiên bản mới, nhưng việc nay cấp này bì thất bại nên chúng ta sẽ quay lại phiên bản đang chạy tốt trước khi nâng cấp

Nâng cấp phiên bản bằng lệnh

`kubectl set image deployments/kubernetes-bootcamp kubernetes-bootcamp=gcr.io/google-samples/kubernetes-bootcamp:v10`

Kiểm tra việc nâng cấp bằng lệnh

`kubectl get deployments`

và lệnh

`kubectl get pods`

Chúng ta thấy việc nâng cấp bị thất bại do image của ứng dụng bị lỗi

Xem kỹ thông tin lỗi hơn bằng lệnh

`kubectl describe pods`

Giờ chúng ta sẽ thực hiện câu lệnh roll back để quay về phiên bản chạy ổn định

`kubectl rollout undo deployments/kubernetes-bootcamp`

Kiểm tra lại ứng dụng đã roll back thành công hay chưa bằng lệnh

`kubectl get deployments`

hoặc

`kubectl get pods`

# Kết luận
Như vậy chúng ta đã đi xuyên suốt từ đầu đến cuối về các khái niệm cơ bản nhất, kiến trúc cơ bản nhất và những bài thực hành cơ bản nhất về Kubernetes. Container và Kubernetes là những công nghệ giúp nâng cao hiệu quả hoạt động kinh doanh của tổ chức, giúp tổ chức triển khai nhanh ứng dụng của mình cho khách hàng và giảm chi phí vận hành. Việc nắm rõ những thành phần cơ bản và các tác vụ hay sử dụng trên Kubernetes sẽ giúp lập trình viên, quản trị viên cũng như người làm chiến lược về công nghệ thông tin tự tin chọn và áp dụng Kubernetes như là một nền tảng công nghệ không thể thiếu trong hạ tầng ứng dụng công nghệ thông tin của mình.

**Nếu các bạn thấy bài viết hữu ích, hãy like và share nhé. Cảm ơn các bạn nhiều!**
