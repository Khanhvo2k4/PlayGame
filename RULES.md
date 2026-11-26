# RULES.md — Luật bắt buộc của dự án DinoSurvival

> Đọc file này + `docs/PROJECT_CONTEXT.md` + `docs/adr/` trước mỗi phiên code (người hoặc AI).
> Ưu tiên khi xung đột: **An toàn > Toàn vẹn dữ liệu (save) > Kế hoạch đã duyệt > Tính đúng > Hiệu năng > Phong cách.**

## 1. Compatibility Matrix
C++17 · Visual Studio 2022 (MSVC v143, x64) · CMake ≥ 3.25 · vcpkg manifest · SDL2 / SDL2_image / SDL2_ttf / SDL2_mixer · tmxlite · nlohmann-json · Catch2 v3 · Python **3.11.x x64** · pybind11 2.12 · pytest · ruff · mypy.

## 2. DO / DON'T

| ✅ DO | ❌ DON'T |
|------|---------|
| `PYBIND11_EMBEDDED_MODULE(game_engine, m)` chỉ trong `src/bridge/PyBridge.cpp` (E1) | `PYBIND11_MODULE`, `import` module trong từng frame |
| Tính `newX/newY`, kiểm trục X rồi Y (E5) | Cộng thẳng tọa độ rồi sửa kẹt |
| Mọi chuyển động/timer dùng `dt` đã kẹp ≤ 0.05 (E6) | Hardcode `0.016` |
| `px/s = SPD × 50.0f` (E7) | Trộn SPD và px/s |
| `Mix_FadeOutMusic` + `Mix_HookMusicFinished` → cờ xử lý trên main thread (E3) | `SDL_Delay` trong game loop |
| `BOSS_ATTACK_RANGE`, đủ `_do_patrol` (E2) | Tên hằng sai / hàm thiếu thân |
| Số liệu gameplay đọc từ `data/balance.json` | Magic number rải rác |
| `from scripts.karma import karma_service` | `from ... import *` |
| `std::unique_ptr` mặc định, RAII | `new/delete` thô, `strcpy/sprintf` |
| Bọc lời gọi Python bằng `try/catch (py::error_already_set&)` | Để lỗi script làm sập engine |
| Python runtime chỉ dùng stdlib | Import gói pip trong `scripts/` |
| Save: `schema_version`, atomic write + `.bak` | Ghi đè trực tiếp `save.json` |
| Object Pool cho VFX/hạt; cache texture chữ | Alloc/tạo texture mỗi frame |

## 3. Tổ chức mã (chia để trị)
- C++: mỗi thư mục `src/<miền>/` một trách nhiệm; phụ thuộc 1 chiều (xem `docs/ARCHITECTURE.md`).
- Python: mỗi miền `scripts/<miền>/` có `constants.py`, `models.py`, `*_service.py`, `utils.py`, `exceptions.py` khi cần.
- Hàm ≤ ~40 dòng, file ≤ ~400 dòng, nesting ≤ 3; tên có đơn vị (`cooldown_sec`, `range_px`).
- C++: `PascalCase` file/class, `m_` cho member, `camelCase` hàm. Python: PEP 8, type hints đầy đủ.

## 4. Quy trình làm việc
1. Việc vừa/lớn (nhiều file, đổi API bridge, đổi format save/data, thêm thư viện) → viết kế hoạch ngắn, duyệt rồi mới code.
2. Vòng lặp: **Viết → Build/Lint → Test → Tự review → Sửa** (tối đa 3 vòng, vẫn lỗi thì dừng và báo).
3. Không tuyên bố "đã chạy/đã test" khi chưa chạy thật.
4. Đổi thư viện/timestep/format save → viết ADR mới trong `docs/adr/`.

## 5. Kiểm thử
- **Bắt buộc:** toán & AABB, A*, LavaSystem, Save/Load, FSM AI, `resolve_ending`.
- **Miễn trừ unit test (kiểm thủ công + mô tả bước):** game feel, camera, VFX, UI.
- Test tất định: seed RNG cố định, `dt` truyền vào, không đọc đồng hồ thật.

## 6. Git & tài nguyên
- Conventional Commits: `feat|fix|refactor|perf|test|docs|chore|build(scope): mô tả`.
- Ảnh/âm thanh/font chỉ commit khi đã được Git LFS theo dõi (`git check-attr filter -- <file>`).
- Không sửa bản gốc trong `D:\PlanProjectGame\GAME\`; làm trên `art_src/` và `assets/`.
- Không chạy lệnh phá hủy (`git reset --hard`, `push --force`, xóa thư mục asset) khi chưa xác nhận.
