# PROJECT_CONTEXT

- Dự án: Kỷ Nguyên Sinh Tồn (APEX: Evolve or Die) — 2D Top-down Survival & Story-Driven Action, Solo Indie
- Engine: **Custom C++17** — SDL2, SDL2_image, SDL2_ttf, SDL2_mixer, tmxlite, nlohmann-json
- Scripting: **Python 3.11 x64** nhúng qua pybind11 (`PYBIND11_EMBEDDED_MODULE(game_engine, m)`), runtime chỉ dùng stdlib
- Build: Windows x64 · Visual Studio 2022 (MSVC v143) · CMake ≥ 3.25 · vcpkg (manifest mode)
- Platforms: Windows 10/11 x64 (Tier: PC)
- Target FPS: 60 (VSync), tối thiểu ≥ 55 FPS ở cảnh nặng nhất Survival
- Độ phân giải logic: 800×600 (MVP) → 1280×720 (V2), `SDL_RenderSetLogicalSize` + `FULLSCREEN_DESKTOP`
- Render: SDL_Renderer 2D (accelerated + VSync), tile 32×32, boss 64×64
- Timestep: biến thiên, `dt = min(raw, 0.05f)` (ADR-0002)
- Quy ước tốc độ: `px/s = SPD × 50.0f` (Errata E7)
- Test framework: pytest (Python), Catch2 v3 (C++)
- Lint: ruff + mypy (Python), MSVC `/W4` (C++)
- Netcode: none · Backend: none
- Phát hành: Itch.io + GameJolt, Free / Pay-what-you-want
- Kế hoạch gốc: `D:\PlanProjectGame\ke-hoach-game-master.md` (8 phần · 36 bước · 16 tuần)
