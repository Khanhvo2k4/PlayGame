# ADR-0004: Vị trí & định dạng file save
- Trạng thái: Chấp nhận
- Ngày: 2026-11-25

## Bối cảnh
Master Plan lưu `data/save.json` (cùng thư mục dữ liệu tĩnh) và phải nhớ xóa save trước khi phát hành. SkillGame (`Level_Save`) yêu cầu `schemaVersion`, atomic write, `.bak`, validate dữ liệu ngoài.

## Phương án
1. **`saves/save.json` cạnh exe** (gitignore), có `schemaVersion`, atomic write + `.bak`.
2. Giữ `data/save.json`.
3. `%APPDATA%/DinoSurvival/` — chuẩn Windows nhưng khó cho bản zip "portable".

## Quyết định
Phương án 1. Cấu trúc JSON giữ nguyên các khóa của kế hoạch, thêm `"schema_version": 1` ở gốc.

## Hệ quả
- (+) Bản Release không bao giờ dính save cá nhân; `data/` chỉ đọc.
- (+) Ghi `save.json.tmp` → flush → copy cũ sang `.bak` → rename; JSON hỏng → thử `.bak` → mặc định.
- (−) Thư mục game phải ghi được (zip giải nén ra Desktop/Documents: OK).
- Version cao hơn bản hiện tại → không ghi đè, báo lỗi thân thiện.

## Cách kiểm chứng
Test Catch2 `tests/cpp/test_save.cpp`: round-trip, file hỏng → `.bak`, thiếu khóa → mặc định, version cao → từ chối.
