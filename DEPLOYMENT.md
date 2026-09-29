# Thông Tin Deploy — Checkpoint 5

## Thông Tin Học Viên

| Mục | Nội dung |
|-----|----------|
| Họ và tên | Tran Nam Anh |
| Mã học viên | 2A202602901 |
| Repo | https://github.com/trnamanh12/K4-L3B-DAY12-TranNamAnh-2A202602901-CloudServicesAndDeployment |

## Service

| Mục | Nội dung |
|-----|----------|
| Public URL | https://day12-agent-production-dabc.up.railway.app |
| Platform | Railway |
| Ngày deploy | 2026-09-29 |

## Biến Môi Trường Đã Set Trên Cloud

Chỉ ghi tên biến và nguồn giá trị, không ghi giá trị secret.

| Biến | Đã set | Ghi chú |
|------|--------|---------|
| `PORT` | ✅ | Railway tự cấp; app đọc từ biến môi trường |
| `AGENT_API_KEY` | ✅ | Lấy từ `.env` cục bộ, lưu trong Railway Variables |
| `REDIS_URL` | ✅ | Reference variable tới `Redis.REDIS_URL` trong cùng project Railway |
| `RATE_LIMIT_PER_MINUTE` | ✅ | `10` |
| `MONTHLY_BUDGET_USD` | ✅ | `10.0` |
| `LOG_LEVEL` | ✅ | `INFO` |

## Lệnh Kiểm Tra

Đã kiểm tra public service tại `https://day12-agent-production-dabc.up.railway.app`:

```text
GET /health
HTTP 200
{"status":"ok","service":"day12-agent","version":"1.0.0"}

GET /ready
HTTP 200
{"status":"ready","redis":true}

POST /ask không có API key
HTTP 401
{"detail":"invalid or missing API key"}

POST /ask có API key hợp lệ
HTTP 200
{"answer":"Câu hỏi hay. Deploy là gì thường được giải quyết bằng cách chuẩn hóa môi trường chạy: cùng một image chạy giống nhau ở laptop và trên cloud.","user_id":"sv-test","history_length":0,"cost_usd":2.145e-05,"tokens":{"in":3,"out":35}}
```

Lệnh có thể chạy lại từ terminal sau khi đặt `AGENT_API_KEY` trong môi trường cục bộ:

```bash
curl -i https://day12-agent-production-dabc.up.railway.app/health
curl -i https://day12-agent-production-dabc.up.railway.app/ready
curl -i -X POST https://day12-agent-production-dabc.up.railway.app/ask \
  -H "Content-Type: application/json" \
  -d '{"question":"Hello"}'
curl -i -X POST https://day12-agent-production-dabc.up.railway.app/ask \
  -H "Content-Type: application/json" \
  -H "X-API-Key: $AGENT_API_KEY" \
  -H "X-User-Id: sv-test" \
  -d '{"question":"Deploy là gì?"}'
```

## Ảnh Chụp Màn Hình

- `screenshots/health.png` — kết quả `/health` của public service.
- `screenshots/ready.png` — kết quả `/ready`, xác nhận kết nối Redis.

Ảnh dashboard Railway chưa được chụp vì phiên làm việc này không có trình
duyệt tương tác đã đăng nhập. Để hoàn thiện bằng chứng nộp bài, mở dashboard
service `day12-agent` trên Railway, chụp trang có trạng thái deployment thành
công và lưu ảnh thành `screenshots/dashboard.png`.
