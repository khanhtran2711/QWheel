Dưới đây là một **bài giảng tổng hợp về REST** theo hướng dễ học, dễ nhớ, nhưng vẫn đủ sâu để bạn dùng cho học tập hoặc thuyết trình.

---

# BÀI GIẢNG VỀ REST

## 1. REST là gì?

**REST** là viết tắt của **Representational State Transfer**.
Đây là **một phong cách kiến trúc** dùng để thiết kế hệ thống phân tán, đặc biệt phổ biến trong việc xây dựng **Web API**.

Nói đơn giản hơn:

> REST là cách tổ chức giao tiếp giữa **client** và **server** sao cho rõ ràng, dễ mở rộng, dễ bảo trì và hoạt động tốt trên Internet.

Ví dụ:

* trình duyệt web gọi API lấy danh sách sản phẩm
* app điện thoại gọi API lấy thông tin người dùng
* frontend React gọi backend ASP.NET/Node.js/Java để lấy dữ liệu

Những kiểu giao tiếp này thường dùng REST hoặc RESTful API.

---

# 2. Ý tưởng cốt lõi của REST

REST xem mọi thứ trong hệ thống như **resource**.

Ví dụ:

* người dùng là một resource
* sản phẩm là một resource
* bài viết là một resource
* đơn hàng là một resource

Mỗi resource thường có một địa chỉ riêng gọi là **URI**.

Ví dụ:

```http
/users/12
/products/5
/orders/1001
/articles/abc
```

Client không gọi server kiểu “làm cái này cho tôi” một cách tùy tiện, mà thường sẽ thao tác lên resource bằng các HTTP method như:

* `GET` → lấy dữ liệu
* `POST` → tạo mới
* `PUT` → cập nhật toàn bộ
* `PATCH` → cập nhật một phần
* `DELETE` → xóa

---

# 3. Ví dụ rất cơ bản về REST

Giả sử có hệ thống bán hàng.

## Lấy danh sách sản phẩm

```http
GET /products
```

## Lấy chi tiết 1 sản phẩm

```http
GET /products/15
```

## Tạo sản phẩm mới

```http
POST /products
```

## Cập nhật sản phẩm

```http
PUT /products/15
```

## Xóa sản phẩm

```http
DELETE /products/15
```

Đó là tư duy REST:
**resource + HTTP method + representation dữ liệu**

---

# 4. “Representation” trong REST là gì?

Client thường không làm việc trực tiếp với “resource thật” bên trong database.
Thay vào đó, server gửi về một **representation** của resource, thường là:

* JSON
* XML
* đôi khi là HTML

Ví dụ sản phẩm trong database có thể được server biểu diễn ra JSON như sau:

```json
{
  "id": 15,
  "name": "Laptop",
  "price": 20000000
}
```

Đây là “representation” của resource `product`.

---

# 5. 6 ràng buộc của REST

Muốn một hệ thống được xem là REST đúng nghĩa, nó phải tuân theo các ràng buộc sau.

## 5.1 Client-Server

### Khái niệm

REST yêu cầu tách biệt:

* **Client**: phía giao diện, gửi request, hiển thị dữ liệu
* **Server**: phía xử lý nghiệp vụ, quản lý dữ liệu

### Mục đích

Tách trách nhiệm để:

* dễ phát triển
* dễ thay đổi
* frontend và backend độc lập hơn

### Ví dụ

* React/Vue/Angular là client
* ASP.NET Core / Node.js / Spring Boot là server

Client chỉ cần biết:

* gọi API nào
* nhận dữ liệu gì

Server chỉ cần quan tâm:

* xử lý logic
* truy vấn database
* trả kết quả

### Lợi ích

* frontend thay đổi không ảnh hưởng mạnh đến backend
* backend có thể phục vụ nhiều loại client:

  * web
  * mobile
  * desktop
  * hệ thống bên thứ ba

---

## 5.2 Stateless

### Khái niệm

**Stateless** nghĩa là:

> server không lưu trạng thái phiên làm việc của client giữa các request.

Mỗi request phải chứa **đủ thông tin** để server hiểu và xử lý.

### Ví dụ

Nếu user đã đăng nhập, request sau phải tự mang theo:

* token
* cookie phiên
* thông tin xác thực cần thiết

Server không nên phụ thuộc vào “nhớ” request trước đó.

### Ví dụ thực tế

Request 1:

```http
POST /login
```

Server trả token.

Request 2:

```http
GET /profile
Authorization: Bearer abcxyz
```

Request 3:

```http
GET /orders
Authorization: Bearer abcxyz
```

Mỗi request đều tự mang token.

### Vì sao quan trọng?

* dễ scale ngang
* request nào vào server nào cũng xử lý được
* giảm phụ thuộc vào session memory ở server

### Lợi ích

* tăng khả năng mở rộng
* dễ triển khai load balancing
* server đơn giản hơn trong việc quản lý session

### Lưu ý

Stateless **không có nghĩa là hệ thống không có trạng thái**.
Trạng thái vẫn tồn tại, nhưng được lưu ở:

* client
* database
* cache
* token
  thay vì server giữ session cứng theo từng user.

---

## 5.3 Cacheable

### Khái niệm

REST yêu cầu response phải cho biết:

* có được cache không
* cache trong bao lâu
* khi nào cần kiểm tra lại dữ liệu

### Ví dụ

Server trả về:

```http
Cache-Control: max-age=600
```

nghĩa là dữ liệu có thể cache trong 600 giây.

Hoặc:

```http
ETag: "product-15-v2"
```

Lần sau client có thể hỏi lại bằng:

```http
If-None-Match: "product-15-v2"
```

Nếu dữ liệu chưa đổi, server trả:

```http
304 Not Modified
```

### Mục đích

* giảm số request không cần thiết
* tăng tốc tải trang
* giảm tải server
* tiết kiệm băng thông

### Ví dụ thực tế

Browser cache:

* logo
* CSS
* JS
* ảnh

API cache:

* danh mục sản phẩm
* bài viết
* dữ liệu ít thay đổi

### Lợi ích

* hiệu năng tốt hơn
* người dùng thấy phản hồi nhanh hơn
* hệ thống chịu tải tốt hơn

### Rủi ro

* nếu cấu hình sai có thể dùng dữ liệu cũ
* không phù hợp với dữ liệu nhạy cảm hoặc đổi liên tục

---

## 5.4 Uniform Interface

Đây là ràng buộc quan trọng nhất của REST.

### Ý nghĩa

REST yêu cầu một cách giao tiếp **thống nhất**, nhất quán giữa client và server.

Nó thường được hiểu qua 4 ý chính:

### a) Resource identification

Mỗi resource phải được nhận diện rõ bằng URI.

Ví dụ:

```http
/users/1
/products/8
/orders/20
```

### b) Manipulation through representations

Client thao tác resource thông qua representation như JSON.

Ví dụ:

* lấy user → server trả JSON user
* cập nhật user → client gửi JSON mới

### c) Self-descriptive messages

Mỗi message nên tự mô tả đủ để hiểu cách xử lý.

Ví dụ:

* HTTP method nói mục đích request
* header nói kiểu dữ liệu
* status code nói kết quả
* body chứa dữ liệu

### d) HATEOAS (ít dùng trong thực tế)

Response có thể chứa link để client biết bước tiếp theo.

Ví dụ:

```json
{
  "id": 10,
  "name": "Book",
  "links": [
    { "rel": "self", "href": "/products/10" },
    { "rel": "reviews", "href": "/products/10/reviews" }
  ]
}
```

### Tại sao uniform interface quan trọng?

Vì nó tạo ra:

* quy tắc chung
* dễ hiểu
* dễ dùng
* client và server ít phụ thuộc chặt vào nhau

### Ví dụ tốt

```http
GET /products
POST /products
GET /products/1
PUT /products/1
DELETE /products/1
```

### Ví dụ không tốt

```http
GET /getAllProducts
POST /createNewProductNow
POST /deleteProductById
```

Cách sau vẫn chạy được, nhưng kém RESTful hơn vì không tận dụng uniform interface.

---

## 5.5 Layered System

### Khái niệm

Hệ thống REST có thể được tổ chức thành nhiều lớp, và client không cần biết nó đang nói chuyện trực tiếp với server gốc hay qua lớp trung gian.

### Ví dụ lớp trung gian

