# Sử dụng HTTP API trong influxDB

## Tạo dữ liệu trong influxDB với HTTP API 

#### Tạo một database 

- Gửi một post request tới endpoint `/query` và đặt tham số URL `q` là `CREATE DATABASE <new_database_name>`
- Ở ví dụ này gửi một request tới influxDB chạy trên localhost và tạo một database có tên là `minhkma`

`curl -i -XPOST http://localhost:8086/query --data-urlencode "q=CREATE DATABASE minhhkma"`

#### Ghi dữ liệu 

- HTTP API là phương tiện chính để ghi dữ liệu vào influxDB bằng các gửi post request tới enpoint `/write`. 
- Ví dụ dưới đây ghi một dữ liệu vào database minhkma. 
- Dữ liệu bao gồm :

    + measurement là cpu_load_short
    + tag keys là host
    + region là server01 và us-west
    + field key có giá trị là 0.64 
    + timestamp ở lúc 1434055562000000000.

`curl -i -XPOST 'http://localhost:8086/write?db=minhkma' --data-binary 'cpu_load_short,host=server01,region=us-west value=0.64 1434055562000000000'`

- Khi ghi dữ liệu bạn phải chỉ định một cơ sở dữ liệu

#### Ghi nhiều dữ liệu cùng một lúc 

- Đẩy nhiều dữ liệu tới nhiều series cùng một thời điểm bằng cách phân tách chúng thành từng dòng mới.

`curl -i -XPOST 'http://localhost:8086/write?db=minhkma' --data-binary 'cpu_load_short,host=server02 value=0.67
cpu_load_short,host=server02,region=us-west value=0.55 1422568543702900257
cpu_load_short,direction=in,host=server01,region=us-west value=2.0 1422568543702900257'

#### Ghi dữ liệu từ file 

- Ví dụ có một properly-formatted file (cpu_data.txt): 

```sh 
cpu_load_short,host=server02 value=0.67
cpu_load_short,host=server02,region=us-west value=0.55 1422568543702900257
cpu_load_short,direction=in,host=server01,region=us-west value=2.0 1422568543702900257
```

- Ghi dữ liệu từ cpu_data.txt tới database minhkma bằng cách:

`curl -i -XPOST 'http://localhost:8086/write?db=minhkma' --data-binary @cpu_data.txt` 

*Note*: Nếu tệp dữ liệu của bạn có hơn 5.000 điểm, bạn có thể chia tệp đó thành nhiều tệp để ghi các dữ liệu của bạn theo hàng cho InfluxDB. Mặc định HTTP request có times out là 5giây. InfluxDB vẫn sẽ cố gắng ghi các điểm sau thời gian đó nhưng sẽ không có xác nhận rằng đã thành công bằng văn bản.

## Truy vấn dữ liệu sử dụng HTTP API 

API HTTP là phương tiện chính để truy vấn dữ liệu trong InfluxDB ngoài ra còn có các cách khác như sử dụng CLI hoặc client libraries để truy vấn dữ liệu 

- Để thực hiện một truy vấn bạn gửi một GET request tới endpoint `/query` và đặt tham số URL `db` bằng tên database bạn muốn query ở đó, tham số `q` là chuỗi query của bạn. Bạn cũng có thể sử dụng một POST request.

`curl -G 'http://localhost:8086/query?pretty=true' --data-urlencode "db=minhkma" --data-urlencode "q=SELECT \"value\" FROM \"cpu_load_short\" WHERE \"region\"='us-west'"`

- Nếu bạn muốn sử dụng nhiều query cùng một lúc mỗi query của bạn sẽ cách nhau bởi dấu chấm phẩy `;`

#### Một số option khác khi truy vấn dữ liệu 

##### Timestamp Format

- Mặc định timestamps được trả lại theo RFC3339 UTC và có độ chính xác nano giây. 

    VD : `2015-08-04T19:05:14.318570484Z`
- Nếu bạn muốn thời gian được định dạng theo Unix epoch format thì trong request của bạn thêm vào tham số `epoch`
- Get epoch in seconds

    `curl -G 'http://localhost:8086/query' --data-urlencode "db=minhkma" --data-urlencode "epoch=s" --data-urlencode "q=SELECT \"value\" FROM \"cpu_load_short\" WHERE \"region\"='us-west'"`

##### Authentication

Nếu bạn đã cấu hình bảo mật cho influxDB bằng username và password thì khi truy vấn bạn cần phải đặt tham số `u` và `p` trong đó u là username của bạn còn p là password

`curl -G "http://localhost:8086/query?u=minhkma&p=minhkma" --data-urlencode "q=SHOW DATABASES"`



