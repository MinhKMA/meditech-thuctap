# influxDB 

- Một số note ngắn gọn để ghi nhớ syntax query influxDB 
- Database metric được thu thập bởi collectd plugin virt 


### Hiển thị thông tin về một MEASUREMENTS

```sh
> select * from virt_value where host = '032d3fc6-7d95-b731-91b8-3031d32c81f6' and (time > now() - 1m) and type = 'virt_vcpu'
name: virt_value
time                host                                 instance       type      type_instance value
----                ----                                 --------       ----      ------------- -----
1516340662645778219 032d3fc6-7d95-b731-91b8-3031d32c81f6 cobbler-server virt_vcpu 0             103230000000
1516340662645854252 032d3fc6-7d95-b731-91b8-3031d32c81f6 cobbler-server virt_vcpu 1             184140000000
1516340662955606754 032d3fc6-7d95-b731-91b8-3031d32c81f6 cobbler-server virt_vcpu 0             103230000000
1516340662955615586 032d3fc6-7d95-b731-91b8-3031d32c81f6 cobbler-server virt_vcpu 1             184140000000
1516340672643795870 032d3fc6-7d95-b731-91b8-3031d32c81f6 cobbler-server virt_vcpu 0             103230000000
1516340672643951970 032d3fc6-7d95-b731-91b8-3031d32c81f6 cobbler-server virt_vcpu 1             184140000000
1516340672953629956 032d3fc6-7d95-b731-91b8-3031d32c81f6 cobbler-server virt_vcpu 0             103230000000
1516340672953652497 032d3fc6-7d95-b731-91b8-3031d32c81f6 cobbler-server virt_vcpu 1             184140000000
1516340682655920262 032d3fc6-7d95-b731-91b8-3031d32c81f6 cobbler-server virt_vcpu 0             103240000000
1516340682656007193 032d3fc6-7d95-b731-91b8-3031d32c81f6 cobbler-server virt_vcpu 1             184150000000
1516340682945579114 032d3fc6-7d95-b731-91b8-3031d32c81f6 cobbler-server virt_vcpu 0             103240000000
1516340682945589624 032d3fc6-7d95-b731-91b8-3031d32c81f6 cobbler-server virt_vcpu 1             184150000000
```

### Danh sách các MEASUREMENTS đã thu thập 

```sh 
> SHOW MEASUREMENTS
name: measurements
name
----
cpu_value
df_value
disk_io_time
disk_read
disk_value
disk_weighted_io_time
disk_write
entropy_value
interface_rx
interface_tx
irq_value
load_longterm
load_midterm
load_shortterm
memory_value
processes_value
swap_value
users_value
virt_read
virt_rx
virt_tx
virt_value
virt_write
vmem_in
vmem_majflt
vmem_minflt
vmem_out
vmem_value
```
 

### List tag keys của từng MEASUREMENTS

```sh 
> show tag keys from virt_value

name: virt_value
tagKey
------
host
instance
type
type_instance
```

### Hiển thị giá trị trong tag keys 

```sh 
> show tag values from virt_value with key = host 

name: virt_value
key  value
---  -----
host 032d3fc6-7d95-b731-91b8-3031d32c81f6
host 0ead5100-68dc-419e-44a1-836a0189fa98
host 0f58a110-9f1b-f8fc-6e88-7bf0280114f9
host 139bce83-aae5-aa6f-0c51-b19cce3147e4
host 1ccf28d7-fcb5-d7cc-fc5d-92392af23265
host 2c491595-5647-de1c-473e-4efcb7c1f321
host 2d5c50ac-8839-05fb-0aef-7d600b784587
host 378ec024-20ba-98bd-9a45-3ece38f5dfc7
host 45b031e5-01e7-42c2-ee52-cc82beda3126
host 5111ee5b-49ae-1848-737b-0202406e167c
host 5429157e-df44-268c-03ed-9dbb64b49c20
host 5453512c-43c9-b216-30dd-1bda5b358cbf
host 746d8447-af09-99bd-6307-e07edf642d58
host 781c5136-7d79-45c4-37d3-be6c015d8cf0
host d0568a96-7283-beb8-08e0-50149d75a00a
host d54290b4-db1f-a978-2b9d-fbe1296bda18
host e010292f-0751-70e9-2d6e-1472a868a4eb
``` 
### Show kiểu dữ liệu của từng MEASUREMENTS

