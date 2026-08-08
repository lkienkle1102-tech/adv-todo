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

<!-- BEGIN:non-interactive-install-rule -->

# Non-interactive installation rule
- Khi cài đặt bất kỳ dependency, package, tool hoặc phần mềm nào, không chạy ngay lệnh cài đặt mặc định nếu lệnh đó có thể mở prompt hoặc giao diện tương tác trong terminal.
- Trước khi cài đặt, phải kiểm tra tài liệu hoặc tùy chọn `--help` của công cụ để tìm và sử dụng cờ non-interactive phù hợp (ví dụ: tự động xác nhận, chấp nhận mặc định hoặc tắt prompt) nhằm hoàn tất cài đặt mà không cần tương tác thủ công. Không tự suy đoán tên cờ; nếu công cụ không hỗ trợ chế độ non-interactive thì phải báo rõ trở ngại trước khi tiếp tục.

<!-- END:non-interactive-install-rule -->

<!-- BEGIN:project-skills-rule -->

# Project skills rule
- Khi làm việc với dự án backend hoặc frontend, bắt buộc khám phá và sử dụng các skill phù hợp trong `.agents/skills` để hiểu cấu trúc, quy ước và công cụ sẵn có của từng dự án, qua đó tái sử dụng workflow hiện có và cải thiện hiệu suất làm việc.
- Khi GitNexus MCP hoặc GitNexus CLI khả dụng, bắt buộc sử dụng GitNexus để khám phá codebase, quan hệ phụ thuộc và luồng gọi trước khi thực hiện thay đổi. Ưu tiên GitNexus MCP; nếu MCP không khả dụng thì sử dụng CLI.

<!-- END:project-skills-rule -->

<!-- BEGIN:gitnexus-path-execute -->

# GitNexus path execution rule
- Trước khi chạy bất kỳ thao tác GitNexus nào qua MCP hoặc CLI, bắt buộc chuyển working directory vào đúng thư mục dự án con cần phân tích: `be-adv-todo/` cho backend hoặc `fe-adv-todo/` cho frontend. Không được chạy hoặc khởi tạo GitNexus từ thư mục gốc `adv-todo/` hiện tại.
- Mỗi index `.gitnexus` chỉ được tồn tại trong thư mục dự án con tương ứng. Không được tạo hoặc sử dụng `.gitnexus` tại thư mục gốc `adv-todo/`; nếu phát hiện thư mục này ở root thì phải dừng thao tác GitNexus và báo cho người dùng trước khi xử lý tiếp.

<!-- END:gitnexus-path-execute -->

<!-- gitnexus:start -->
# GitNexus — Code Intelligence

This project is indexed by GitNexus as **adv-todo** (3541 symbols, 11386 relationships, 300 execution flows). Use the GitNexus MCP tools to understand code, assess impact, and navigate safely.

> If any GitNexus tool warns the index is stale, run `npx gitnexus analyze` in terminal first.

## Always Do

- **MUST run impact analysis before editing any symbol.** Before modifying a function, class, or method, run `gitnexus_impact({target: "symbolName", direction: "upstream"})` and report the blast radius (direct callers, affected processes, risk level) to the user.
- **MUST run `gitnexus_detect_changes()` before committing** to verify your changes only affect expected symbols and execution flows.
- **MUST warn the user** if impact analysis returns HIGH or CRITICAL risk before proceeding with edits.
- When exploring unfamiliar code, use `gitnexus_query({query: "concept"})` to find execution flows instead of grepping. It returns process-grouped results ranked by relevance.
- When you need full context on a specific symbol — callers, callees, which execution flows it participates in — use `gitnexus_context({name: "symbolName"})`.

## When Debugging

1. `gitnexus_query({query: "<error or symptom>"})` — find execution flows related to the issue
2. `gitnexus_context({name: "<suspect function>"})` — see all callers, callees, and process participation
3. `READ gitnexus://repo/adv-todo/process/{processName}` — trace the full execution flow step by step
4. For regressions: `gitnexus_detect_changes({scope: "compare", base_ref: "main"})` — see what your branch changed

