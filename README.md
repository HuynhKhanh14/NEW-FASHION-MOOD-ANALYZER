# Fashion Mood Analyzer — Docker (Frontend + Backend)

Tách dự án TanStack Start gốc thành 2 service chạy độc lập:

- **frontend/** — React + Vite (SPA), build static, serve bằng Nginx (port `8081`)
- **backend/**  — Node + Express, expose `POST /api/sentiment` gọi Lovable AI Gateway (port `8080`)

CORS được bật ở backend → frontend gọi backend qua `VITE_API_URL`.

## 1. Chuẩn bị

```bash
cp .env.example .env
# Sửa LOVABLE_API_KEY (bắt buộc).
# Nếu deploy ra domain thật, sửa VITE_API_URL = https://api.your-domain.com
# và CORS_ORIGIN = https://your-domain.com (hoặc danh sách phân tách bằng dấu phẩy).
```

## 2. Chạy bằng Docker Compose

```bash
docker compose up --build
```

- Frontend: http://localhost:8081
- Backend health: http://localhost:8080/health

> Lưu ý: `VITE_API_URL` được **bake vào bundle lúc build**. Nếu thay đổi giá trị
> này, phải build lại frontend: `docker compose build frontend`.

## 3. Chạy thủ công (không Docker)

Backend:
```bash
cd backend
npm install
LOVABLE_API_KEY=xxx npm run dev
```

Frontend:
```bash
cd frontend
bun install   # hoặc npm install
echo "VITE_API_URL=http://localhost:8080" > .env
bun run dev   # hoặc npm run dev
```

## 4. Lấy `LOVABLE_API_KEY`

Tạo trong Lovable Workspace Settings → AI Gateway, hoặc thay handler trong
`backend/server.js` để dùng provider khác (OpenAI, Gemini, Anthropic…).

## 5. Triển khai production gợi ý

- Backend: bất kỳ host Node nào (Fly.io, Render, Railway, VPS Docker…).
- Frontend: bundle static có thể đẩy lên Vercel/Netlify/Cloudflare Pages,
  hoặc giữ Nginx container như compose này.
- Nhớ đặt `CORS_ORIGIN` thành đúng domain frontend, không để `*` ở production.
