# babyshop.1.0.2
### Công việc đã thực hiện
* Render trang sản phẩm, chi tiết
* Làm xong các router ứng với từng category và sub_category
* Dùng module **speakingurl** để parser title sang chuẩn SEO
```js
const getSlug = require('speakingurl')
let SEO = getSlug(data, {lang: 'vn'})
```
* Sử dụng thêm redis để caching
```js
const redis = require('promise-redis')(function (resolver) {
    return new Promise(resolver);
});
const client = redis.createClient()

client.on('connect', () => {
    console.log('connected');
})

```

# Redis Server
## Dùng Resdis để cache ngoài dữ liệu từ dữ liệu tử database trả về
### Caching là gì
Theo truyền thống, truy cập vào ổ đĩa thì tốn kém. Thường xuyên truy cập dữ liệu từ ổ đĩa sẽ gây ra chậm, hiệu năng sẽ giảm. Để chống lại điều này, chúng ta có thể thực hiện một lớp bộ nhớ đệm ở giữa ứng dụng và máy chủ cơ sở dữ liệu.

Lớp caching thì không lưu dữ liệu ở lần đầu. Khi nhận yêu cầu dữ liệu, nó sẽ truy cập cơ sở dữ liệu và lưu kết qủa vào trong bộ nhớ (gọi là cache). Những yêu cầu dữ liệu ở lần tiếp theo sẽ được cung cấp từ lớp dữ liệu cache này, vậy nên không cần thiết phải truy cập database, nên hiệu suất sẽ tăng lên.

### Tại sao sử dụng Redis
* Redis là một in-memory, dạng cấu trúc lưu trữ dữ liệu, sử dụng như là databse, lưu trữ dạng key-value. Ưu điểm là tốc độ nhanh và phục hồi dữ liệu gần như tức thời. Redis hỗ trợ cấu trúc dữ liệu cao cấp như lists, hashes, sets, và có thể tồn tại vào đĩa.

* Trong khi nhiều lập trình viên thích Memcache với dalli cho việc caching của họ, tôi tìm thấy Redis thì đơn giản cho việc cài đặt và dẽ dàng để quản trị. Nếu bạn sử dụng reque hoặc sidekiq cho việc quản lý backgroud jobs, bạn cũng cần dùng redis.
### Dowload
* dowload redis 
[dowload link tại đây](https://redis.io/download)
* Dowload redis-server, redis-cli để kiểm tra trên terminal
```js
    const redis = require('promise-redis')

    const client = redis.createcreateClient()

    // Check kết nối
    client.on('connect', () => {
        console.log('connected')
```

### Cách sử dụng
#### Redis hỗ trợ nhiều kiểu dữ liệu khác nhau, đơn giản dùng kiểu string, hashes. Một vấn đề quan trọng cần tìm hiểu pub/sub 
* Trước khi vào database để lấy dữ liệu --> kiểm tra trong cache đã có data chưa
* Kiểu String 
  *  get, set --> set, get key
  * Mset, Mget --> set và get nhiều key 
[xem thêm tại đây] (https://redis.io/commands/mget)
```js
    client.get('data')
    .then(data => {
        if(data) {
            res.json(data)
        }else{
            db.any('SELECT * FROM user WHERE id = $1')
            .then(result => {
                 client.set('data', result)
            })
        }
    })

```
* Kiểu Hashes tương tự map trong ES6 dưới dang 1 key và 1 map chưa key-value
[xem thêm tại đây] (https://redis.io/commands#hash)

###
[Example về redis](https://github.com/bradtraversy/redusers/blob/master/app.js)

