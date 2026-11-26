# ADR-0005: Tổ chức tài nguyên & animation hướng dữ liệu
- Trạng thái: Chấp nhận
- Ngày: 2026-11-25

## Bối cảnh
Ảnh thực tế trong `D:\PlanProjectGame\GAME\` là **mỗi hành động 1 file sprite sheet ngang** (vd `rex_run.png` 6 frame), có 5 file `.webp`, và `map_concept_art.png` chỉ để tham khảo.

## Quyết định
- `art_src/` giữ bản gốc + tham khảo + prompt (không ship); `assets/` chỉ chứa file runtime chuẩn (PNG RGBA, WAV, OGG, TTF), chia thư mục theo nhân vật/nhóm.
- Thông tin clip (file, frameCount, frameDelay, loop) đặt trong `data/animations.json`; C++ tự tính `frameW = texW / frameCount` (chống ảnh AI sai kích thước).
- Thiếu ảnh → `ResourceManager` sinh Placeholder màu theo bảng 0.4.C của kế hoạch, không crash.

## Hệ quả
- (+) Thêm/sửa animation không compile lại C++.
- (−) Thêm 1 file dữ liệu cần validate (`data/schemas/animations.schema.json`).

## Cách kiểm chứng
Xóa tạm 1 PNG → game vẫn chạy với hộp màu; sửa `frameDelay` trong JSON → animation đổi tốc độ sau F6.
