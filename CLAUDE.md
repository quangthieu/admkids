# ADM Store (admkids) — Shopify theme

## Dự án
Store Shopify bán áo thun trẻ em, có custom printing (in tên/số lên mẫu có sẵn).
Thị trường Việt Nam: VND, tiếng Việt, mobile-first (traffic chủ yếu từ Facebook/TikTok).
Lộ trình tổng: https://claude.ai/artifact/2x74Af3kT9BerQAggSARNR

**Giai đoạn hiện tại: 1 — dựng store demo trên development store** (xem `docs/phase-1.md`).
Phôi áo, xưởng in, size và vùng in CHƯA chốt → không hard-code thông số sản phẩm
(size, màu, vùng in, số ngày giao) trong code. Đọc từ metafield / metaobject / theme settings.

## Cấu trúc repo
- `theme/` — Shopify theme (Atelier — họ theme Horizon của Shopify, pull từ store). Mọi lệnh theme dùng `--path theme`.
- `docs/` — kế hoạch, task, ghi chú theme.
- `services/` — (giai đoạn 2B, chưa có) webhook server tạo file in.

## Lệnh thường dùng
```bash
shopify theme dev   --path theme --store adm-kids-dev.myshopify.com
shopify theme check --path theme
shopify theme push  --path theme --unpublished          # tạo theme preview mới
shopify theme push  --path theme --theme <theme-id>     # đẩy lên theme có sẵn
# Đồng bộ thay đổi làm trong theme editor về repo (làm TRƯỚC khi sửa các file này):
shopify theme pull  --path theme --only 'templates/*.json' --only 'sections/*.json' --only config/settings_data.json
```

## Quy ước code
- Hạn chế sửa file gốc của Horizon. Tạo section / block / snippet mới với prefix `adm-`
  (vd `blocks/adm-size-chart.liquid`). Nếu buộc phải sửa file gốc, đánh dấu:
  `{%- comment -%} ADM: <lý do> {%- endcomment -%}` để dễ merge khi update theme.
- JS: vanilla ES modules + custom elements theo pattern của Horizon. Không thêm jQuery/framework.
  File JS riêng đặt trong `assets/adm-*.js`.
- CSS: dùng CSS variables / color schemes của theme, không hard-code màu.
- Chuỗi hiển thị cho khách: dùng translation keys trong `locales/vi.json` (+ `en.default.json`),
  không viết cứng tiếng Việt trong Liquid.
- Ảnh: `image_url` + `image_tag` với `widths`/`sizes`; `loading: 'lazy'` cho ảnh dưới màn hình đầu.
- Không thêm app/script bên thứ ba nếu làm được bằng theme code.
- Accessibility: dialog/modal phải có focus trap, đóng bằng ESC, có label; input có `<label>`.
- Tiếng Việt: luôn test với chuỗi có dấu chồng, vd "Nguyễn Ỷ Thuỳ"; font phải có Vietnamese subset.
- Trước khi commit: `shopify theme check --path theme` không còn error.
- `config/settings_data.json` và `templates/*.json` bị theme editor sửa → luôn pull trước khi chỉnh tay.

## Data model (tạo trong Shopify Admin)
Product metafields (namespace `custom`):
| key | type | dùng cho |
| --- | --- | --- |
| `age_group` | list.single_line_text_field | lọc, badge |
| `gender` | single_line_text_field | lọc |
| `material` | single_line_text_field | badge, mô tả |
| `print_method` | single_line_text_field | badge (DTF/DTG/…) |
| `collection_theme` | single_line_text_field | lọc theo bộ sưu tập |
| `occasion` | list.single_line_text_field | lọc theo dịp |
| `size_chart` | metaobject_reference → `size_chart` | modal bảng size |

Metaobject `size_chart`:
| field | type | ghi chú |
| --- | --- | --- |
| `title` | single_line_text_field | vd "Áo thun trẻ em – form regular" |
| `rows` | json | `[{"size":"4Y","age":"3–4 tuổi","height_cm":"98–104","length_cm":42,"chest_cm":32}]` |
| `note` | multi_line_text_field | cách đo, lưu ý co rút |

Product type: `Ready-made` (mẫu in sẵn) và `Custom` (in tên/số — giai đoạn 2A).
Giai đoạn 2 sẽ thêm metaobject `print_template` + metafield `custom.print_template`.
