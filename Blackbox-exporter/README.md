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
---
# Blackbox Exporter
## 簡介
`Blackbox Exporter`是由Prometheus官方維護的外掛，用來「從外部」檢查你的服務是否正常。它不像 node_exporter 那樣從主機內部抓取指標，而是像一個「使用者」從網路上實際存取你的服務（網站、API、TCP 服務等），模擬真實連線狀態。
## 主要支援檢查類型
1. HTTP/HTTPS檢查
  - 模擬 GET/POST 訪問網址，檢查 HTTP 狀態碼、回應內容、SSL 憑證有效性等。
2. TCP 檢查
  - 檢查目標 IP/Port 是否可連線（如 3306、5432、6379 等）。
3. ICMP(Ping)檢查
  - 模擬 ping 指令，看目標主機是否可達。
4. DNS查詢
  - 查詢特定 DNS 記錄，驗證 DNS 是否能正常解析。

## 基本架構與運作流程
1. Prometheus 發送 HTTP 請求給 Blackbox Exporter，要求檢查一個特定的 endpoint。
2. Blackbox Exporter 根據 Prometheus 的參數，去做 HTTP/TCP/ICMP/DNS 檢查。
3. 將檢查結果（如是否存活、回應時間、狀態碼等）轉成 metrics。
4. Prometheus 再來抓這些 metrics，存進時序資料庫，可用於告警、儀表板。

## 官方文件
GitHub:(https://github.com/prometheus/blackbox_exporter)
