## Cấu trúc repo “Pig Blast PRJ” (Warsow assets)

Tài liệu tóm tắt cấu trúc, mục đích của từng thư mục và cách đóng gói/ghi đè khi tùy biến thành game mới.

### Tổng quan

- Không kèm engine: Repo này chứa dữ liệu/asset và script của Warsow; không có mã nguồn engine. Để build executable/server cần engine (Warsow/Qfusion) riêng.
- Dạng dữ liệu: Engine nạp từ thư mục game (`basewsw`) và các gói `.pk3` (zip). Gói đặt tên “đi sau” thường ghi đè “đi trước”.

### Thành phần chính trong repo

- `warsow-base-master`: gốc dữ liệu game Warsow.
  - `basewsw/`: thư mục dữ liệu chính khi chạy game (asset, shader, UI, âm thanh…).
  - `docs/`: tài liệu (tính năng, hướng dẫn server, giấy phép).
  - `warsow`, `warsow-server`: script shell khởi chạy client/server (chọn binary theo kiến trúc và trỏ `+set fs_basepath`).
- `warsow-gfx-simpleitems-master`: bộ icon “simple items” (SVG) và Makefile để render, đóng gói thành `.pk3` riêng.

### Cấu trúc chi tiết `warsow-base-master/basewsw`

- `ai/`: dữ liệu AI bot (ví dụ `dm.weights`, `generic.weights`).
- `fonts/`: font TTF/OTF và fallback (ví dụ `DroidSans.ttf`, `W arsowCrosshairs.ttf`).
- `gfx/`: tài nguyên đồ họa 2D/UI/HUD và hiệu ứng:
  - `gfx/2d`, `gfx/hud`, `gfx/ui`, `gfx/decals`, `gfx/colors`, `gfx/correction`, `gfx/indicators`, `gfx/misc`, `gfx/simpleitems`.
- `l10n/`: bản dịch `.po` cho UI/client/game:
  - `l10n/ui/*.po`, `l10n/cgame/*.po`, `l10n/*.po` (đổi/ thêm ngôn ngữ tại đây).
- `models/`: model 3D và texture kèm theo:
  - `models/players`, `models/weapons`, `models/v_weapons`, `models/items`, `models/mapobjects`, `models/effects`.
- `scripts/`: shader/material, sound shader, cấu hình hiển thị/hud UI:
  - Ví dụ: `world.shader`, `hud.shader`, `models.shader`, `ui.shader`, `shaderlist.txt`.
- `sounds/`: âm thanh theo nhóm: `sounds/weapons`, `sounds/players`, `sounds/ambient`, `sounds/menu`, `sounds/music`, …
- `textures/`: texture cho map/chủ đề: `textures/world`, `textures/wdm6`, `textures/tutorial`, …
- `*.pk3`: các gói map và texture pack có sẵn (ví dụ `map_wdm1.pk3`, `tex_glass.pk3`, `map_wtutorial1.pk3`).

### Thư mục tài liệu `warsow-base-master/docs`

- `features.txt`: danh sách tính năng client và cvar.
- `dedicated_server_guide.txt`: hướng dẫn cấu hình và chạy server.
- `license.txt`: giấy phép tài sản (CC BY-SA 4.0; một số gói “nonfree” là CC BY-ND 4.0).

### Script khởi chạy

- `warsow`: wrapper shell xác định kiến trúc hệ thống, chọn binary tương ứng và chạy với `+set fs_basepath` (trỏ tới thư mục chứa `basewsw/`) và `+set fs_usehomedir`.
- `warsow-server`: tương tự cho dedicated server.

### Cách nạp dữ liệu & cơ chế ghi đè

- Engine nạp từ thư mục game (mặc định `basewsw/`) và các gói `.pk3` nằm cùng cấp.
- Đóng gói `.pk3` theo đúng cấu trúc thư mục bên trong (`gfx/`, `scripts/`, `models/`, `sounds/`, `textures/`, `l10n/`…), thả vào `basewsw/`.
- Thứ tự ưu tiên: tùy engine, nhưng thường `.pk3` có tên “cao” (theo chữ cái) sẽ ghi đè nội dung trước đó; file rời bên ngoài `.pk3` cũng có thể ghi đè.

### Khi fork thành game mới bạn nên chỉnh ở đâu

- Tên game/thư mục game:
  - Giữ `basewsw/` để chạy nhanh hoặc tạo thư mục game mới (ví dụ `basepig/`) và cấu hình engine trỏ tới đó (tham số dòng lệnh của engine).
- UI/HUD: `gfx/ui`, `gfx/hud`, và shader liên quan trong `scripts/hud.shader`, `scripts/ui.shader`.
- Ngôn ngữ: thêm/sửa `.po` trong `l10n/ui/`, `l10n/cgame/`, `l10n/`.
- Âm thanh & nhạc: trong `sounds/**`.
- Model & texture: trong `models/**`, `textures/**` (cập nhật shader trong `scripts/*.shader` nếu thêm vật liệu mới).
- Shader/material: thêm/sửa ở `scripts/*.shader`; nhớ cập nhật `scripts/shaderlist.txt` nếu tạo file shader mới.
- Map: đóng gói như các `map_*.pk3` sẵn có.
- Simple items: dùng dự án `warsow-gfx-simpleitems-master` để build `.pk3` icon phẳng.

### Build “simple items” (`warsow-gfx-simpleitems-master`)

- Yêu cầu: GNU Make, Inkscape, ImageMagick.
- Lệnh build tạo `dist/gfx_items_flat.pk3`:

```bash
cd warsow-gfx-simpleitems-master
make -j $(nproc)
```

- Kết quả: file `.pk3` chứa `gfx/…/*.tga`; copy/thả file này vào `warsow-base-master/basewsw/` để kích hoạt.

### Giấy phép (lưu ý khi tái phân phối)

- Mặc định: CC BY-SA 4.0 (yêu cầu ghi công và chia sẻ tương tự).
- Ngoại lệ “nonfree”: một số gói như `tex_000_nonfree.pk3` thuộc CC BY-ND 4.0 (không phái sinh). Tránh chỉnh sửa/phái sinh các gói này khi làm game mới.

### Luồng đóng gói đề xuất cho game mới

1) Phát triển nội dung trong thư mục làm việc cục bộ theo cấu trúc `gfx/`, `scripts/`, `sounds/`, `models/`, `textures/`, `l10n/`.
2) Đóng gói thành `.pk3` (zip với nén `-9`, giữ nguyên đường dẫn bên trong).
3) Đặt `.pk3` vào thư mục game (mặc định `basewsw/` hoặc thư mục mới bạn chọn). Đặt tên gói theo ý đồ ưu tiên ghi đè.

### Ghi chú

- Đây là repo asset; để chạy build native cần binaries/engine tương thích. Hai script `warsow` và `warsow-server` chỉ là wrapper để gọi binary phù hợp.
- Bạn có thể thêm tệp hướng dẫn riêng (ví dụ `STRUCTURE.md`) hoặc tạo khung thư mục game mới (ví dụ `basepig/`) để tách bạch khỏi `basewsw/`.
