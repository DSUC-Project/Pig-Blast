# Lộ Trình Pig Blast – Phase 1 (Asset Mod)

Mục tiêu: biến Warsow assets thành phiên bản Pig Blast của bạn mà KHÔNG đụng mã engine, tập trung vào đồ họa/UI/HUD/âm thanh/cấu hình. Giai đoạn sau mới tính gameplay-level code và blockchain.

## 1) Phạm Vi & Kết Quả
- Thay đổi nhận diện: logo, nền menu, màu sắc, font, icon.
- Cập nhật HUD/UI qua ảnh và shader; thêm/làm phẳng “simple items”.
- Điều chỉnh cảm giác chơi bằng cvars (không sửa engine): outline, shadow, crosshair, FOV, shake.
- Đóng gói bản ghi đè (`.pk3`) riêng của Pig Blast để bật/tắt dễ dàng.
- Kết quả: chạy được bản Pig Blast qua thư mục game riêng `basepig/` hoặc `.pk3` overlay.

## 2) Yêu Cầu Hệ Thống
- Windows: Warsow/Qfusion client (binaries) — dùng bản đã build sẵn cho Windows.
- Công cụ đồ họa: Inkscape (SVG), GIMP/Photoshop (TGA/PNG), ImageMagick (batch render).
- Công cụ nén: 7-Zip hoặc `zip` CLI (để tạo `.pk3`).
- Tùy chọn build “simple items”: GNU Make + Inkscape + ImageMagick (Makefile có sẵn).

## 3) Kiến Trúc Nạp Asset & Ghi Đè (Quan trọng)
- Engine nạp dữ liệu từ thư mục game (mặc định `basewsw/`) và các gói `.pk3` trong đó.
- Cơ chế ưu tiên: tên “đi sau” (theo thứ tự chữ cái) sẽ ghi đè nội dung “đi trước”. File rời trong thư mục game cũng ghi đè file nằm trong `.pk3`.
- Khuyến nghị: tạo thư mục game mới `basepig/` để tách bạch khỏi `basewsw/`. Chỉ copy/đặt những file bạn sửa vào `basepig/`.

Tham chiếu:
- `Pig-Blast/pigblast-base-master/basewsw/`
- `Pig-Blast/pigblast-base-master/docs/`

## 4) Chạy Baseline (để biết “trước khi sửa”) — 0.5 ngày
- Cài binary Warsow phù hợp Windows (không cần source engine).
- Chạy với tham số trỏ repo:
  - Shortcut cho `warsow_x64.exe` (ví dụ) thêm: `+set fs_basepath "C:\\...\\Pig-Blast\\pigblast-base-master" +set fs_usehomedir 0 +set fs_game basewsw`
- Kiểm thử nhanh: vào menu, mở console (`~`), chạy `map wdm1` hoặc thử Join Game.
- Ghi chú baseline: FPS, cảm giác camera, outline, shadow, HUD, UI.

## 5) Tạo Workspace Mod `basepig/` — 0.5 ngày
- Tạo thư mục cùng cấp `basewsw/`: `Pig-Blast/pigblast-base-master/basepig/`
- Cấu hình chạy Pig Blast:
  - Shortcut tham số: `+set fs_basepath "...pigblast-base-master" +set fs_usehomedir 0 +set fs_game basepig`
- Tạo khung thư mục (ban đầu để trống, chỉ thêm những thứ cần override):
  - `basepig/gfx/ui/`, `basepig/gfx/hud/`, `basepig/gfx/simpleitems/`
  - `basepig/scripts/`
  - `basepig/sounds/`
  - `basepig/l10n/ui/`
  - `basepig/configs/autoexec.cfg`
- Tạo `autoexec.cfg` cơ bản (đề xuất bên dưới mục 10) để có cảm giác khác biệt ngay khi bật `basepig`.

