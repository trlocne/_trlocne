---
title: "FAST API"
date: 2025-02-17
authors:
  - name: tr.locne
    link: https://github.com/trlocne
    image: https://github.com/trlocne.png
password: "1234"
type: blogs
math: true
comments: true
blog: true
---
FastAPI
<!--more-->
## 1. Introduction

{{% details title="Cách chúng ta giao tiếp giữa 2 ngôn ngữ khác nhau?" closed="true" %}}

![alt text](image.png)

Chúng ta giao tiếp với nhau qua API, vậy API là gì?
{{% /details %}}

## 2. API

{{% details title="Getting Started" closed="true" %}}
**API** (Giao diện lập trình ứng dụng) là một công cụ trung gian cho phép các ứng dụng, cơ sở dữ liệu, phần mềm và thiết bị IoT giao tiếp với nhau.

![alt text](image-1.png)


{{% /details %}}

{{% details title="Các công nghệ API phổ biến" closed="true" %}}
![alt text](image-2.png)

{{% details title="REST (Representational State Transfer)" closed="true" %}}

+ **Client-Server:** Tách biệt giữa giao diện người dùng và lưu trữ dữ liệu.
+ **Stateless:** Mỗi request từ client phải chứa tất cả thông tin cần thiết để hiểu request đó.
+ **Cacheable:** Server phải gửi thông tin về cache để client có thể sử dụng.
+ **Uniform Interface:** Để giảm sự phức tạp của hệ thống, REST sử dụng các giao thức chuẩn như HTTP.
+ **Layered System:** Client không cần biết server làm gì, server cũng không cần biết client làm gì.
+ REST hỗ trợ nhiều định dạng dữ liệu, bao gồm:
  + **JSON (JavaScript Object Notation):** Định dạng dữ liệu phổ biến nhất.
  + **XML:** Định dạng phức tạp hơn, thường được sử dụng trong các ứng dụng doanh nghiệp.
  + **HTML:** Định dạng dữ liệu được hiển thị trên trình duyệt.

{{< callout type="info" >}}
  Ưu điểm của REST: Linh hoạt trong việc xử lý định dạng và cấu trúc dữ liệu. Dễ dàng mở rộng và tích hợp với nhiều loại client khác nhau.
{{< /callout >}}

{{% /details %}}

{{% details title="GraphQL" closed="true" %}}
GraphQL là một ngôn ngữ truy vấn cho API, nơi người dùng có thể yêu cầu chính xác dữ liệu mà họ cần. Nó sử dụng schema để xác định các loại dữ liệu và mối quan hệ giữa chúng. Điều này cho phép người dùng truy vấn nhiều tài nguyên trong một yêu cầu duy nhất.

GraphQL chủ yếu sử dụng **JSON** để truyền dữ liệu. Cấu trúc của dữ liệu được xác định bởi schema, cho phép client yêu cầu các trường cụ thể.

{{< callout type="info" >}}
  Giải quyết vấn đề over-fetching (lấy quá nhiều dữ liệu) và under-fetching (lấy không đủ dữ liệu). Client có thể yêu cầu đúng các trường dữ liệu mà nó cần.
{{< /callout >}}

{{% /details %}}

{{% details title="SOAP (Simple Object Access Protocol) " closed="true" %}}
SOAP (Simple Object Access Protocol) là một giao thức truyền thông cho phép trao đổi dữ liệu giữa các ứng dụng. Nó có cấu trúc tin nhắn cố định, sử dụng XML để định nghĩa nội dung và quy tắc giao tiếp. SOAP thường đi kèm với WS-Security để đảm bảo tính bảo mật.

SOAP sử dụng XML làm định dạng chính để cấu trúc tin nhắn. Điều này giúp đảm bảo tính nhất quán và tính tương thích giữa các hệ thống khác nhau.

{{< callout type="info" >}}
  Rất an toàn và có thể mở rộng. Hỗ trợ các giao thức bảo mật như WS-Security, phù hợp cho các ứng dụng yêu cầu bảo mật cao như ngân hàng.

{{< /callout >}}

{{% /details %}}

{{% details title="RPC (Remote Procedure Call)" closed="true" %}}

**RPC (Remote Procedure Call)** cho phép một chương trình gọi hàm hoặc thực thi một thủ tục trên một máy tính khác như thể nó là một phần của chương trình cục bộ. RPC thường sử dụng các giao thức như HTTP/HTTPS và có thể truyền dữ liệu ở nhiều định dạng khác nhau.

RPC hỗ trợ nhiều định dạng khác nhau, bao gồm JSON, XML, và Flatbuffers, cho phép linh hoạt trong việc truyền tải dữ liệu.

