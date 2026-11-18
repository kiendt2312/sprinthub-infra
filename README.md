# sprinthub-infra

Hạ tầng của **SprintHub**: Docker Compose, Caddy, deploy lên Oracle Always Free,
backup lên Cloudflare R2, E2E Playwright. Thiết kế nằm ở
[sprinthub-be](https://github.com/kiendt2312/sprinthub-be).

```text
base/        compose, Caddy, redis.conf, cloud-init
envs/prod/   override + image tags (mỗi lần deploy là một PR)
scripts/     deploy, backup, restore drill, prod-like
e2e/         Playwright
```

License: MIT.
