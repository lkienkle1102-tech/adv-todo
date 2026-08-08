<!-- BEGIN:nextjs-agent-rules -->

# This is NOT the Next.js you know

This version has breaking changes — APIs, conventions, and file structure may all differ from your training data. Read the relevant guide in `node_modules/next/dist/docs/` (resolved from this file's directory; in monorepos the `next` package may not be visible from the repo root) before writing any code. Heed deprecation notices.

This block is written and re-added by `next dev` — verify at `node_modules/next/dist/server/lib/generate-agent-files.js`. Removing it from a diff only re-creates the uncommitted change; committing it with your work keeps the tree clean.

<!-- END:nextjs-agent-rules -->

<!-- BEGIN:frontend-rules -->

# Form rules
- Form nhập liệu dùng `useActionState` (React 19) thay `useState` rời rạc quản field + pending + error.
- Validate input bằng `zod` (`safeParse`) trong action function, không validate thủ công trong component.

# Router + locale rule
- Mọi navigate/link nội bộ (`Link href`, `router.push`, `router.replace`) dùng `Link`/`useRouter` từ `@/features/i18n/navigation` (KHÔNG import trực tiếp từ `next/link`/`next/navigation`). Wrapper tự phát hiện app có đang chạy trong `app/[locale]` hay không (đọc `useParams().locale`) và tự nhúng prefix locale vào path đích — component viết `router.push("/login")`/`<Link href="/login">` như route phẳng bình thường, không tự ghép `/${locale}` thủ công.
- Nhúng locale chỉ áp dụng cho path nội bộ (bắt đầu bằng `/`, không phải `//` — tức không phải URL tuyệt đối/domain khác). Link ra ngoài domain khác giữ nguyên, không bị nhúng locale sai.
- Đổi ngôn ngữ (LanguageSwitcher) vẫn dùng `useRouter()` gốc từ `next/navigation`, tự thay prefix locale trong pathname rồi push — không dùng wrapper `useRouter` cho case này (case này cần đổi locale, không giữ locale hiện tại).
- `useTranslation()` đọc `locale` từ `useParams()` (route segment), không đọc từ Zustand — tránh lệch giá trị lúc mount đầu (nguyên nhân gây chớp ngôn ngữ). Zustand (`useLocaleStore`) chỉ dùng để side-effect ngoài React tree đọc được (`api-client.ts`), sync một chiều từ URL qua `LocaleSync`, không phải nguồn đọc cho UI.

# Dependency version rule
- Mọi dependency phải pin đúng version (không `^`/`~`). `.npmrc` đã có `save-exact=true` nhưng không tự áp dụng cho version đã ghi tay trong `package.json` — sau `pnpm add <pkg>`, luôn mở `package.json` kiểm tra lại dòng vừa thêm không có dấu `^`/`~` trước khi coi là xong việc.

# Storybook rule
- Khi thực hiện công việc frontend, bắt buộc chạy Storybook và sử dụng skill `storybook` để khám phá dự án frontend, kiểm tra các story/component hiện có và ưu tiên tái sử dụng component phù hợp trước khi tạo component mới.

# Frontend browser testing rule
- Khi Chrome DevTools MCP khả dụng, bắt buộc mở ứng dụng trên đó và dùng Chrome DevTools MCP để kiểm thử thay đổi frontend.
- Khi Playwright khả dụng, bắt buộc chạy kiểm thử frontend bằng Playwright. Nếu cả Chrome DevTools MCP và Playwright đều khả dụng thì phải chạy cả hai, không được coi công cụ này là thay thế cho công cụ kia.

<!-- END:frontend-rules -->

<!-- BEGIN:rtk-rule -->

# RTK rule
- Nếu máy tính đã cài đặt và có thể sử dụng `rtk`, bắt buộc ưu tiên dùng `rtk` cho các tác vụ được hỗ trợ nhằm giảm lượng token tiêu thụ.

<!-- END:rtk-rule -->

<!-- BEGIN:project-skills-rule -->

# Project skills rule
- Khi làm việc với dự án backend hoặc frontend, bắt buộc khám phá và sử dụng các skill phù hợp trong `.agents/skills` để hiểu cấu trúc, quy ước và công cụ sẵn có của từng dự án, qua đó tái sử dụng workflow hiện có và cải thiện hiệu suất làm việc.

<!-- END:project-skills-rule -->
