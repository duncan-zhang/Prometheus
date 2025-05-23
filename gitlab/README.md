# Gitlab monitor by Promtheus

## 1.Grafana 配置
### Grafana Permission Denied
Grafana 容器在嘗試創建 `/var/lib/grafana/plugins` 目錄時遇到了權限問題，並且顯示 `/var/lib/grafana` 目錄不可寫。
這通常是因為宿主機上的掛載目錄 `(./grafana)` 權限不足，導致容器無法寫入數據。
### Solution
建立目錄及修改擁有者
```sh
mkdir -p ./grafana
sudo chown -R 472:472 ./grafana
```
## 2. Gitlab API Token
1. 啟用Mertics-Promtheus(default enabled)  
`Gitlab Website -> admin -> settings -> Metrics and profiling -> Metrics-Prometheus`
2. 獲取Personal Access Token
- 1. 以 admin 身份登入 GitLab
- 2. 個人頭像 → Edit Profile（或 Preferences / 設定）
- 3. 左側選單找 Access Tokens（或「存取權杖」、「個人存取憑證」）
- 4. 輸入名稱（自取），設定有效期限（可不設，測試建議不設）
- 5. Scopes 權限 勾選：
     - read_api（最少要勾這個！）
     - 若不放心可全勾（admin 沒差，但建議原則上給最小權限即可）
- 6. 按建立，複製這組 token

## 3. 配置docker-compose
將`token`填入`docker-compose.yaml`的`GCPE_GITLAB_TOKEN`

## 4. Git-runner配置
- 1. 啟用git-runner監聽port(default:9252)
 ```sh
 vim /etc/gitlab-runner/config.toml
 ```
 ```sh
 #在globe區域配置 最上面區塊
 listen_address = "0.0.0.0:9252"
```
- 2. 確認`prometheus.yml`是否配置`targets`