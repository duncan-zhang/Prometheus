# Grafana Permission Denied
Grafana 容器在嘗試創建 `/var/lib/grafana/plugins` 目錄時遇到了權限問題，並且顯示 `/var/lib/grafana` 目錄不可寫。
這通常是因為宿主機上的掛載目錄 `(./grafana)` 權限不足，導致容器無法寫入數據。
# Solution
創建目錄
```sh
mkdir -p ./grafana
```
修改目錄擁有者及權限
```sh
sudo chown -R 472:472 ./grafana
```
啟動Docker-compose
```sh
docker-compose up -d
```