{{< callout type="info" >}}
  Tải trọng nhẹ giúp tăng hiệu suất. Cách thức gọi hàm rất gần gũi với lập trình, giúp người phát triển dễ dàng sử dụng.
{{< /callout >}}
{{% /details %}}
{{% /details %}}

{{% details title="Non-Concurrency and Concurrency" closed="true" %}}

+ Concurrency: Đồng thời, nhiều tác vụ chạy cùng một lúc.
{{% details title="Example Concurrency" closed="true" %}}
![alt text](image-3.png)
{{% /details %}}
+ Non-Concurrency: Không đồng thời, một tác vụ chạy xong mới tới tác vụ khác.

{{< callout type="error" >}}
  Concurrency is not parabellism.
{{< /callout >}}

<img src="image-4.png" width='300px'>
{{% /details %}}

## 3. Fast API

{{% details title="Getting Started" closed="true" %}}
![alt text](image-5.png)
![alt text](image-6.png)
{{% /details %}}

{{% details title="First API" closed="true" %}}
![alt text](image-7.png)
Then locate `localhost:8000/docs` to see the Swagger UI.

<img src="image-8.png' width='300px'>
{{% /details %}}

{{% details title="Path Operations" closed="true" %}}
![alt text](image-9.png)

{{% /details %}}

{{% details title="How to send data to the API endpoints" closed="true" %}}

![alt text](image-10.png)
{{% /details %}}

{{% details title="Path Parameters" closed="true" %}}
+ **Path Parameters** are request parameters that have been attached to the URL

+ **Path Parameters** are usually defined as a way to find information based on location

![alt text](image-11.png)

![alt text](image-12.png)
{{% /details %}}

{{% details title="Query Parameters" closed="true" %}}
**Query Parameters** are request parameters that have been attached after a “?”
**Query Parameters** have `name`=`value pairs`

+ Example:
     `localhost:8000/items/?skip=0&limit=10`

![alt text](image-13.png)

![alt text](image-14.png)
{{% /details %}}


{{% details title="Request Body" closed="true" %}}

{{% details title="POST Request Method" closed="true" %}}
+ Used to create data
+ POST can have a body that has additional information that GET does not have
![alt text](image-15.png)
{{% /details %}}

{{% details title="PUT Request Method" closed="true" %}}
+ Used to update data
+ PUT can have a body that has additional information (like POST) that GET does not have

![alt text](image-16.png)
{{% /details %}}

{{% details title="DELETE Request Method" closed="true" %}}
+ Used to delete data
![alt text](image-17.png)

{{% /details %}}

{{% /details %}}


## 4. Pydantics

![alt text](image-22.png)

{{% details title="Overview" closed="true" %}}
Pydantic là một thư viện Python mạnh mẽ được sử dụng để mô hình hóa dữ liệu, phân tích dữ liệu và xử lý lỗi hiệu quả. Nó thường được sử dụng để xác thực dữ liệu và xử lý dữ liệu đến trong ứng dụng FastAPI. Pydantic giúp định nghĩa các mô hình dữ liệu và tự động xác thực, chuyển đổi dữ liệu đầu vào, cung cấp thông báo lỗi chi tiết khi xác thực không thành công.

+ **Data Modeling:**

```python
from pydantic import BaseModel

class Item(BaseModel):
    id: int
    name: str
    price: float
```

+ **Data Parsing:**

```python
item_data = {"id": 1, "name": "Sample Item", "price": 19.99}
item = Item(**item_data)
```

+ **Error Handling:**

```python
from pydantic import ValidationError

try:
    item = Item(id="not-an-integer", name="Sample Item", price=19.99)
except ValidationError as e:
    print(e.json())
```
{{% /details %}}

{{% details title="Status Code" closed="true" %}}

![alt text](image-18.png)

![alt text](image-23.png)
{{% details title="2xx Successful Status Codes" closed="true" %}}
![alt text](image-19.png)

{{% /details %}}

{{% details title="4xx Client Errors Status Codes" closed="true" %}}
![alt text](image-20.png)

{{% /details %}}

{{% details title="5xx Server Status Codes" closed="true" %}}
![alt text](image-21.png)

{{% /details %}}

{{% /details %}}

{{% details title="Annotated and Field" closed="true" %}}

**Annotated** và **Field** trong FastAPI & Pydantic

+ Dùng để thêm metadata vào các mô hình (models) của Pydantic.

+ **Annotated** được sử dụng để thêm metadata tùy chỉnh vào các kiểu dữ liệu.
+ **Field** dùng để thêm các trường dữ liệu cho mô hình (ví dụ giá trị mặc định, xác thực dữ liệu), thêm metadata phục vụ cho quá trình truần tự hóa dữ liệu.