* load balancer
* reverse proxy
* API gateway
* authentication layer
* cache layer
* application server

### Ví dụ thực tế

Khi client gọi:

```http
GET /products
```

request có thể đi qua:

1. CDN
2. Load balancer
3. API gateway
4. Auth middleware
5. App server
6. Database

Nhưng client chỉ thấy:

* gửi request
* nhận response

### Lợi ích

* tăng bảo mật
* dễ mở rộng
* dễ thay đổi hạ tầng bên trong
* dễ quản lý từng tầng riêng

### Nhược điểm

* tăng độ phức tạp
* debug khó hơn nếu không có logging tốt
* có thể tăng độ trễ

---

## 5.6 Code-on-Demand

### Khái niệm

Server có thể gửi code cho client để client thực thi.

Ví dụ điển hình:

* gửi JavaScript cho browser chạy

### Vì sao gọi là tùy chọn?

Đây là ràng buộc **optional**, không bắt buộc.
Một hệ thống vẫn có thể được xem là RESTful dù không dùng code-on-demand.

### Ví dụ

Server trả về:

* HTML
* CSS
* JavaScript

JavaScript chạy trên browser để:

* validate form
* cập nhật giao diện
* tạo tương tác động

### Thực tế hiện nay

Trong REST API hiện đại, phần này ít được nhắc đến vì đa số API chỉ trả:

* JSON
* XML
* dữ liệu thuần

---

# 6. Tóm tắt 6 ràng buộc cực dễ nhớ

## Client-Server

Tách client và server.

## Stateless

Mỗi request tự mang đủ thông tin.

## Cacheable

Response phải cho biết có thể cache hay không.

## Uniform Interface

Giao tiếp phải thống nhất, nhất quán.

## Layered System

Hệ thống có thể có nhiều lớp trung gian.

## Code-on-Demand

Server có thể gửi code cho client chạy, nhưng không bắt buộc.

---

# 7. Vì sao phải dùng REST?

Đây là câu hỏi rất quan trọng.

## 7.1 Dễ hiểu, dễ học, dễ dùng

REST tận dụng HTTP vốn đã quen thuộc:

* `GET`
* `POST`
* `PUT`
* `DELETE`
* status code
* header

Lập trình viên dễ nắm bắt.

---

## 7.2 Phù hợp với web và Internet

REST sinh ra để phù hợp với mô hình web:

* URI
* HTTP
* client-server
* cache
* proxy
* layered system

Nên nó rất tự nhiên khi xây dựng web API.

---

## 7.3 Dễ mở rộng hệ thống

Nhờ stateless và layered system, REST rất hợp với:

* load balancing
* horizontal scaling
* microservices
* cloud deployment

---

## 7.4 Hỗ trợ nhiều loại client

Một REST API có thể được dùng bởi:

* web app
* mobile app
* desktop app
* đối tác bên ngoài
* hệ thống nội bộ khác

---

## 7.5 Tách biệt frontend và backend

Frontend có thể phát triển độc lập với backend.
Ví dụ:

* backend viết bằng ASP.NET Core
* frontend viết bằng React
* mobile viết bằng Flutter

Tất cả dùng chung REST API.

---

## 7.6 Dễ bảo trì

Khi API được thiết kế tốt:

* route rõ ràng
* method nhất quán
* response dễ đoán

thì code dễ bảo trì hơn.

---

## 7.7 Tận dụng hạ tầng sẵn có

REST tận dụng được:

* browser cache
* CDN
* reverse proxy
* HTTP security
* logging, monitoring
* gateway

---

# 8. Ứng dụng của REST trong thực tế

REST được dùng rất rộng.

## Web application

Frontend gọi backend để:

* login
* lấy dữ liệu
* lưu form
* tìm kiếm
* thanh toán

## Mobile application

App Android/iOS gọi API để:

* lấy profile
* xem sản phẩm
* chat
* đặt hàng

## Microservices

Các service giao tiếp với nhau qua REST API.

Ví dụ:

* user service
* order service
* payment service
* notification service

## Third-party integration

Cho phép hệ thống bên ngoài tích hợp:

* thanh toán
* bản đồ
* vận chuyển
* CRM
* ERP

## SaaS platforms