## 6) Branding Pass (Logo, Nền, Font, L10n) — 1–2 ngày
- Logo & nền menu:
  - Override trong `basepig/gfx/ui/` (copy tên file từ `basewsw` sang và thay ảnh), cập nhật tỉ lệ/độ phân giải phù hợp.
- Font và màu UI:
  - Thử đổi font bằng các cvars liên quan (xem `docs/features.txt`), hoặc thay file font trong `basepig/fonts/` và tham chiếu trong UI shader nếu có.
- Ngôn ngữ/UI text:
  - Sửa `.po` trong `basepig/l10n/ui/` để đặt tên Pig Blast, menu text, v.v. (copy file tương ứng từ `basewsw/l10n/ui/`).
- Kiểm tra trong game, ghi lại chỗ UI nào chưa ghi đè đúng.

Chấp nhận: vào game thấy logo/nền/brand Pig Blast ở menu, không vỡ layout.

## 7) HUD + UI Shaders — 1–2 ngày
- Ảnh HUD/UI: `basepig/gfx/hud/`, `basepig/gfx/ui/`
- Shader UI/HUD: `basepig/scripts/ui.shader`, `basepig/scripts/hud.shader`
  - Nếu tạo file shader mới, bổ sung tên file vào `basepig/scripts/shaderlist.txt` để engine nạp.
- Crosshair, màu sắc, độ dày outline HUD: tùy chỉnh trong ảnh hoặc shader.
- Scoreboard font/cỡ chữ: qua cvars `cg_scoreboardFont`, `cg_scoreboardWidthScale`.

Chấp nhận: HUD có màu/chữ/biểu tượng Pig Blast, không lỗi shader trong console.

## 8) Simple Items (Icon phẳng) — 0.5–1 ngày
- Thư mục: `Pig-Blast/pigblast-gfx-simpleitems-master`
- Yêu cầu: GNU Make, Inkscape, ImageMagick.
- Lệnh build (Linux/macOS/WSL):
  ```bash
  cd Pig-Blast/pigblast-gfx-simpleitems-master
  make -j $(nproc)
  ```
- Kết quả: `dist/gfx_items_flat.pk3` — copy/thả vào `Pig-Blast/pigblast-base-master/basepig/`.
- Nếu Windows thuần: dùng WSL cho Makefile, hoặc tự render SVG → PNG/TGA, giữ path `gfx/simpleitems/...` rồi tự nén `.pk3` (mục 9).

Chấp nhận: vào game thấy icon phẳng mới (đặt tên gói ưu tiên cao để ghi đè).

## 9) Đóng Gói `.pk3` Overlay — 0.5 ngày (liên tục dùng về sau)
- Trong dev: để file rời trong `basepig/` cho nhanh.
- Khi phát hành/đóng gói test:
  - Chỉ chọn thư mục bạn override: `gfx/`, `scripts/`, `sounds/`, `l10n/`, `fonts/`...
  - Tạo zip nén tối đa và đổi đuôi `.pk3`.
  - Quy ước đặt tên để ghi đè: `pig_zzz_base.pk3` (zzz để đứng sau).

Ví dụ lệnh:
- PowerShell + 7-Zip:
  ```powershell
  cd Pig-Blast/pigblast-base-master/basepig
  7z a -tzip -mx=9 pig_zzz_base.pk3 .\gfx .\scripts .\sounds .\l10n .\fonts
  ```
- Bash (WSL):
  ```bash
  cd Pig-Blast/pigblast-base-master/basepig
  zip -9 -r pig_zzz_base.pk3 gfx scripts sounds l10n fonts
  ```

Thả `.pk3` ngay trong `basepig/` hoặc `basewsw/` tùy cách bạn chạy.

## 10) Tinh Chỉnh Gameplay Bằng CVars — 0.5 ngày
- Không đụng code vẫn chỉnh cảm giác đáng kể. Thêm vào `basepig/configs/autoexec.cfg`:

Preset “Arcade” (mềm mại, rõ ràng):
```
seta cg_pickup_flash 0
seta cg_damage_kick 1
seta cg_damage_blend 1
seta cg_outlineworld 1
seta cg_shadows 2
seta r_outlines_scale 1.0
seta r_outlines_cutoff 1500
seta fov 100
seta cg_showPlayerNames 1
seta cg_showPointedPlayer 1
seta cg_scoreboardFont "virtue_10"
seta cg_scoreboardWidthScale 1.05
```

Preset “Hardcore” (sạch, ít hiệu ứng):
```
seta cg_pickup_flash 0
seta cg_damage_kick 0
seta cg_damage_blend 0
seta cg_outlineworld 0
seta cg_shadows 1
seta fov 95
seta cg_showPlayerNames 0
seta cg_showPointedPlayer 0
```

- Gán phím thử nhanh trong cùng file:
```
bind F5 "exec configs/autoexec.cfg" // reload
```

Tham khảo thêm cvars trong: `Pig-Blast/pigblast-base-master/docs/features.txt`.

## 11) Kiểm Thử & Checklist Chất Lượng — xuyên suốt từng bước
- Menu/HUD: không vỡ layout, text không tràn.
- Shader: không có cảnh báo/lỗi trong console; nếu có, kiểm tra `scripts/*.shader` và `shaderlist.txt`.
- Hiệu năng: FPS ổn trên PC mục tiêu.
- Âm thanh: volume hợp lý, không rè/clip; mapping tên file đúng thư mục.
- Ghi đè: thử xóa `.pk3` mới để so sánh trước/sau; xác nhận logic ưu tiên tên.
- Network/Map: map phổ biến (`wdm1`, `wctf1`, …) vào được bình thường.

## 12) Pháp Lý & Bản Quyền
- Mặc định asset theo CC BY-SA 4.0 (ghi công + chia sẻ tương tự).
- Gói “nonfree” như `tex_000_nonfree.pk3` là CC BY-ND 4.0 (không phái sinh).
  - Tránh sửa/đóng gói lại nội dung “nonfree”. Thay vào đó, ghi đè bằng asset tự tạo.

## 13) Bàn Giao & Lưu Trữ
- Lưu `.pk3` chính: `pig_zzz_base.pk3` và (nếu có) `gfx_items_flat.pk3` trong `basepig/`.
- Commit thay đổi trong thư mục `Pig-Blast/` (đặc biệt `basepig/` và `ROADMAP.md`).
- Nhật ký thay đổi ngắn gọn: `Pig-Blast/NoteLog.md` (ghi các mốc đã làm, file đã đổi).

## 14) Dự Kiến Thời Gian
- Tuần 1: baseline, scaffold `basepig`, branding pass.
- Tuần 2: HUD/UI shader, simple items, audio/FX.
- Tuần 3: tinh chỉnh cvars, polish, đóng gói `.pk3`, kiểm thử.

## 15) Phase 2 (Xem Trước)
- Gameplay sâu (sát thương, di chuyển, vũ khí): cần game code (Qfusion/Warsow gameplay code) → fork, build, và thay đổi logic.
- Công cụ map/biên tập bản đồ: tạo map mới, material riêng.
- Tích hợp blockchain: khi gameplay ổn định, thiết kế flow off-chain/on-chain phù hợp (leaderboard, skin ownership, kinh tế trong game, v.v.).

---

Hỏi đáp nhanh:
- Tôi muốn thấy thay đổi ngay: làm mục 5 + 6 (logo/nền) trước, vào game để “thấy chất Pig Blast”.
- Tại sao file không đổi dù tôi đã thay? Kiểm tra tên file đúng, đúng thư mục, và thứ tự ưu tiên tên `.pk3`.
- Shader không nạp: thêm file vào `scripts/shaderlist.txt` hoặc hợp nhất vào file shader cũ đã có trong danh sách.
