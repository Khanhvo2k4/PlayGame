# ADR-0003: Lấy pybind11 từ pip (venv Python 3.11) thay vì port vcpkg
- Trạng thái: Đề xuất (kiểm chứng ở Bước 1.1)
- Ngày: 2026-11-25

## Bối cảnh
Master Plan đặt `pybind11` trong `vcpkg.json`. Port vcpkg `pybind11` phụ thuộc port `python3` của vcpkg → build thêm một CPython riêng (lâu) và có thể **lệch phiên bản** với Python 3.11 dùng để test/đóng gói. Máy hiện có cả Python 3.12 (mặc định PATH) và 3.11.

## Phương án
1. **pip `pybind11` trong `.venv` 3.11** + `-Dpybind11_DIR=<python -m pybind11 --cmakedir>` + `-DPython3_EXECUTABLE=.venv/Scripts/python.exe`.
2. Port vcpkg `pybind11` (+ `python3` của vcpkg).

## Quyết định
Phương án 1: một nguồn Python 3.11 duy nhất cho build, test và đóng gói.

## Hệ quả
- (+) Không build CPython; phiên bản runtime khớp bản embeddable 3.11.
- (−) Phải truyền 2 biến CMake (đặt sẵn trong `CMakePresets.json`).
- Nếu kiểm chứng thất bại → chuyển phương án 2 bằng ADR mới.

## Cách kiểm chứng
`cmake --preset debug` log `Found Python3: ...Python311...` và `Found pybind11 v2.12.0`.
