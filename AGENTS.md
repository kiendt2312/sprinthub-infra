# Agent Instructions — sprinthub-infra

Hạ tầng và deploy của **SprintHub**. Repo trung tâm là `../sprinthub-be`: mở session
ở đó; thiết kế deploy ở `../sprinthub-be/docs/design/design.md` (mục Persistence,
backup, Redis client và deploy).

Trạng thái: **chỉ có khung thư mục**.

## Cấu trúc

| Đường dẫn | Nội dung |
| --- | --- |
| `base/` | `compose.yaml` chung, Caddyfile, `redis.conf`, cloud-init cho VM |
| `envs/prod/` | `compose.override.yaml`, `image-tags.env` (SHA image của api, worker, web, admin-ui, admin-service) |
| `scripts/` | deploy (SSH), backup lên R2, restore drill, `prod-like` |
| `e2e/` | Playwright chạy trên toàn bộ stack (EN và VI) |

## Quy tắc

- Luồng **local → prod**. Mỗi lần deploy là một PR đổi `envs/prod/image-tags.env`;
  PR phải qua kiểm tra tạm trong CI (compose + seed + E2E + migration +
  `FUNCTION LOAD`) và được duyệt tay.
- Thứ tự deploy: backup → migration expand → `FUNCTION LOAD REPLACE` → đổi `api` +
  `worker` rồi `web` → health gate 60 s → tự rollback nếu fail.
- Prod: 1 VM Oracle Always Free (Singapore, arm64). Image phải multi-arch.
- **Không commit secret.** Secret nằm trong GitHub Environment `production`, job
  deploy ghi ra `.env` trên VM.
- Backup prod (mã hóa) chỉ ở VM và Cloudflare R2; không đưa vào runner CI.
- Restore drill chạy trên VM vào Redis tạm.

## Git

Trunk-based, public, MIT. Danh tính commit: `kiendt <kiendt2312.work@gmail.com>`.
Không push hay tạo PR khi chưa được yêu cầu.
