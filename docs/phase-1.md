# Giai đoạn 1 — Setup Shopify store (demo) · tuần 1–4

**Mốc kết thúc:** store demo chạy trên development store, 10–15 sản phẩm demo (mockup tạm),
lọc sản phẩm hoạt động, đặt được 5 đơn test (Bogus gateway + COD + chuyển khoản), Lighthouse mobile ≥ 70.

Ký hiệu: **[Admin]** = bạn làm trong Shopify Admin · **[Code]** = làm bằng Claude Code trong PhpStorm.

---

## A. Chuẩn bị store [Admin] — tuần 1

- [x] Shopify Partner → Stores → tạo development store `adm-kids-dev`. Kiểm tra lúc tạo: chọn loại store **có thể chuyển giao (transfer)** để sau này nâng lên gói trả phí và dùng làm store thật.
- [x] Settings → General: tiền tệ **VND**, múi giờ GMT+07:00, metric (kg), tiền tố mã đơn `#ADM`, backup region Vietnam.
- [x] Markets: tạo market **Việt Nam** (VND); Canada và United States chuyển sang Draft.
- [x] Settings → Languages: **Tiếng Việt** là ngôn ngữ mặc định.
- [x] Online Store → Themes: thêm theme **Atelier** (miễn phí, họ Horizon).
- [x] Settings → Payments: COD + Bank Deposit (manual) + Test payment gateway (1 = thành công, 2 = từ chối, 3 = lỗi).
    - [ ] Bạn tự điền thông tin tài khoản vào phần hướng dẫn của Bank Deposit (đang là placeholder).
- [x] Shipping (market Việt Nam): "Giao hàng tiêu chuẩn" 30.000đ, 3–5 ngày làm việc, freeship từ 499.000đ (số tạm).
- [ ] Apps: cài **Search & Discovery** (lọc sản phẩm) và **Shopify Flow** (dùng từ giai đoạn 2).
- [x] Settings → Custom data: 7 product metafield `custom.*` + metaobject `size_chart` (title, rows JSON, note) theo `CLAUDE.md`.
- [ ] Xoá hoặc ẩn dữ liệu mẫu snowboard có sẵn của dev store trước khi nhập sản phẩm demo.

## B. Dữ liệu demo [Admin + Code] — tuần 1–2

- [ ] Chuẩn bị 1 bộ mockup áo trẻ em trơn (PNG/PSD, 3 màu) và 3–5 thiết kế mẫu.
- [ ] Tạo 3 collection demo (vd Khủng long, Phương tiện, Chữ vui) + collection "Áo in tên".
- [ ] 10–15 sản phẩm: option **Size** (2Y, 4Y, 6Y, 8Y, 10Y, 12Y — tạm) × **Màu** (Trắng, Đen, Hồng pastel); giá tạm; 2–3 sản phẩm product type `Custom` làm chỗ cho giai đoạn 2A.
- [ ] 1 entry metaobject `size_chart` với số đo tạm, gắn vào sản phẩm.

> Tuỳ chọn [Code] — prompt:
> "Đọc CLAUDE.md và docs/phase-1.md mục B. Tạo `docs/demo-products.csv` theo định dạng product CSV import của Shopify cho 12 sản phẩm demo: 3 bộ sưu tập × 4 thiết kế, option Size (2Y,4Y,6Y,8Y,10Y,12Y) × Màu (Trắng, Đen, Hồng pastel), product type Ready-made (3 sản phẩm Custom), tiêu đề và mô tả tiếng Việt chuẩn SEO, giá 189000/229000, cột metafield custom.* theo CLAUDE.md, để trống Image Src (mình sẽ upload ảnh lên Files rồi điền URL)."

## C. Theme [Code] — tuần 1–4

Mỗi task: 1 branch (`feature/adm-...`), chạy `shopify theme dev`, `theme check`, rồi commit.

### T1. Khởi tạo repo
Bạn chạy: `shopify theme pull --path theme --store adm-kids-dev.myshopify.com` (chọn Atelier).
> Prompt: "Đọc CLAUDE.md. Theme Atelier (họ Horizon) đã ở `theme/`. Tạo `.gitignore` cho repo Shopify theme (node_modules, .DS_Store, .shopify/, .idea/ trừ file chia sẻ được, .env). Chạy `shopify theme check --path theme` và báo cáo. Sau đó đọc cấu trúc Atelier (sections, blocks, snippets, JS chính trong assets, cách component/custom element được khai báo, cách product form gửi add-to-cart) và viết tóm tắt vào `docs/theme-notes.md` để dùng cho các task sau."

