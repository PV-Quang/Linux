### Cài đặt Certbot

> Centos
``` shell
yum -y install certbot
```

> Ubuntu
``` shell
apt -y install certbot
```

### Tạo Certificate
> Chạy lệnh sau để tạo cert
``` shell
certbot certonly --manual --preferred-challenges dns --key-type rsa --rsa-key-size 2048 -d "*.quangpv.tech" -d quangpv.tech
```
![11](Image/11.png)

> Add record TXT với value được tạo ở lệnh trên
<p>

  ![10](Image/10.png)

### Kiểm tra đúng value là được
> Dùng cmd
``` shell
nslookup -type=TXT _acme-challenge.quangpv.tech 8.8.8.8
```
<p>

![12](Image/12.png)

> Dùng web: https://toolbox.googleapps.com/apps/dig/
<p>

  ![13](Image/13.png)

### Hoàn thành tạo cert
> Sau khi xác nhận ok nhấn enter để tiếp tục

![15](Image/15.png)

> Nén thư mục chứa cert để download về
``` shell
zip -r /etc/letsencrypt/live/quangpv.tech.zip /etc/letsencrypt/live/quangpv.tech/ certonly --manual --preferred-challenges dns --key-type rsa --rsa-key-size 2048 -d "*.quangpv.tech" -d quangpv.tech
```

![16](Image/16.png)