Rất nhiều nền tảng cung cấp REST API cho nhà phát triển.

---

# 9. Ví dụ luồng REST trong hệ thống thực tế

Giả sử có app bán hàng.

## Đăng nhập

Client:

```http
POST /auth/login
```

Server trả:

```json
{
  "token": "abc123"
}
```

## Lấy danh sách sản phẩm

Client:

```http
GET /products
Authorization: Bearer abc123
```

Server trả:

```json
[
  { "id": 1, "name": "Laptop" },
  { "id": 2, "name": "Phone" }
]
```

## Xem chi tiết sản phẩm

Client:

```http
GET /products/1
```

## Thêm vào giỏ hàng

Client:

```http
POST /cart/items
```

## Tạo đơn hàng

Client:

```http
POST /orders
```

Đây là ví dụ REST rất điển hình.

---

# 10. RESTful API là gì?

Một API được gọi là **RESTful** khi nó được thiết kế theo tư tưởng REST, đặc biệt là:

* resource rõ ràng
* URI hợp lý
* dùng đúng HTTP method
* stateless
* response rõ ràng
* tận dụng HTTP tốt

Ví dụ RESTful:

```http
GET /students
GET /students/5
POST /students
PUT /students/5
DELETE /students/5
```

---

# 11. REST không phải là gì?

Điểm này rất hay gây nhầm.

## REST không phải chỉ là JSON

JSON chỉ là định dạng dữ liệu thường dùng.
REST là kiến trúc.

## REST không phải chỉ là HTTP method

Dùng `GET` hay `POST` thôi chưa đủ để gọi là REST đúng nghĩa.

## REST không phải session-based server state

REST thiên về stateless.

## REST không phải luôn hoàn hảo cho mọi trường hợp

Có lúc GraphQL, gRPC, WebSocket hoặc message queue phù hợp hơn.

---

# 12. Ưu điểm của REST

## Đơn giản

Dễ học và dễ triển khai.

## Phổ biến

Được hỗ trợ bởi hầu hết framework.

## Linh hoạt

Có thể phục vụ nhiều client.

## Scale tốt

Nhờ stateless và cacheable.

## Dễ tích hợp

Nhiều hệ thống bên ngoài hỗ trợ REST.

## Tận dụng HTTP

Không phải phát minh giao thức mới.

---

# 13. Nhược điểm của REST

## Có thể over-fetch hoặc under-fetch

Ví dụ:

* lấy quá nhiều dữ liệu không cần
* hoặc phải gọi nhiều API mới đủ dữ liệu

## Không lý tưởng cho realtime mạnh

REST phù hợp request-response hơn là realtime liên tục.
Realtime thường dùng:

* WebSocket
* SSE

## Khó biểu diễn nghiệp vụ quá phức tạp nếu thiết kế route kém

Nếu chỉ chăm chăm vào CRUD, API có thể thiếu tự nhiên với các nghiệp vụ đặc biệt.

## HATEOAS ít được dùng

Một phần lý tưởng của REST ít được hiện thực đầy đủ trong thực tế.

---

# 14. Khi nào nên dùng REST?

Nên dùng REST khi:

* xây dựng web API thông thường
* CRUD là chủ yếu
* cần hỗ trợ nhiều client
* cần giao tiếp qua HTTP
* muốn hệ thống dễ hiểu, dễ bảo trì
* cần public API cho bên khác dùng

Ví dụ phù hợp:

* hệ thống quản lý sinh viên
* app bán hàng
* blog
* quản lý đơn hàng
* hệ thống bệnh viện
* LMS, CRM, HRM

---

# 15. Khi nào REST có thể không phải lựa chọn tốt nhất?

## Realtime chat/game/trạng thái liên tục

Nên cân nhắc WebSocket.

## Cần truy vấn dữ liệu cực linh hoạt từ frontend

Có thể cân nhắc GraphQL.

## Giao tiếp nội bộ hiệu năng cao giữa service

Có thể cân nhắc gRPC.

## Xử lý bất đồng bộ mạnh

Có thể dùng message broker như Kafka, RabbitMQ.

---

# 16. Các nguyên tắc thiết kế REST API tốt

## Dùng danh từ cho resource

Nên:

```http
GET /products
GET /orders
GET /users
```

Không nên:

```http
GET /getProducts
POST /createOrder
```

## Dùng đúng HTTP method

* `GET` để lấy
* `POST` để tạo
* `PUT/PATCH` để cập nhật
* `DELETE` để xóa

## Dùng status code hợp lý

* `200 OK`
* `201 Created`
* `204 No Content`
* `400 Bad Request`
* `401 Unauthorized`
* `403 Forbidden`
* `404 Not Found`
* `500 Internal Server Error`

## Response nhất quán

Nên có cấu trúc rõ ràng.

## Versioning nếu cần

Ví dụ:

```http
/api/v1/products
/api/v2/products
```

## Quan tâm bảo mật

* HTTPS
* authentication
* authorization
* rate limiting
* input validation

---

# 17. Ví dụ status code trong REST

## 200 OK

Lấy dữ liệu thành công.

## 201 Created

Tạo mới thành công.

## 204 No Content

Thành công nhưng không cần trả body.

## 400 Bad Request

Request sai dữ liệu.

## 401 Unauthorized

Chưa xác thực.

## 403 Forbidden

Có đăng nhập nhưng không có quyền.

## 404 Not Found

Không tìm thấy resource.

## 500 Internal Server Error

Lỗi phía server.

---

# 18. Một ví dụ REST hoàn chỉnh

## Yêu cầu

Tạo sinh viên mới.

Client gửi:

```http
POST /students
Content-Type: application/json
```

Body:

```json
{
  "name": "An",
  "email": "an@gmail.com"
}
```

Server xử lý xong, trả:

```http
201 Created
Location: /students/101
```

Body:

```json
{
  "id": 101,
  "name": "An",
  "email": "an@gmail.com"
}
```

Đây là một response RESTful khá đẹp:

* đúng method
* đúng status
* trả representation của resource mới
* có `Location`

---

# 19. So sánh REST với SOAP, GraphQL, gRPC rất ngắn

## REST

* đơn giản
* phổ biến
* dựa trên HTTP
* phù hợp CRUD/web API

## SOAP

* nặng hơn
* chuẩn chặt chẽ
* XML-based
* hay gặp trong enterprise cũ

## GraphQL

* client chọn đúng dữ liệu cần
* linh hoạt hơn
* nhưng phức tạp hơn REST

## gRPC

* nhanh
* mạnh cho service-to-service
* không trực quan bằng REST với web client thông thường

---

# 20. Kết luận tổng quát

REST là một **phong cách kiến trúc** giúp thiết kế API theo cách:

* rõ ràng
* chuẩn hóa
* dễ mở rộng
* phù hợp với web

Nó dựa trên 6 ràng buộc:

1. **Client-Server**
2. **Stateless**
3. **Cacheable**
4. **Uniform Interface**
5. **Layered System**
6. **Code-on-Demand**

Trong đó:

* **Uniform Interface** là phần cốt lõi nhất
* **Stateless** rất quan trọng cho scale
* **Cacheable** giúp tối ưu hiệu năng
* **Layered System** giúp hệ thống linh hoạt
* **Code-on-Demand** là tùy chọn

REST được dùng rộng rãi vì:

* dễ học
* dễ triển khai
* phù hợp với HTTP
* hỗ trợ nhiều client
* dễ tích hợp
* dễ bảo trì
* scale tốt

---

# 21. Cách học REST cho chắc

Bạn nên học theo thứ tự này:

## Bước 1

Hiểu:

* resource là gì
* URI là gì
* representation là gì

## Bước 2

Nắm chắc:

* GET
* POST
* PUT
* PATCH
* DELETE

## Bước 3

Học status code.

## Bước 4

Học 6 constraints.

## Bước 5

Thực hành thiết kế một REST API thật:

* students
* products
* orders
* books

## Bước 6

Học thêm:

* auth bằng JWT
* cache
* versioning
* pagination
* filtering
* sorting
* error handling

---

Nếu bạn muốn, ở tin nhắn tiếp theo tôi có thể biến bài này thành một **bản note học tập cực gọn 1 trang**, hoặc thành một **bài giảng kiểu cho sinh viên năm 3 có ví dụ minh họa từng request/response**.