```sh
> show FIELD KEYS from virt_value
name: virt_value
fieldKey fieldType
-------- ---------
value    float
```

## Function 

### Count 

EXP 1: Trả về số lượng của các non-null field values.

```sh
> select count(value) from virt_value where host = '032d3fc6-7d95-b731-91b8-3031d32c81f6' and type = 'virt_vcpu' and type_instance = '1'
name: virt_value
time count
---- -----
0    3707
```

### mean()

Trả về giá trị trung bình cho các giá trị trong một trường duy nhất. Kiểu dữ liệu phải là int64 hoặc float64

- Example 1: Calculate the mean field value associated with a field key

```sh 
> select mean(value) from virt_value where host = '032d3fc6-7d95-b731-91b8-3031d32c81f6' and type = 'virt_vcpu' and type_instance = '1'

name: virt_value
time mean
---- ----
0    178770002663.82526
```

```sh 
select mean(value) from virt_value where host = '032d3fc6-7d95-b731-91b8-3031d32c81f6' and type = 'virt_vcpu' and type_instance = '1' group by time(10s)
```

### MEDIAN

Trả về giá trị đứng giữa từ các giá trị được sắp xếp trong một trường đơn

```sh
> select MEDIAN(value) from virt_value where host = '032d3fc6-7d95-b731-91b8-3031d32c81f6' and type = 'virt_vcpu' and type_instance = '1'
name: virt_value
time median
---- ------
0    179090000000
```
### SUM

Tính tổng của các giá trị 

```sh
> select sum(value) from virt_value where host = '032d3fc6-7d95-b731-91b8-3031d32c81f6' and (time > now() - 1m) and type = 'virt_vcpu'
name: virt_value
time                sum
----                ---
1516431266210744661 2364100000000
```

### BOTTOM

Trả về N giá trị nhỏ nhất 

```sh
> select bottom(value, 10) from virt_value where host = '032d3fc6-7d95-b731-91b8-3031d32c81f6' and type = 'virt_vcpu'
name: virt_value
time                bottom
----                ------
1516325512670633215 97260000000
1516325512943086495 97270000000
1516325522642017534 97270000000
1516325522943534278 97270000000
1516325532639300782 97280000000
1516325532945335596 97280000000
1516325542669943259 97280000000
1516325542952156453 97290000000
1516325552633268898 97290000000
1516325552941498074 97290000000
```

***SETUP FORMAT TIME***

``> precision rfc3339``

### FRIST 

Chọn giá trị đầu tiên liên kết với một field key 

```sh
> select first(value) from virt_value where host = '032d3fc6-7d95-b731-91b8-3031d32c81f6' and type = 'virt_vcpu'
name: virt_value
time                           first
----                           -----
2018-01-25T01:19:12.944592883Z 335320000000
```

### LAST 

Trả về giá trị trường mới nhất (được xác định bởi dấu thời gian)

```sh
> select last(value) from virt_value where host = '032d3fc6-7d95-b731-91b8-3031d32c81f6' and type = 'virt_vcpu'
name: virt_value
time                           last
----                           ----
2018-01-20T07:16:42.941520085Z 313600000000

2018-01-19T01:31:52.670633215Z 97260000000
```
### Max 

Trả về giá trị lớn nhất 

```sh
> select max(value) from virt_value where host = '032d3fc6-7d95-b731-91b8-3031d32c81f6' and type = 'virt_vcpu'
name: virt_value
time                           max
----                           ---
2018-01-29T14:24:52.943322821Z 1404700000000
```

### Min 

Trả về giá trị bé nhất 

```sh 
> select min(value) from virt_value where host = '032d3fc6-7d95-b731-91b8-3031d32c81f6' and type = 'virt_vcpu'
name: virt_value
time                           min
----                           ---
2018-01-30T11:11:43.194493067Z 2640000000
```

### Insert 

Ở đây mình tạo ra một database và một series để dễ dành theo dõi hơi trong các ví dụ query tiếp theo 