**Xong khi:** commit đầu tiên đã push lên GitHub; `docs/theme-notes.md` có mô tả product form và block system.

### T2. Nền thương hiệu
> Prompt: "Đọc CLAUDE.md và docs/theme-notes.md. Đề xuất cấu hình font cho Horizon có Vietnamese subset (heading tròn, thân thiện trẻ em; body dễ đọc) và 3 color scheme pastel. Thêm block footer `adm-moit-badge`: setting link + ảnh logo 'Đã thông báo Bộ Công Thương', không render khi chưa có link. Chuỗi hiển thị qua locales vi/en."

**Xong khi:** test chuỗi "Nguyễn Ỷ Thuỳ" hiển thị đúng ở heading/body; badge ẩn khi trống.

### T3. Bảng size (modal)
> Prompt: "Đọc CLAUDE.md (data model size_chart) và docs/theme-notes.md. Tạo theme block `adm-size-chart` cho product information: nút 'Bảng size' mở dialog hiển thị bảng từ `product.metafields.custom.size_chart` (field rows là JSON, field note là ghi chú). Dùng `<dialog>` hoặc component dialog có sẵn của Horizon, có focus trap, ESC để đóng, bảng cuộn ngang trên mobile. Không render gì nếu sản phẩm không có metaobject. Thêm translation keys vi/en."

**Xong khi:** có/không có size chart đều không lỗi; dùng được bằng bàn phím; ổn trên màn 360px.

### T4. Badge + thời gian giao dự kiến
> Prompt: "Tạo block `adm-product-badges` hiển thị chất liệu và công nghệ in từ metafield `custom.material`, `custom.print_method` (ẩn badge nếu trống). Tạo block `adm-delivery-estimate`: số ngày lấy từ block settings riêng cho product type `Ready-made` và `Custom` (vd 'Giao 2–4 ngày' / 'Sản xuất 2–3 ngày + giao 2–4 ngày'). Không hard-code số ngày."

### T5. Bộ lọc collection
[Admin] Search & Discovery → Filters: Size, Màu, Độ tuổi (`custom.age_group`), Bộ sưu tập, Giá.
> Prompt: "Kiểm tra UI filter của Horizon trên collection page ở mobile: drawer, label tiếng Việt, hiển thị swatch màu cho option Màu. Sửa label/locale nếu cần, ghi chú các thay đổi vào file gốc theo quy ước ADM."

### T6. Trang chủ mobile-first
[Admin] Dựng bằng theme editor: hero, collection theo độ tuổi, bộ sưu tập, teaser "Áo in tên", đánh giá (placeholder).
> Prompt: "Tạo section `adm-usp` (3–4 ô: icon, tiêu đề, mô tả ngắn — vd Cotton an toàn cho da bé / In tên riêng cho bé / Giao toàn quốc), dùng blocks để admin thêm/bớt, responsive 2 cột trên mobile, icon SVG inline nhẹ."

### T7. Ngôn ngữ
> Prompt: "So sánh `theme/locales/vi.json` với `en.default.json`: liệt kê key còn thiếu hoặc dịch máy, đề xuất câu chữ tiếng Việt tự nhiên cho cart, product, collection, search, account. Không sửa schema locales."
[Admin] Settings → Notifications và checkout: kiểm tra nội dung tiếng Việt.

### T8. QA + hiệu năng
> Prompt: "Chạy `shopify theme check --path theme` và sửa hết error. Rà các file `adm-*`: ảnh có widths/sizes/lazy, không có JS chặn render, không có chuỗi hard-code. Viết checklist QA thủ công vào `docs/qa-phase-1.md` (home, collection + filter, product + size chart, cart, checkout) cho iOS Safari và Android Chrome."
[Admin] Lighthouse mobile ≥ 70 cho home/collection/product; đặt 5 đơn test: Bogus thành công, Bogus thất bại, COD, chuyển khoản, huỷ + hoàn tiền.

---

## Để sau (không thuộc giai đoạn 1)
- Tracking GA4 / Meta Pixel / TikTok Pixel — làm khi có tài khoản quảng cáo (trước launch).
- Tích hợp hãng vận chuyển — sau khi chốt hãng (giai đoạn 4).
- Block cá nhân hoá + preview — giai đoạn 2A.
