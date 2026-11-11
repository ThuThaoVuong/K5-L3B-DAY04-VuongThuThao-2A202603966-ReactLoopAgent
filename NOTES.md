# Ghi chú phân tích · Day 04

Họ và tên:
MSSV:

## 1. Case được chọn

- ID case: A5 – Truy vấn danh sách đầy đủ incident GitHub Actions trong năm 2025.
- Câu trả lời của agent: Agent trả lời rằng không có incident GitHub Actions nào trong năm 2025, sau đó liệt kê 5 incident xảy ra trong năm 2026. Agent có đề cập dữ liệu chỉ giới hạn trong 50 incident gần nhất, nhưng chưa nhấn mạnh rõ rằng không thể xác nhận tình hình incident trong năm 2025 từ dữ liệu hiện có.
- Điểm ở lần chạy 1: 50/100.

## 2. Bằng chứng trong trace

Dán đoạn trace chứng minh lỗi và nêu nguyên nhân có thể xảy ra.

```
"trace": [
          {
            "event": "step",
            "step": 1,
            "finish": "tool_calls"
          },
          {
            "event": "tool",
            "step": 1,
            "name": "get_status",
            "arguments": {
              "provider": "github",
              "component": "actions"
            },
            "result": {
              "provider": "github",
              "captured_at": "2026-11-09T03:50:47.065020+00:00",
              "overall": {
                "indicator": "none",
                "description": "All Systems Operational"
              },
              "components": [
                {
                  "name": "Actions",
                  "status": "operational"
                }
              ],
              "incidents": [
                {
                  "id": "pfbmpxxfq1cy",
                  "name": "Degraded performance for multiple GitHub services",
                  "status": "resolved",
                  "updated_at": "2026-11-06T00:20:49.369Z"
                },
                {
                  "id": "t89g2q1800fr",
                  "name": "Incident with Actions",
                  "status": "resolved",
                  "updated_at": "2026-11-05T17:42:35.455Z"
                },
                {
                  "id": "h02n9572mx9y",
                  "name": "Incident with Actions",
                  "status": "resolved",
                  "updated_at": "2026-11-07T01:10:18.506Z"
                },
                {
                  "id": "xr3myzj7ms51",
                  "name": "Incident with Actions and Pages",
                  "status": "resolved",
                  "updated_at": "2026-11-07T01:04:38.647Z"
                },
                {
                  "id": "smtdswd1h2w4",
                  "name": "Incident with Pull Requests, Git Operations and Issues",
                  "status": "resolved",
                  "updated_at": "2026-11-04T11:50:06.459Z"
                }
              ],
              "matching_incidents_in_last_50": 23,
              "note": "Chỉ tìm theo chuỗi trong 50 incident gần nhất; không tìm thấy không chứng minh không có sự cố.",
              "source": "https://www.githubstatus.com"
            }
          },
          {
            "event": "step",
            "step": 2,
            "finish": "stop"
          }
        ]
```

- Nguyên nhân có thể xảy ra:
  - Agent chưa xử lý đúng giới hạn dữ liệu mà tool cung cấp: chỉ có thể tra cứu trong 50 incident gần nhất.
  - Agent đã suy luận rằng không có incident trong năm 2025, trong khi dữ liệu hiện tại không đủ để đưa ra kết luận này.
  - Agent đưa các incident năm 2026 vào câu trả lời dù người dùng chỉ yêu cầu dữ liệu năm 2025, khiến câu trả lời thiếu tập trung và chưa đáp ứng yêu cầu

## 3. Thay đổi đã thực hiện

- Sửa gì, ở đâu:
  - Cập nhật SYSTEM_PROMPT trong file agent.py.
  - Bổ sung quy tắc yêu cầu agent kiểm tra phạm vi thời gian và giới hạn số lượng bản ghi trước khi đưa ra kết luận.
  - Yêu cầu phân biệt giữa việc không tìm thấy incident trong dữ liệu hiện có và việc thực tế không có incident.
  - Không đưa các incident ngoài khoảng thời gian được yêu cầu vào danh sách kết quả chính.

- Vì sao chọn thay đổi này:
  - Lỗi xuất phát từ cách agent diễn giải dữ liệu và giới hạn bằng chứng, không phải do lỗi thực thi tool.
  - Cập nhật system prompt là thay đổi tập trung, không cần sửa cấu trúc agent loop hay bộ công cụ.- Thay đổi giúp hạn chế kết luận sai khi người dùng truy vấn dữ liệu lịch sử nằm ngoài phạm vi snapshot hiện có.


## 4. So sánh hai lần chạy

| Chỉ số | `eval_before.json` | `eval_after.json` |
|---|---|---|
| Điểm tổng |  |  |
| Điểm case đã chọn |  |  |
| Số model calls |  |  |
| Token, chi phí nếu có |  |  |

- Lỗi đã giải quyết:
- Lỗi còn lại hoặc mới xuất hiện:

## 5. Giới hạn

Nêu giới hạn của điểm judge, của snapshot và của việc chỉ chạy một lần mỗi phiên bản.