```sh
CREATE DATABASE pirates
> INSERT cpu,host=minhkma,region=kvm30 value=1
> INSERT cpu,host=minhkma,region=kvm30 value=3
> INSERT cpu,host=minhkma,region=kvm30 value=10
> INSERT cpu,host=minhkma,region=kvm30 value=9
> INSERT cpu,host=minhkma,region=kvm30 value=4
> INSERT cpu,host=minhkma,region=kvm30 value=6
> INSERT cpu,host=minhkma,region=kvm30 value=7
> INSERT cpu,host=minhkma,region=kvm30 value=2
> INSERT cpu,host=minhkma,region=kvm30 value=2
> INSERT cpu,host=minhkma,region=kvm30 value=8
> INSERT cpu,host=minhkma,region=kvm30 value=11
> INSERT cpu,host=minhkma,region=kvm30 value=7
> INSERT cpu,host=minhkma,region=kvm30 value=9
```

```sh
> select * from cpu
name: cpu
time                           sample
----                           ------
2018-01-31T02:13:51.222039696Z 1
2018-01-31T02:13:53.850030976Z 3
2018-01-31T02:13:57.829384354Z 10
2018-01-31T02:14:00.363663952Z 9
2018-01-31T02:14:02.802142327Z 4
2018-01-31T02:14:04.813133162Z 6
2018-01-31T02:14:10.742070259Z 7
2018-01-31T02:14:15.034200219Z 2
2018-01-31T02:14:17.099288577Z 2
2018-01-31T02:14:26.151766031Z 8
2018-01-31T02:14:29.780351378Z 11
2018-01-31T02:14:33.594484378Z 7
2018-01-31T02:14:41.047571581Z 9
```

### Percentile

### Sample

Trả về N giá trị ngẫu nhiên 

```sh
> select sample(value,2) from cpu
name: cpu
time                           sample
----                           ------
2018-01-31T02:14:00.363663952Z 9
2018-01-31T02:14:26.151766031Z 8
```

### Top

Trả về N giá trị lớn nhất 

```sh
> select top(value,4) from cpu
name: cpu
time                           top
----                           ---
2018-01-31T02:13:57.829384354Z 10
2018-01-31T02:14:00.363663952Z 9
2018-01-31T02:14:29.780351378Z 11
2018-01-31T02:14:41.047571581Z 9
```

### Cumulative_sum 

Trả về giá trị cộng giữa tổng giá trị cũ cộng với 1 giá trị mới tiếp theo như dãy số sau `1, 3, 4 , 2, 5` sẽ là `1, 4, 8, 10, 15`

```sh
> select CUMULATIVE_SUM(value) from cpu
name: cpu
time                           cumulative_sum
----                           --------------
2018-01-31T02:13:51.222039696Z 1
2018-01-31T02:13:53.850030976Z 4
2018-01-31T02:13:57.829384354Z 14
2018-01-31T02:14:00.363663952Z 23
2018-01-31T02:14:02.802142327Z 27
2018-01-31T02:14:04.813133162Z 33
2018-01-31T02:14:10.742070259Z 40
2018-01-31T02:14:15.034200219Z 42
2018-01-31T02:14:17.099288577Z 44
2018-01-31T02:14:26.151766031Z 52
2018-01-31T02:14:29.780351378Z 63
2018-01-31T02:14:33.594484378Z 70
2018-01-31T02:14:41.047571581Z 79
```

### Derivative

Trả về tỷ lệ thay đổi giữa các lần tiếp theo

```sh 
> select derivative(value) from cpu
name: cpu
time                           derivative
----                           ----------
2018-01-31T02:13:53.850030976Z 0.7610375328186021
2018-01-31T02:13:57.829384354Z 1.759079763737434
2018-01-31T02:14:00.363663952Z -0.39458945287220043
2018-01-31T02:14:02.802142327Z -2.0504590285735054
2018-01-31T02:14:04.813133162Z 0.994534617061047
2018-01-31T02:14:10.742070259Z 0.16866429574805117
2018-01-31T02:14:15.034200219Z -1.1649227881254556
2018-01-31T02:14:17.099288577Z 0
2018-01-31T02:14:26.151766031Z 0.662801982163324
2018-01-31T02:14:29.780351378Z 0.826768482235179
2018-01-31T02:14:33.594484378Z -1.0487311271001825
2018-01-31T02:14:41.047571581Z 0.26834517637133837
```