```python {filename="Dữ liệu mẫu"}
from typing import Annotated
from fastapi import FastAPI, Query
from pydantic import BaseModel, Field

app = FastAPI()

items = [
    {
        "name": "Banh thap cam",
        "price": 45.0,
        "description": "Description of the banh trung thu thap cam",
    },
    {
        "name": "Banh dau xanh",
        "price": 40.0,
        "description": "Description of the banh trung thu dau xanh",
    },
]
```

```python {filename="Feild method"}
class Item(BaseModel):
    name: str = Field(..., min_length=1, max_length=100)
    price: float = Field(..., gt=0, description="Price of the mooncake")
    description: str | None = Field(
        None, max_length=500, description="Optional description"
    )
```

`BaseModel`: Kế thừa từ `BaseModel` của Pydantic để xác định mô hình dữ liệu.
`Field(...)`:
+ `min_length=1`, `max_length=100`: Giới hạn độ dài chuỗi cho name.
+ `gt=0`: Giá của bánh phải lớn hơn 0.
+ `description`: Cung cấp mô tả giúp tài liệu API dễ hiểu hơn.
+ `None`, `max_length=500`: Trường description có thể không bắt buộc (None), nhưng tối đa 500 ký tự.

```python {filename="Annotated method"}
@app.get("/search_items/")
async def search_items(
    q: Annotated[str, Query(min_length=3, max_length=50, description="Search query")],
    max_price: Annotated[float | None, Query(gt=0, description="Maximum price")] = None,
):
    results = [item for item in items if q.lower() in item["name"].lower()]

    if max_price:
        results = [item for item in results if item["price"] <= max_price]

    return results
```

{{% /details %}}

{{% details title="File and Upload File" closed="true" %}}
Trong ví dụ sau đây có hai cách để xử lí một file upload trong FastAPI
<img src="image-24.png" width='300px'>
+ **Direct File Access:** Nhận tệp dưới dạng byte và truy cập kích thước của nó.
+ **Upload file class:** Sử dụng UploadFile class cho dạng metata có thể truy cập tên file và content type.


![alt text](image-25.png)
{{% /details %}}
## 5. Middleware

{{% details title="Overview" closed="true" %}}
Middleware là một hàm trung gian hoạt động với mọi request trước khi một route (đường dẫn API) cụ thể được xử lý, và cũng mọi response trước khi nó được trả về client. Middleware thường được dùng để:
+ Xử lý xác thực (authentication)
+ Ghi log (logging)
+ Thêm hoặc thay đổi header của request/response
+ Giới hạn tốc độ (rate limiting)
+ Kiểm tra bảo mật

![alt text](image-26.png)

{{% /details %}}

{{% details title="Chi tiết về Middleware" closed="true" %}}
```python
import time
from fastapi import FastAPI, Request

app = FastAPI()

@app.middleware("http")
async def add_process_time_header(request: Request, call_next):
    # Ghi lại thời gian bắt đầu xử lý request
    start_time = time.perf_counter()
    
    # Gọi middleware tiếp theo hoặc route handler
    response = await call_next(request)
    
    # Tính thời gian xử lý request
    process_time = time.perf_counter() - start_time
    
    # Thêm thời gian xử lý vào response header
    response.headers["X-Process-Time"] = str(process_time)
    
    # Trả về response đã chỉnh sửa
    return response

```

+ **FastAPI Middleware**: Measures HTTP request processing time.
+ **Records Start/End Times**: Uses `time.perf_counter()` to track request duration.
+ **Adds** `“X-Process-Time”` **Header**: Includes processing time in response headers. 
+ **Calls Next Handler**: Passes the request to the next `middleware` or `route`.

{{% details title="Other" closed="true" %}}
```python
from fastapi import FastAPI, Request
from starlette.middleware.base import BaseHTTPMiddleware

app = FastAPI()

class CustomMiddleware(BaseHTTPMiddleware):
    async def dispatch(self, request: Request, call_next):
        # Trước khi xử lý request
        print(f"Request: {request.method} {request.url}")
        
        # Chuyển request đến route handler
        response = await call_next(request)
        
        # Sau khi xử lý response
        response.headers["X-Custom-Header"] = "Middleware Example"
        print("Response processed")

        return response

# Thêm middleware vào FastAPI app
app.add_middleware(CustomMiddleware)

@app.get("/")
async def read_root():
    return {"message": "Hello, World!"}

```

{{% /details %}}
{{% /details %}}