## When Refactoring

- **Renaming**: MUST use `gitnexus_rename({symbol_name: "old", new_name: "new", dry_run: true})` first. Review the preview — graph edits are safe, text_search edits need manual review. Then run with `dry_run: false`.
- **Extracting/Splitting**: MUST run `gitnexus_context({name: "target"})` to see all incoming/outgoing refs, then `gitnexus_impact({target: "target", direction: "upstream"})` to find all external callers before moving code.
- After any refactor: run `gitnexus_detect_changes({scope: "all"})` to verify only expected files changed.

## Never Do

- NEVER edit a function, class, or method without first running `gitnexus_impact` on it.
- NEVER ignore HIGH or CRITICAL risk warnings from impact analysis.
- NEVER rename symbols with find-and-replace — use `gitnexus_rename` which understands the call graph.
- NEVER commit changes without running `gitnexus_detect_changes()` to check affected scope.

## Tools Quick Reference

| Tool | When to use | Command |
|------|-------------|---------|
| `query` | Find code by concept | `gitnexus_query({query: "auth validation"})` |
| `context` | 360-degree view of one symbol | `gitnexus_context({name: "validateUser"})` |
| `impact` | Blast radius before editing | `gitnexus_impact({target: "X", direction: "upstream"})` |
| `detect_changes` | Pre-commit scope check | `gitnexus_detect_changes({scope: "staged"})` |
| `rename` | Safe multi-file rename | `gitnexus_rename({symbol_name: "old", new_name: "new", dry_run: true})` |
| `cypher` | Custom graph queries | `gitnexus_cypher({query: "MATCH ..."})` |

## Impact Risk Levels

| Depth | Meaning | Action |
|-------|---------|--------|
| d=1 | WILL BREAK — direct callers/importers | MUST update these |
| d=2 | LIKELY AFFECTED — indirect deps | Should test |
| d=3 | MAY NEED TESTING — transitive | Test if critical path |

## Resources

| Resource | Use for |
|----------|---------|
| `gitnexus://repo/adv-todo/context` | Codebase overview, check index freshness |
| `gitnexus://repo/adv-todo/clusters` | All functional areas |
| `gitnexus://repo/adv-todo/processes` | All execution flows |
| `gitnexus://repo/adv-todo/process/{name}` | Step-by-step execution trace |

## Self-Check Before Finishing

Before completing any code modification task, verify:
1. `gitnexus_impact` was run for all modified symbols
2. No HIGH/CRITICAL risk warnings were ignored
3. `gitnexus_detect_changes()` confirms changes match expected scope
4. All d=1 (WILL BREAK) dependents were updated

## Keeping the Index Fresh

After committing code changes, the GitNexus index becomes stale. Re-run analyze to update it:

```bash
npx gitnexus analyze
```

If the index previously included embeddings, preserve them by adding `--embeddings`:

```bash
npx gitnexus analyze --embeddings
```

To check whether embeddings exist, inspect `.gitnexus/meta.json` — the `stats.embeddings` field shows the count (0 means no embeddings). **Running analyze without `--embeddings` will delete any previously generated embeddings.**

> Claude Code users: A PostToolUse hook handles this automatically after `git commit` and `git merge`.

## CLI

| Task | Read this skill file |
|------|---------------------|
| Understand architecture / "How does X work?" | `.claude/skills/gitnexus/gitnexus-exploring/SKILL.md` |
| Blast radius / "What breaks if I change X?" | `.claude/skills/gitnexus/gitnexus-impact-analysis/SKILL.md` |
| Trace bugs / "Why is X failing?" | `.claude/skills/gitnexus/gitnexus-debugging/SKILL.md` |
| Rename / extract / split / refactor | `.claude/skills/gitnexus/gitnexus-refactoring/SKILL.md` |
| Tools, resources, schema reference | `.claude/skills/gitnexus/gitnexus-guide/SKILL.md` |
| Index, status, clean, wiki CLI commands | `.claude/skills/gitnexus/gitnexus-cli/SKILL.md` |

<!-- gitnexus:end -->
