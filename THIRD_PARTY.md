# Nguồn bên ngoài

Khác các repo `bow-skill-*` chép skill **nguyên văn** từ hãng: phần lớn skill ở đây là bản team Bow
**chuyển thể** từ nguồn mở (đổi mô tả, dịch, thêm quy ước của team) rồi dùng trong `bow-skill-flutter`
trước khi tách ra thành bộ này. File này ghi skill nào chuyển thể từ đâu và giấy phép của nguồn.

Cách xác minh (2026-10-02): so từng skill với bản cùng tên ở bản clone local của hai nguồn —
tiêu đề mục + câu đặc trưng + `diff`. Bản clone là shallow (1 commit) nên **không** tra được commit
mà team đã lấy lúc chuyển thể: cột "Ghi chú" chỉ ghi commit đã dùng để ĐỐI CHIẾU.

- addyosmani/agent-skills: `9d0c60d406b454a78ccc0a175b19932047aa4dac` (2026-10-01)
- obra/superpowers: `8ca22dba9a94f28898bbce59f2537ff4d87c747d` (2026-09-25)

| Skill | Chuyển thể từ | Giấy phép nguồn | Ghi chú |
| --- | --- | --- | --- |
| `code-simplification` | [addyosmani/agent-skills](https://github.com/addyosmani/agent-skills) `skills/code-simplification` | MIT | Thân bài trùng bản đối chiếu; chỉ `description` do team viết lại. Nguồn tự ghi "Inspired by the Claude Code Simplifier plugin" (anthropics/claude-plugins-official — giấy phép: chưa kiểm) |
| `debugging-and-error-recovery` | addyosmani/agent-skills `skills/debugging-and-error-recovery` | MIT | `description` do team viết lại; thân bài khác bản đối chiếu 4 dòng (3 dòng bản đối chiếu có thêm ghi chú "dùng lệnh test của chính repo"; 1 dòng "Steps 4-10" ↔ "Steps 4-6") |
| `planning-and-task-breakdown` | addyosmani/agent-skills `skills/planning-and-task-breakdown` | MIT | `description` do team viết lại; không có mục "Output Files" / "See Also" mà bản đối chiếu có |
| `security-and-hardening` | addyosmani/agent-skills `skills/security-and-hardening` | MIT | `description` do team viết lại. Cùng khung (Overview, Threat Model First, Three-Tier Boundary System, Red Flags, Verification) nhưng ví dụ code nằm thẳng trong `SKILL.md`; bản đối chiếu đã dời chúng sang `references/hardening-patterns.md` |
| `source-driven-development` | addyosmani/agent-skills `skills/source-driven-development` | MIT | KHÔNG sửa — `SKILL.md` trùng từng byte với bản đối chiếu. Lấy qua `bow-skill-flutter-api`, nơi ghi commit gốc `2686b620fc1fed2e8f60c704839c766b8594c6b6` (chưa kiểm lại). Giữ file `LICENSE` trong thư mục skill |
| `spec-driven-development` | addyosmani/agent-skills `skills/spec-driven-development` | MIT | `description` do team viết lại; không có "Phase 0: Scope Check" và các đoạn về `tasks/plan.md` mà bản đối chiếu có |
| `test-driven-development` | addyosmani/agent-skills `skills/test-driven-development` | MIT | `description` do team viết lại; không có mục "Discover the Stack First" mà bản đối chiếu có. (obra/superpowers cũng có skill cùng tên — nội dung KHÁC, không phải nguồn) |
| `finishing-a-development-branch` | [obra/superpowers](https://github.com/obra/superpowers) `skills/finishing-a-development-branch` | MIT | Viết lại bằng tiếng Việt theo cùng khung (verify → xác nhận môi trường → trình lựa chọn → thực thi → dọn dẹp); các lựa chọn đổi theo luồng của team (`bow-commit`, MR vào `develop`) |
| `receiving-code-review` | obra/superpowers `skills/receiving-code-review` | MIT | Viết lại bằng tiếng Việt: giữ mẫu phản hồi 6 bước và danh sách câu cấm; thêm ví dụ và mục du-quest của team |
| `verify-runtime-not-just-static-green` | obra/superpowers `skills/verification-before-completion` | MIT | ĐỔI TÊN + viết lại bằng tiếng Việt: giữ "luật sắt" và "cổng kiểm tra" 5 bước; thêm bảng verify theo từng tầng và phần hook của team |
| `bow-lean` | [DietrichGebert/ponytail](https://github.com/DietrichGebert/ponytail) — theo dòng ghi nguồn ở đầu skill | MIT | Giấy phép đọc từ file `LICENSE` của nguồn tại `e3ba2aa6f1e6f0bc4d69eb09c9f0d0a93af56156 (2026-09-14)`. Nội dung CHƯA so từng dòng với nguồn — team đã chuyển thể và tự theo dõi bản mới của nguồn |
| `bow-lean-review` | `ponytail-review` của cùng dự án — theo dòng ghi nguồn ở đầu skill | MIT | Như trên |
| `bow-log-subtask`, `impact-sweep`, `jira-triage-bug-qc` | — (team Bow tự viết) | — | Không thấy bản cùng tên / cùng khung ở hai nguồn đã đối chiếu |

Văn bản giấy phép: [`LICENSES/addyosmani-agent-skills-MIT.txt`](LICENSES/addyosmani-agent-skills-MIT.txt),
[`LICENSES/obra-superpowers-MIT.txt`](LICENSES/obra-superpowers-MIT.txt),
[`LICENSES/ponytail-MIT.txt`](LICENSES/ponytail-MIT.txt).

"Thân bài khác bản đối chiếu" ở bảng trên: **chưa kiểm** được phần khác đó do nguồn đổi sau khi team
lấy, hay do team sửa — cần lịch sử đầy đủ của nguồn mới biết.

## Team đã đổi gì khi tách sang bộ này

Chỉ gỡ dấu vết của một sản phẩm cụ thể, không viết lại nội dung:

- `<PROJECT_KEY>` đóng vai tên sản phẩm → bỏ / thay bằng "dự án"; giữ khi là mã Jira trong mẫu nhánh / ticket.
- Mã ticket thật, tên hàm / bảng / file của sản phẩm trong các "bài học từ sự cố" → viết lại dạng tổng quát.
- Đường dẫn monorepo (`apps/mobile`, `apps/admin`) → tổng quát, giữ làm ví dụ.
- Cổng riêng của repo (guard-push hook, quest gate, Stop hook `self-verify-rubric.sh`,
  `scripts/quest-checklist.sh`, `docs/conventions/common.md`) → có điều kiện "nếu repo có".
- Bỏ tham chiếu tới memory và skill riêng của sản phẩm.

`security-and-hardening`, `source-driven-development`: không đổi dòng nào.

## Đã soát gì (2026-10-02)

- Không có script / file thực thi — chỉ Markdown và một file `LICENSE`.
- Không có ký tự vô hình (tag Unicode, zero-width, bidi).
- Không có lệnh cài đặt kéo từ mạng (`curl … | sh`), không có chỉ dẫn gửi dữ liệu ra ngoài.
- Link ngoài: `github.com/DietrichGebert/ponytail` (ghi nguồn), `github.com/anthropics/claude-plugins-official`
  (ghi nguồn), `genai.owasp.org` (OWASP Top 10 cho LLM), `react.dev` và vài tên miền tài liệu khác làm
  ví dụ trích nguồn trong `source-driven-development`.
- `source-driven-development` bảo agent **tải trang tài liệu chính thức** của framework đang dùng — đó
  là mục đích của skill; nó cũng dặn coi nội dung tải về là dữ liệu, không phải chỉ dẫn.
- Tham chiếu tới thứ KHÔNG có trong bộ này (mang theo từ nguồn, không làm hỏng gì):
  `references/security-checklist.md` (`security-and-hardening`), `references/testing-patterns.md` và
  skill `browser-testing-with-devtools` (`test-driven-development`), skill `incremental-implementation`
  và `context-engineering` (`spec-driven-development`).

## Cập nhật từ nguồn

Bộ này là bản chuyển thể nên KHÔNG chép đè được. Khi muốn lấy thay đổi mới của nguồn:

```bash
git clone https://github.com/addyosmani/agent-skills /tmp/up-addy && git -C /tmp/up-addy rev-parse HEAD
git clone https://github.com/obra/superpowers /tmp/up-obra && git -C /tmp/up-obra rev-parse HEAD
# xem nguồn đã đổi gì kể từ commit đối chiếu ghi ở đầu file, ĐỌC phần khác biệt, rồi chọn đưa tay từng ý
git -C /tmp/up-addy log -p 9d0c60d406b454a78ccc0a175b19932047aa4dac..HEAD -- skills/test-driven-development
diff /tmp/up-addy/skills/source-driven-development/SKILL.md skills/source-driven-development/SKILL.md
```
Sửa commit đối chiếu ở đầu file, tăng `version` trong `bow-skill.json`, gắn tag mới.