## 6.  CORS (Cross-Origin Resource Sharing)

{{% details title="Overview" closed="true" %}}
CORS (Cross-Origin Resource Sharing) là một cơ chế cho phép tài nguyên từ một trang web hoặc ứng dụng web được yêu cầu từ một nguồn khác ngoài trang web hoặc ứng dụng web hiện tại. CORS cho phép các trang web hoặc ứng dụng web chia sẻ dữ liệu với các nguồn khác mà không cần lo lắng về việc chặn truy cập từ các nguồn khác.

![alt text](image-27.png)

+ Allow requests from specific origins (e.g., http://localhost.tiangolo.com)
+ Allow credentials (cookies, authorization headers, etc.)
+ Allow all HTTP methods (GET, POST, etc.)
+ Enable all headers

![alt text](image-28.png)

{{% /details %}}

## 7. Static Files

{{% details title="Overview" closed="true" %}}
Phục vụ các tệp tĩnh tự động từ một thư mục bằng cách sử dụng **StaticFiles**

"Mounting" có nghĩa là thêm một ứng dụng độc lập vào một đường dẫn cụ thể, sau đó ứng dụng đó sẽ xử lý tất cả các đường dẫn con.
{{% /details %}}

{{% details title="Code" closed="true" %}}
```python
from fastapi import FastAPI
from fastapi.staticfiles import StaticFiles

app = FastAPI()

# Gắn thư mục chứa các tệp tĩnh
app.mount("/static", StaticFiles(directory="static"), name="static")

@app.get("/")
def read_root():
    return {"message": "Hello World"}

```

{{% /details %}}

## 8. Alembic

{{% details title="SQL Database Introduction" closed="true" %}}
Cơ sở dữ liệu là gì?

+ Cơ sở dữ liệu là một tập hợp dữ liệu.
+ Nó quản lý dữ liệu, cho phép việc truy xuất, lưu trữ và chỉnh sửa dễ dàng hơn.
+ Có nhiều loại Hệ quản trị cơ sở dữ liệu (DBMS).

SQL là gì?

+ **SQL (Structured Query Language**) là ngôn ngữ truy vấn cấu trúc. Nó cho phép người dùng thực hiện các thao tác trên các bản ghi trong cơ sở dữ liệu, thường được gọi là CRUD:
  + **Create**: Tạo mới bản ghi.
  + **Read**: Đọc dữ liệu từ bản ghi.
  + **Update**: Cập nhật dữ liệu trong bản ghi.
  + **Delete**: Xóa bản ghi.

{{% /details %}}

{{% details title="Alembic" closed="true" %}}
+ **Alembic** là một công cụ di chuyển cơ sở dữ liệu nhẹ, thường được sử dụng với SQLAlchemy.
+ Nó cung cấp các công cụ di chuyển cho phép lập kế hoạch, chuyển giao và nâng cấp tài nguyên trong cơ sở dữ liệu.
+ Alembic cho phép bạn thay đổi bảng cơ sở dữ liệu SQLAlchemy sau khi đã tạo.
+ Hiện tại, SQLAlchemy chỉ có thể tạo các bảng cơ sở dữ liệu mới mà không thể **update** các bảng đã có.
{{% /details %}}

{{% details title="Alembic hoạt động như thế nào?" closed="true" %}}
+ Alembic cung cấp việc tạo và thực thi các script quản lý thay đổi.
+ Điều này cho phép bạn tạo môi trường di chuyển và thay đổi dữ liệu theo ý muốn.
+ Trong phần này, bạn sẽ học cách sử dụng Alembic cho dự án của mình.

{{% details title="Ví dụ Alembic làm việc?" closed="true" %}}
+ Giả sử bạn đã có một số dữ liệu trong cơ sở dữ liệu của mình.
+ Xem xét bảng người dùng hiện tại và tạo một cột mới trong bảng đã tồn tại.

{{% /details %}}
{{% /details %}}

{{% details title="Tại sao Alembic quan trọng?" closed="true" %}}
+ **Alembic** là một công cụ di chuyển quan trọng giúp bạn sửa đổi cấu trúc cơ sở dữ liệu khi ứng dụng của bạn phát triển.
+ Khi ứng dụng của bạn tiến triển, cơ sở dữ liệu cũng cần phát triển theo.
+ **Alembic** giúp bạn tiếp tục sửa đổi cơ sở dữ liệu để đáp ứng các yêu cầu phát triển nhanh chóng.
+ Nó hoạt động trên các bảng đã có dữ liệu, cho phép bạn liên tục tạo nội dung bổ sung phù hợp với ứng dụng của mình.
{{% /details %}}