Trong đó `0.7610375328186021` bằng 

```sh
   (3 - 1)     / (57.829384354s - 53.850030976s)
-------------    -------------------------------
      |                        |
      |                        |
chênh lệch giữa    chênh lệch giữa 2 khoảng thời gian      
   2 giá trị                 
```

### Difference

Trả lại giá trị chênh lệch giữa 2 giá trị liên tiếp

```sh
> select DIFFERENCE(value) from cpu
name: cpu
time                           difference
----                           ----------
2018-01-31T02:13:53.850030976Z 2
2018-01-31T02:13:57.829384354Z 7
2018-01-31T02:14:00.363663952Z -1
2018-01-31T02:14:02.802142327Z -5
2018-01-31T02:14:04.813133162Z 2
2018-01-31T02:14:10.742070259Z 1
2018-01-31T02:14:15.034200219Z -5
2018-01-31T02:14:17.099288577Z 0
2018-01-31T02:14:26.151766031Z 6
2018-01-31T02:14:29.780351378Z 3
2018-01-31T02:14:33.594484378Z -4
2018-01-31T02:14:41.047571581Z 2
```

### Elapsed 

Trả về chênh lệch giữa mốc thời gian của giá trị liên tiếp.

```sh
> select elapsed(value) from cpu
name: cpu
time                           elapsed
----                           -------
2018-01-31T02:13:53.850030976Z 2627991280
2018-01-31T02:13:57.829384354Z 3979353378
2018-01-31T02:14:00.363663952Z 2534279598
2018-01-31T02:14:02.802142327Z 2438478375
2018-01-31T02:14:04.813133162Z 2010990835
2018-01-31T02:14:10.742070259Z 5928937097
2018-01-31T02:14:15.034200219Z 4292129960
2018-01-31T02:14:17.099288577Z 2065088358
2018-01-31T02:14:26.151766031Z 9052477454
2018-01-31T02:14:29.780351378Z 3628585347
2018-01-31T02:14:33.594484378Z 3814133000
2018-01-31T02:14:41.047571581Z 7453087203
```

### Moving_average

Ví dụ này mình tính giá trị trung bình của 2 trường liên tiếp 1

```sh
> select * from cpu
name: cpu
time                           host    region value
----                           ----    ------ -----
2018-01-31T02:13:51.222039696Z minhkma kvm30  1
2018-01-31T02:13:53.850030976Z minhkma kvm30  3
2018-01-31T02:13:57.829384354Z minhkma kvm30  10
2018-01-31T02:14:00.363663952Z minhkma kvm30  9
2018-01-31T02:14:02.802142327Z minhkma kvm30  4
2018-01-31T02:14:04.813133162Z minhkma kvm30  6
2018-01-31T02:14:10.742070259Z minhkma kvm30  7
2018-01-31T02:14:15.034200219Z minhkma kvm30  2
2018-01-31T02:14:17.099288577Z minhkma kvm30  2
2018-01-31T02:14:26.151766031Z minhkma kvm30  8
2018-01-31T02:14:29.780351378Z minhkma kvm30  11
2018-01-31T02:14:33.594484378Z minhkma kvm30  7
2018-01-31T02:14:41.047571581Z minhkma kvm30  9

> select MOVING_AVERAGE(value,2) from cpu
name: cpu
time                           moving_average
----                           --------------
2018-01-31T02:13:53.850030976Z 2
2018-01-31T02:13:57.829384354Z 6.5
2018-01-31T02:14:00.363663952Z 9.5
2018-01-31T02:14:02.802142327Z 6.5
2018-01-31T02:14:04.813133162Z 5
2018-01-31T02:14:10.742070259Z 6.5
2018-01-31T02:14:15.034200219Z 4.5
2018-01-31T02:14:17.099288577Z 2
2018-01-31T02:14:26.151766031Z 5
2018-01-31T02:14:29.780351378Z 9.5
2018-01-31T02:14:33.594484378Z 9
2018-01-31T02:14:41.047571581Z 8
```


