# bow-skill-process

Bộ skill **quy trình làm việc**, dùng với [bow-agent](https://github.com/Bow-T/bow-agent). Bộ này
không gắn với công nghệ nào: nó dạy agent *làm việc thế nào* (lập spec, chia việc, viết test trước,
gỡ lỗi tìm gốc rễ, rà phạm vi ảnh hưởng, kiểm chứng trước khi báo xong, chốt nhánh, nhận review…),
còn *viết code thế nào* thuộc về các bộ công nghệ (Flutter, Next.js, React Native, Supabase…).
Một dự án dùng nó CÙNG với bộ công nghệ của mình.

## Cấu trúc

```
bow-skill-process/
├── bow-skill.json        # manifest: id "process" + quy tắc tự nhận diện repo
├── THIRD_PARTY.md        # skill nào chuyển thể từ đâu, giấy phép gì, đã đổi gì
├── LICENSES/             # văn bản giấy phép của nguồn bên ngoài
└── skills/
    ├── spec-driven-development/                # trước khi code
    ├── bow-ticket-spec/
    ├── planning-and-task-breakdown/
    ├── bow-log-subtask/
    ├── jira-triage-bug-qc/
    ├── bow-lean/                               # trong khi code
    ├── test-driven-development/
    ├── source-driven-development/
    ├── security-and-hardening/
    ├── debugging-and-error-recovery/
    ├── impact-sweep/                           # trước khi báo xong
    ├── verify-runtime-not-just-static-green/
    ├── bow-lean-review/
    ├── code-simplification/
    ├── finishing-a-development-branch/         # chốt việc
    └── receiving-code-review/
```

| Skill | Dùng khi | Nguồn |
| --- | --- | --- |
| `spec-driven-development` | bắt đầu tính năng / thay đổi lớn mà chưa có spec, yêu cầu còn mơ hồ | chuyển thể từ addyosmani/agent-skills (MIT) |
| `bow-ticket-spec` | ticket / epic Jira nhiều AC, nhiều comment, dẫn sang Figma / tài liệu, hoặc đã bị reopen — đọc HẾT nguồn, ra bảng vế có nguồn dẫn + kịch bản Given/When/Then + câu hỏi mở kèm mặc định | team Bow, lấy ý tưởng từ SpecsGraph (Apache-2.0) |
| `planning-and-task-breakdown` | đã có spec, cần chia thành các việc nhỏ làm được và kiểm được | chuyển thể từ addyosmani/agent-skills (MIT) |
| `bow-log-subtask` | ticket lớn đáng tách — in danh sách subtask để người dùng tự đưa lên Jira | team Bow |
| `jira-triage-bug-qc` | nhận MỘT ticket Jira: Task hay Bug, QC log đã đủ / đúng chưa | team Bow |
| `bow-lean` | sắp thêm code / thư viện / abstraction — cân nhắc có cần viết không, viết bằng cách rẻ nhất | team Bow, chuyển thể từ ponytail (MIT) |
| `test-driven-development` | viết logic, sửa bug, đổi hành vi — test đỏ trước, code sau | chuyển thể từ addyosmani/agent-skills (MIT) |
| `source-driven-development` | viết code theo framework / thư viện — bám tài liệu chính thức đúng phiên bản, có trích nguồn | addyosmani/agent-skills (MIT), không sửa |
| `security-and-hardening` | tính năng nhận dữ liệu không tin cậy, đăng nhập / phiên, secret, tích hợp bên thứ ba | chuyển thể từ addyosmani/agent-skills (MIT) |
| `debugging-and-error-recovery` | test đỏ, build hỏng, lỗi runtime — tìm gốc rễ thay vì đoán | chuyển thể từ addyosmani/agent-skills (MIT) |
| `impact-sweep` | sửa code dùng chung, đổi signature / enum / schema — quét hết nơi bị ảnh hưởng | team Bow |
| `verify-runtime-not-just-static-green` | ngay trước khi nói "xong / đã fix / pass" — phải có bằng chứng chạy thật | team Bow, chuyển thể từ obra/superpowers (MIT) |
| `bow-lean-review` | soi diff tìm phần over-engineering có thể cắt (không soi bug / security) | team Bow, chuyển thể từ ponytail (MIT) |
| `code-simplification` | code chạy đúng nhưng khó đọc — làm gọn mà không đổi hành vi | chuyển thể từ addyosmani/agent-skills (MIT) |
| `finishing-a-development-branch` | code xong — verify, rồi HỎI người dùng chọn hướng tích hợp (push / mở MR / để đó) | team Bow, chuyển thể từ obra/superpowers (MIT) |
| `receiving-code-review` | nhận góp ý review — kiểm chứng từng ý với code thật trước khi sửa | team Bow, chuyển thể từ obra/superpowers (MIT) |

14 skill tách ra từ `bow-skill-flutter`, `source-driven-development` lấy từ `bow-skill-flutter-api`.
Khi tách đã gỡ phần riêng của một sản phẩm (mã ticket, tên hàm / bảng / file cụ thể, đường dẫn
monorepo) và giữ nguyên bài học — chi tiết ở `THIRD_PARTY.md`. Vài điều nên biết:

- **Ví dụ vẫn mang màu công nghệ.** Quy trình thì dùng chung, nhưng ví dụ trong skill lấy từ nơi nó
  sinh ra: Flutter + Supabase ở các skill của team, TypeScript / npm ở các skill chuyển thể từ
  addyosmani. Gặp stack khác thì đổi lệnh theo repo đang làm.
- **Cổng riêng của repo là có điều kiện.** Guard-push hook, quest gate, Stop hook
  `self-verify-rubric.sh`, `scripts/quest-checklist.sh` chỉ áp dụng **khi repo có**. du-quest (hệ
  chấm điểm chung của công ty) vẫn được nhắc ở `bow-lean`, `code-simplification`,
  `test-driven-development`, `receiving-code-review`.
- **Tham chiếu sang bộ khác.** Vài skill trỏ tới `[[bow-commit]]` (bộ `commit`), `[[flutter-realtime]]`
  / `[[bow-model]]` / `[[bow-i18n]]` (bộ `flutter`), `[[supabase-security-review]]` (bộ `supabase`).
  Dự án không dùng các bộ đó thì đây chỉ là tên, không làm hỏng gì.
- **Jira chỉ đọc.** `bow-log-subtask`, `bow-ticket-spec` và `jira-triage-bug-qc` cố ý KHÔNG ghi lên Jira — chỉ đọc
  ticket rồi in kết quả ra chat để người dùng tự đưa lên.

## Tự nhận diện

`targets.detect` khai `{ "path": ".git" }`: repo git nào cũng khớp, nên bow-agent tự chọn bộ này cho
mọi dự án chưa khai công nghệ — quy trình làm việc không phụ thuộc repo viết bằng gì. Không muốn
dùng thì bỏ tick ở ô **Công nghệ** (nhóm "Dùng chung mọi công nghệ").

## Đăng ký vào bow-agent

Bộ này khai ở `addons` của `~/.bow-agent/registry.json` — nó hiện ở nhóm "Dùng chung mọi công nghệ"
trong ô **Công nghệ**, không nằm trong danh sách stack (bow chỉ tải repo có trong registry, đúng tag
đã ghim):

```json
{ "id": "process", "label": "Quy trình làm việc", "repo": "github.com/Bow-T/bow-skill-process", "ref": "v1.0.0" }
```

Lưu ý trong lúc chuyển tiếp: bundle cũ "Flutter + Supabase" (`bow-skill-flutter`) còn mang 14 skill
cùng tên, và `bow-skill-flutter-api` còn mang `source-driven-development` cùng một bản
`debugging-and-error-recovery` riêng cho Flutter + REST. Trùng tên skill thì nguồn đứng TRƯỚC trong
danh sách đã chọn giữ tên — stack đứng trước bộ ghép, nên chừng nào stack còn mang bản cũ thì bản ở
đây nhường. Repo đích đã fork riêng một skill thì khai tên vào `.claude/skills/.bow-skip`.

Thử local trước khi đẩy GitHub: `"repo": "file:///đường/dẫn/tới/bow-skill-process"`.

## Phát hành phiên bản mới

Sửa skill qua PR → tăng `version` trong `bow-skill.json` → gắn tag (`git tag v1.0.1 && git push --tags`)
→ cập nhật `ref` của mục `process` trong registry. bow tải theo tag và cache theo `<id>@<ref>`, nên
sửa nội dung mà giữ nguyên tag sẽ KHÔNG được tải lại. Lấy thay đổi mới từ nguồn bên ngoài: xem cuối
`THIRD_PARTY.md`.
