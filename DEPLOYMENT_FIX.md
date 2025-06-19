# Hướng dẫn sửa lỗi Deployment Cloudflare Pages

## Vấn đề
Lỗi: `No such module "node:buffer"` khi deploy lên Cloudflare Pages do thiếu compatibility flag cho Node.js modules.

## Giải pháp

### Cách 1: Cấu hình trong Cloudflare Dashboard (Khuyến nghị)

1. Truy cập Cloudflare Dashboard
2. Vào project Sink của bạn
3. Chọn **Settings** > **Functions** > **Compatibility flags**  
4. Thêm flag: `nodejs_compat`
5. Save và redeploy

### Cách 2: Sử dụng Environment Variables

Thêm environment variable trong Cloudflare Pages:
- Variable name: `COMPATIBILITY_FLAGS`
- Value: `nodejs_compat`

### Cách 3: Cấu hình Build

Đã cập nhật các file sau:
- `nuxt.config.ts`: Thêm nitro preset và compatibility flags
- `wrangler.json`: Cập nhật cho Pages deployment
- `functions/_worker.compat.js`: File cấu hình compatibility

### Các thay đổi đã thực hiện

1. **nuxt.config.ts**:
   ```typescript
   nitro: {
     preset: 'cloudflare-pages',
     cloudflare: {
       pages: {
         compatibilityFlags: ['nodejs_compat'],
         compatibilityDate: '2025-05-08'
       }
     }
   }
   ```

2. **wrangler.json**:
   - Thêm `pages_build_output_dir: "dist"`
   - Giữ nguyên `compatibility_flags: ["nodejs_compat"]`

## Kiểm tra trước khi deploy

1. Build local: `npm run build`
2. Preview: `npx wrangler pages dev dist`
3. Deploy: `npx wrangler pages deploy dist`

## Lưu ý
- Compatibility date phải là `2025-05-08` hoặc mới hơn
- Đảm bảo KV namespace ID đã được cấu hình đúng
- Nếu vẫn lỗi, kiểm tra logs để xem module nào đang thiếu 