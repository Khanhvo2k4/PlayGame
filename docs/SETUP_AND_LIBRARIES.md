# 📦 SETUP & LIBRARIES — Thư viện cần tải & Setup ban đầu

> Bám sát [ke-hoach-game-master.md](file:///D:/PlanProjectGame/ke-hoach-game-master.md) (Bước 1.1, 1.11, 1.12, 6.1, 6.3) · Công nghệ: **C++17 + Python 3.11**.
> Gốc dự án: `d:\ProjectGame\`. Mọi lệnh chạy trong **PowerShell** tại gốc dự án trừ khi ghi khác.

---

## 0. Tình trạng máy hiện tại (đã kiểm tra 25/11/2026)

| Thành phần | Trạng thái | Việc cần làm |
|------------|-----------|--------------|
| Visual Studio 2022 Community | ✅ Đã cài — ❌ **THIẾU workload C++** (không tìm thấy MSVC x64) | Mục 3.1 |
| Python 3.12.10 x64 | ⚠️ Đang là `python` mặc định trong PATH | Không dùng cho dự án |
| Python 3.11 | ✅ Có sẵn tại `C:\Users\vovan\AppData\Local\Programs\Python\Python311\` | Tạo `.venv` từ bản này (Mục 3.4) |
| Git 2.52 + Git LFS 3.7.1 | ✅ Sẵn sàng | Chạy `git lfs install` (Mục 3.6) |
| CMake | ❌ Chưa có trong PATH | Mục 3.2 |
| vcpkg | ❌ Chưa có (`C:\vcpkg` không tồn tại) | Mục 3.3 |
| Tiled Map Editor | ❓ Chưa kiểm tra | Mục 3.7 |

> [!IMPORTANT]
> Dự án dùng **Python 3.11**, không phải 3.12. Build, test và bản đóng gói embeddable phải cùng **3.11.x x64** — lệch phiên bản sẽ lỗi `python311.dll`/`encodings` lúc chạy.

---

## 1. Công cụ cần cài (Windows x64)

| # | Công cụ | Bắt buộc | Dùng ở bước | Tải tại |
|---|---------|----------|-------------|---------|
| 1 | Visual Studio 2022 Community — workload **Desktop development with C++** | ✅ | 1.1 → 6.3 | visualstudio.microsoft.com |
| 2 | CMake ≥ 3.25 | ✅ | 1.1 | cmake.org/download (hoặc bản đi kèm VS) |
| 3 | Git + Git LFS | ✅ | 1.1, 1.13 | git-scm.com (LFS đã kèm) |
| 4 | vcpkg | ✅ | 1.1 | github.com/microsoft/vcpkg |
| 5 | Python 3.11.x **64-bit** | ✅ | 1.1, 1.7 | python.org/downloads/windows |
| 6 | Tiled Map Editor ≥ 1.10 | ✅ | 1.3, 3.1, 4.5 | mapeditor.org |
| 7 | Audacity | ⭐ Nên có | 1.12, 6.1 | audacityteam.org |
| 8 | LibreSprite / Aseprite | Tùy chọn | Sửa sprite, VFX 6.1 | libresprite.github.io |
| 9 | VS Code (+ CMake Tools, C/C++, Python, Ruff, Even Better TOML) | Tùy chọn | Soạn thảo | code.visualstudio.com |
| 10 | Python 3.11 **Windows embeddable package (64-bit)** | ✅ (lúc phát hành) | 6.3 | python.org → "Windows embeddable package (64-bit)" cùng bản 3.11.x |
| 11 | Visual C++ Redistributable 2015–2022 x64 | Để test máy sạch | 6.3 | learn.microsoft.com (vc_redist.x64.exe) |

---

## 2. Danh sách thư viện toàn dự án

### 2.1. Thư viện C++ (qua vcpkg manifest — file [vcpkg.json](file:///d:/ProjectGame/vcpkg.json))

| Port vcpkg | Tác dụng trong game | Bước kế hoạch | License |
|------------|---------------------|---------------|---------|
| `sdl2` | Cửa sổ, renderer, input, timer, game loop | 1.2 | Zlib |
| `sdl2-image` | Nạp PNG sprite/tileset (`IMG_LoadTexture`) | 1.3, 1.4 | Zlib |
| `sdl2-ttf` | Vẽ chữ UTF-8 tiếng Việt (HUD, Dialogue, Toast) | 1.11, 2.6 | Zlib (+ FreeType FTL) |
| `sdl2-mixer` | SFX `.wav`, BGM `.ogg`, FadeOut/Hook (E3), 2 kênh crossfade | 1.12, 3.5 | Zlib |
| `tmxlite` | Đọc `.tmx`/`.tsx` Tiled: layer, ObjectGroup, Custom Property | 1.3, 3.1 | Zlib |
| `nlohmann-json` | Đọc/ghi `save.json`, `balance.json`, `endings.json` phía C++ | 2.3, 4.6 | MIT |
| `catch2` *(feature `tests`)* | Unit test C++: AABB, A*, Lava, Save | 1.5, 2.5, 3.4, 2.3 | BSL-1.0 |

> `pybind11` **không** lấy từ vcpkg (ADR-0003) — lấy từ pip trong `.venv` 3.11 để không build thêm một CPython riêng.

Phụ thuộc gián tiếp vcpkg tự kéo về (sẽ phải copy DLL khi phát hành, Bước 6.3): `libpng`, `zlib`, `freetype`, `brotli`, `bzip2`, `libogg`/`libvorbis` (tùy cấu hình port mixer). Danh sách thật lấy theo thư mục `build/Release/` sau khi build.

### 2.2. Thư viện Python

| File | Gói (đã pin) | Dùng để | Ship vào game? |
|------|--------------|---------|----------------|
| [requirements/base.txt](file:///d:/ProjectGame/requirements/base.txt) | `pybind11==2.12.0` | Header + CMake config cho `pybind11::embed` | ❌ (chỉ build) |
| [requirements/dev.txt](file:///d:/ProjectGame/requirements/dev.txt) | `pytest==8.3.2`, `pytest-cov==5.0.0`, `ruff==0.6.8`, `mypy==1.11.2` | Test `tests/python`, lint, type-check | ❌ |
| [requirements/tools.txt](file:///d:/ProjectGame/requirements/tools.txt) | `pillow==10.4.0`, `jsonschema==4.23.0` | Convert `.webp`, kiểm sprite, validate `data/*.json` | ❌ |
| *(runtime game)* | **Chỉ thư viện chuẩn**: `json`, `math`, `random`, `enum`, `dataclasses`, `pathlib`, `typing` | Toàn bộ `scripts/` | ✅ qua `python311.zip` |

> [!WARNING]
> Không `import` gói pip nào trong `scripts/` — máy người chơi chỉ có bản Python embeddable (stdlib).

### 2.3. Tài nguyên ngoài (không phải thư viện code)

| Loại | Gợi ý | Ghi chú |
|------|-------|---------|
| Font | **Be Vietnam Pro** (Google Fonts, OFL) cho chữ thường; font pixel tùy chọn cho tiêu đề | `Press Start 2P` **không đủ dấu tiếng Việt**. Bắt buộc test chuỗi `"Tiến hóa – Giai đoạn 3! ẾỘỮỴ"` |
| SFX | ElevenLabs Sound Effects, Freesound.org (kiểm license CC0/CC-BY) | `.wav` 44.1 kHz 16-bit, chuẩn hóa −3 dBFS |
| BGM | Suno/Udio, Incompetech.com (CC-BY, ghi credit) | `.ogg` Vorbis 128–192 kbps; 2 loop lãnh thổ ~20–30s |
| Ảnh V2 thiếu | Placeholder màu theo bảng 0.4.C của kế hoạch | Không chờ vẽ mới code |

---

## 3. Hướng dẫn cài đặt từng bước

### 3.1. Thêm workload C++ cho Visual Studio 2022
1. Mở **Visual Studio Installer** → *Visual Studio Community 2022* → **Modify**.
2. Tick **Desktop development with C++**. Ở cột *Installation details* đảm bảo có:
   - `MSVC v143 - VS 2022 C++ x64/x86 build tools (Latest)`
   - `Windows 11 SDK` (hoặc Windows 10 SDK mới nhất)
   - `C++ CMake tools for Windows`
3. **Modify** → chờ cài xong → khởi động lại máy nếu được yêu cầu.

Kiểm tra (mở **Developer PowerShell for VS 2022**):
```powershell
cl
cmake --version
```

### 3.2. CMake
- Nếu đã tick *C++ CMake tools for Windows*, CMake có sẵn trong **Developer PowerShell for VS 2022**.
- Muốn dùng ở PowerShell thường: cài bản riêng từ cmake.org (tick *Add CMake to the system PATH*) hoặc:
```powershell
winget install --id Kitware.CMake -e
```

### 3.3. vcpkg (manifest mode)
```powershell
git clone https://github.com/microsoft/vcpkg C:\vcpkg
C:\vcpkg\bootstrap-vcpkg.bat -disableMetrics
[Environment]::SetEnvironmentVariable("VCPKG_ROOT", "C:\vcpkg", "User")
```
Mở lại terminal, sau đó khóa baseline cho [vcpkg.json](file:///d:/ProjectGame/vcpkg.json) (ghi commit vcpkg vào file → máy nào cũng ra đúng phiên bản):
```powershell
C:\vcpkg\vcpkg.exe x-update-baseline --add-initial-baseline
```

> [!NOTE]
> Ở manifest mode **không cần** chạy `vcpkg install sdl2:x64-windows ...` như kế hoạch gốc — lần đầu `cmake --preset ...` sẽ tự tải và build toàn bộ thư viện trong `vcpkg.json` (mất 10–30 phút). Không trộn classic mode với manifest mode.

### 3.4. Môi trường Python 3.11 (`.venv`)
```powershell
& "C:\Users\vovan\AppData\Local\Programs\Python\Python311\python.exe" -m venv .venv
.\.venv\Scripts\Activate.ps1
python -c "import sys, platform; print(sys.version, platform.architecture()[0])"
```
Kết quả phải là `3.11.x ... 64bit`. Nếu PowerShell chặn script: `Set-ExecutionPolicy -Scope CurrentUser RemoteSigned`.

### 3.5. Cài gói Python
```powershell
python -m pip install --upgrade pip
pip install -r requirements/dev.txt -r requirements/tools.txt
python -m pybind11 --cmakedir
```
Lệnh cuối in đường dẫn dùng cho `pybind11_DIR` (sẽ đặt trong `CMakePresets.json` ở Bước 1.1).

### 3.6. Git + Git LFS
```powershell
git init -b main
git lfs install
git lfs env
```
[.gitattributes](file:///d:/ProjectGame/.gitattributes) đã khai báo LFS cho `png/webp/wav/ogg/ttf/zip/psd/aseprite`. Trước khi commit ảnh, kiểm:
```powershell
git check-attr filter -- assets/sprites/rex/rex_idle.png
```
Phải trả về `filter: lfs`.

### 3.7. Tiled Map Editor
- Cài từ mapeditor.org. Thiết lập mặc định: *Tile layer format* = **CSV**, *Tile size* = 32×32.
- Lưu tileset ngoài (`maps/tilesets/forest.tsx`) thay vì embed để dùng chung nhiều map.

### 3.8. Nhập & chuẩn hóa tài nguyên từ `D:\PlanProjectGame\GAME\`
Hiện trạng: **41 file** (37 ảnh V1 + 4 `.webp` môi trường), trong đó **5 file `.webp`** cần convert.

| Nguồn | Đích |
|-------|------|
| `GAME\MainCharacter\rex_*.png` (trừ portrait) | `assets\sprites\rex\` |
| `GAME\MainCharacter\rex_portrait.png`, `GAME\Boss\boss_portrait.png` | `assets\ui\portraits\` |
| `GAME\Prey\boar_*.png` | `assets\sprites\boar\` |
| `GAME\Boss\boss_*.png` (trừ portrait) | `assets\sprites\khen\` |
| `GAME\Map\*.png` (trừ concept art) | `assets\tilesets\forest\` |
| `GAME\Map\map_concept_art.png` | `art_src\reference\` |
| `GAME\Map\dirt_tile.png.webp` → convert | `assets\tilesets\forest\dirt_tile.png` |
| `GAME\Effect\aura_effect.png`, `hit_effect.png` | `assets\effects\vfx\` |
| `GAME\Effect\*.webp` (4 file) → convert | `assets\effects\environment\` (`ancient_tree_stump`, `thorny_thicket`, `fern_bush`, `giant_fern`.png) |
| Toàn bộ `GAME\` (bản gốc) | `art_src\original\` |
| `map-image-prompts.md`, `character-image-prompts.md` | `art_src\prompts\` |

Lệnh copy (chạy ở gốc dự án):
```powershell
$G = "D:\PlanProjectGame\GAME"
Copy-Item "$G\*" "art_src\original\" -Recurse -Force
Copy-Item "D:\PlanProjectGame\map-image-prompts.md","D:\PlanProjectGame\character-image-prompts.md" "art_src\prompts\"
Copy-Item "$G\MainCharacter\rex_*.png" "assets\sprites\rex\" -Exclude "rex_portrait.png"
Copy-Item "$G\MainCharacter\rex_portrait.png","$G\Boss\boss_portrait.png" "assets\ui\portraits\"
Copy-Item "$G\Prey\boar_*.png" "assets\sprites\boar\"
Copy-Item "$G\Boss\boss_*.png" "assets\sprites\khen\" -Exclude "boss_portrait.png"
Copy-Item "$G\Map\*.png" "assets\tilesets\forest\" -Exclude "map_concept_art.png"
Copy-Item "$G\Map\map_concept_art.png" "art_src\reference\"
Copy-Item "$G\Effect\*.png" "assets\effects\vfx\"
```
Convert 5 file `.webp` (script `tools/convert_webp_to_png.py` tạo ở Bước 1.1, dùng Pillow):
```powershell
python tools/convert_webp_to_png.py
```

> [!CAUTION]
> Không chỉnh sửa trực tiếp `D:\PlanProjectGame\GAME\` — đó là bản gốc. Mọi xử lý làm trên bản copy.

---

## 4. Kiểm tra sau khi setup (Definition of Done cho Setup)

- [ ] `cl` và `cmake --version` (≥ 3.25) chạy được.
- [ ] `$env:VCPKG_ROOT` = `C:\vcpkg`, `vcpkg.json` có `builtin-baseline`.
- [ ] `.venv\Scripts\python.exe --version` = `3.11.x`, 64-bit.
- [ ] `pytest --version`, `ruff --version`, `python -m pybind11 --cmakedir` OK.
- [ ] `git lfs env` OK, `git check-attr` ảnh trả về `filter: lfs`.
- [ ] `assets/` có đủ **40 PNG** (35 PNG copy + 5 PNG convert từ `.webp`), `art_src/reference/map_concept_art.png` tồn tại. Kiểm nhanh: `(Get-ChildItem assets -Recurse -Filter *.png).Count`.
- [ ] Tiled mở được `forest_tileset_complete.png` với lưới 32×32 (8×5 ô).

---

## 5. Lệnh build (dùng sau khi hoàn tất Bước 1.1)

```powershell
cmake --preset debug          # cấu hình: vcpkg toolchain + Python3 3.11 venv + pybind11_DIR
cmake --build --preset debug
.\build\debug\Debug\DinoSurvival.exe
pytest                        # test Python
ctest --preset debug          # test C++ (khi bật feature "tests")
ruff check scripts tools tests
```
Bản phát hành (Bước 6.3):
```powershell
cmake --preset release
cmake --build --preset release
.\tools\package_release.ps1
```

---

## 6. Lỗi hay gặp khi setup

| Triệu chứng | Nguyên nhân | Cách sửa |
|-------------|-------------|----------|
| `unresolved external symbol __imp_Py...` | Python 32-bit / lệch phiên bản với triplet `x64-windows` | Dùng Python 3.11 **x64**, xóa `build/` cấu hình lại |
| CMake tìm thấy Python 3.12 | 3.12 đứng trước trong PATH | Truyền `-DPython3_EXECUTABLE=.venv/Scripts/python.exe` (đặt trong preset) |
| `Could not find pybind11Config.cmake` | Chưa truyền `pybind11_DIR` | `-Dpybind11_DIR=(python -m pybind11 --cmakedir)` |
| Thiếu `SDL2.dll` khi chạy exe | Chưa copy DLL runtime | vcpkg applocal (mặc định bật) hoặc `cmake/CopyRuntime.cmake` |
| `No module named 'encodings'` khi chạy máy khác | Thiếu Python embeddable | Bước 6.3: `python311.zip` + `python311._pth` + `PYTHONHOME` |
| Chữ tiếng Việt thành ô vuông | Font thiếu glyph / dùng `TTF_RenderText_*` | Font đủ dấu + `TTF_RenderUTF8_Blended` |
| `IMG_Load` lỗi với `dirt_tile.png.webp` | WebP trá tên | Chạy `tools/convert_webp_to_png.py` |
| vcpkg build rất lâu lần đầu | Build từ source | Bình thường; lần sau dùng binary cache |
