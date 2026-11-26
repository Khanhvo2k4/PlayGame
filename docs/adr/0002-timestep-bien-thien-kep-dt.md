# ADR-0002: Timestep biến thiên có kẹp `dt ≤ 0.05s`
- Trạng thái: Chấp nhận
- Ngày: 2026-11-25

## Bối cảnh
SkillGame khuyến nghị fixed timestep + accumulator. Master Plan dùng `dt` biến thiên kẹp `MAX_DT = 0.05f` (Errata E6), toàn bộ code mẫu (Python AI, Camera lerp, Lava) viết theo `dt`.

## Phương án
1. **Biến thiên + kẹp 0.05s** — khớp 100% kế hoạch, đơn giản. Nhược: không tất định tuyệt đối.
2. Fixed 60 Hz + nội suy render — tất định, nhưng phải viết lại mẫu và thêm nội suy vị trí.

## Quyết định
Phương án 1 cho toàn bộ dự án (game không có netcode/replay). Mọi chuyển động & timer nhân `dt`; không hardcode `0.016`.

## Hệ quả
- (+) Bám sát kế hoạch, ít code.
- (−) Nếu sau này thêm replay/rollback → cần ADR mới chuyển sang fixed step.
- Camera dùng damping độc lập framerate: `t = 1 - exp(-k·dt)` (tốt hơn `min(1, 10·dt)`).

## Cách kiểm chứng
Log `dt` ≈ 0.0167; kéo cửa sổ 2s → nhân vật không xuyên tường.
