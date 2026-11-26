# ADR-0001: Khóa công nghệ Custom C++17 (SDL2) + Python 3.11 nhúng (pybind11)
- Trạng thái: Chấp nhận
- Ngày: 2026-11-25

## Bối cảnh
Master Plan chọn C++ cho vòng lặp real-time/đồ họa/va chạm/A* và Python cho AI, tiến hóa, nghiệp quả, hội thoại.

## Phương án
1. **C++17 + SDL2 + Python nhúng (pybind11)** — kiểm soát hoàn toàn, AI sửa nhanh không compile lại. Nhược: đóng gói Python phức tạp.
2. Engine thương mại (Godot/Unity) — nhanh hơn nhưng lệch mục tiêu học tập và lệch toàn bộ kế hoạch.
3. C++ thuần (Lua/không script) — đơn giản đóng gói nhưng mất tốc độ iterate AI.

## Quyết định
Chọn phương án 1. Khóa **SDL2 (không SDL3)**, Python **3.11.x x64** khớp chính xác giữa lúc build và bản embeddable lúc phát hành.

## Hệ quả
- (+) Ranh giới rõ: `src/bridge/` là nơi duy nhất chạm Python.
- (−) Bắt buộc Bước 6.3 đóng gói `python311.dll` + `python311.zip` + `._pth`.
- Cấm dùng API engine thương mại; Python runtime không import thư viện ngoài stdlib.

## Cách kiểm chứng
Bước 1.1: `DinoSurvival.exe` in phiên bản SDL2 và `sys.version` = 3.11.x, thoát mã 0.
