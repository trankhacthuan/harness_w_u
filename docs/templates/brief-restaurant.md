# Brief template — restaurant website

Fill every `[...]`, then give this whole file to the agent as the task prompt.
Put the same facts in `docs/product/PRODUCT.md`. Anything left blank becomes a
`[CẦN BỔ SUNG: ...]` placeholder (`docs/CONTENT_RULES.md`).

How the harness treats it: this is a spec intake. The site pages are normal-lane
stories; the reservation form and its database are **high-risk** (personal data).
Where this brief states its own steps or stopping points, they override the
harness defaults.

---

# VAI TRÒ
Bạn là art director kiêm front-end developer chuyên website nhà hàng. Hãy làm
website thật, dùng được ngay, mang bản sắc riêng của nhà hàng, không giống
landing page AI hàng loạt.

# NGUYÊN TẮC DỮ LIỆU
- Không bịa tên, giá, đánh giá, giải thưởng, số liệu, địa chỉ, liên hệ, câu chuyện.
- Thiếu dữ liệu thì dùng placeholder dạng [CẦN BỔ SUNG: ...] và hiển thị rõ trong giao diện.
- Khu vực chưa có nội dung thật (đánh giá, tin tức) thì ẩn hoặc để placeholder, không tạo nội dung giả.
- Cuối cùng liệt kê toàn bộ nội dung và tài sản còn thiếu (ghi vào `docs/product/MISSING.md`).

# THÔNG TIN NHÀ HÀNG
Tên: [TÊN] | Slogan: [SLOGAN] | Ẩm thực: [LOẠI HÌNH] | Món đặc trưng: [MÓN]
Địa chỉ: [ĐỊA CHỈ] | SĐT: [SĐT] | Email: [EMAIL] | Giờ mở cửa: [GIỜ]
Facebook: [LINK] | Zalo: [LINK] | Google Maps: [LINK]
Khách chính: [NHÓM KHÁCH]
Chi tiết thật làm chất liệu viết nội dung (bắt buộc điền 2-3 ý): [nguyên liệu / cách nấu / nguồn gốc / người làm / không gian]

# BẢN SẮC THỊ GIÁC
- Từ khóa không khí: [3-5 TỪ, vd: gỗ, khói bếp, đèn vàng, thủ công]
- Định vị: ấm cúng, đáng tin, thân thiện, có chiều sâu văn hóa ẩm thực.
- Tham chiếu (học cấu trúc, không sao chép): [LINK 1: học nhịp bố cục], [LINK 2: học typography], [LINK 3: học cách trình bày menu]. Nếu để trống, tự chọn hướng và nêu lý do.

# QUYẾT ĐỊNH THIẾT KẾ (bắt buộc tuân thủ)
- Typography: 1 serif display + 1 sans cho body, đều hỗ trợ đầy đủ dấu tiếng Việt (kiểm tra bằng chuỗi "Ắ Ề Ổ Ữ ặ ệ ớ ự"). Thang cỡ chữ rõ, tiêu đề lớn tương phản mạnh với body, body tối thiểu 16px, line-height thoải mái.
- Màu: 1 màu nền, 1 màu chữ, 1 màu nhấn duy nhất lấy từ [MÀU THƯƠNG HIỆU]. Không thêm màu nhấn thứ hai. Tương phản đạt WCAG AA.
- Bố cục: mỗi section dùng cấu trúc khác nhau (ảnh tràn lề, chia cột bất đối xứng, chữ lớn chồng lên ảnh, menu trình bày như thực đơn in). Tối đa 1 section dạng lưới card đồng dạng.
- Cấm: gradient màu mè, glassmorphism, glow, icon trang trí vô nghĩa, bóng đổ dày, bo góc đồng loạt, animation gây phân tâm.
- Chuyển động: chỉ fade/slide nhẹ khi cuộn, hover, chuyển ảnh; tôn trọng prefers-reduced-motion.
- Ảnh placeholder: khung đúng tỷ lệ cuối cùng, nền trung tính, kèm mô tả ảnh cần chụp (vd: "[CẦN BỔ SUNG ẢNH: cận cảnh tô [MÓN], khói bốc, ánh sáng bên trái]") để layout vẫn đẹp khi chưa có ảnh thật.
- Giọng văn: cụ thể, giàu giác quan, dựa trên chi tiết thật. Tốt: "Nước dùng ninh 12 tiếng từ xương ống." Xấu: "Hương vị tinh hoa hội tụ", "trải nghiệm ẩm thực tuyệt vời". Chi tiết chưa có thì để placeholder, không bịa cho hay.

# CẤU TRÚC
Trang chủ (/):
1. Thanh thông báo ngắn (ưu đãi / giờ mở cửa / hướng dẫn đặt bàn)
2. Header: logo/tên + menu + nút "Đặt bàn" (cố định, dễ thấy)
3. Hero: ảnh chính, tiêu đề nói rõ bán gì trong vài giây, mô tả ngắn, 2 CTA (Xem thực đơn, Đặt bàn)
4. Câu chuyện và điểm khác biệt (ngắn)
5. Món nổi bật 4-6 món (chỉ hiện giá nếu đã có)
6. 3-5 lý do chọn nhà hàng, dựa trên thông tin thật
7. Gallery không gian và món ăn, có lightbox (bàn phím + Esc, focus trap)
8. Đánh giá khách hàng (placeholder hoặc ẩn)
9. Tin tức/sự kiện (chỉ khi có nội dung thật)
10. Form đặt bàn: họ tên, SĐT, số khách, ngày, giờ, ghi chú, email (tùy chọn)
11. Liên hệ, Google Maps, nút gọi (tel:) và chỉ đường trên di động
12. Footer: thông tin, liên kết nhanh, kênh liên hệ
Trang phụ: /menu (chia danh mục, dễ đọc trên điện thoại), /about, /gallery, /contact, /news (chỉ khi có nội dung thật).

# KỸ THUẬT
- Stack cố định theo repo (`docs/decisions/0009-web-stack.md`, `0011-postgresql-database.md`): Next.js (App Router) + TypeScript, CSS Modules + CSS variables, PostgreSQL. Không phụ thuộc thư viện nặng không cần thiết.
- Mobile-first, responsive; ưu tiên tốc độ: ảnh lazy-load, đúng kích thước, width/height khai báo để tránh layout shift. Ngân sách hiệu năng theo `docs/DESIGN.md`.
- Form đặt bàn: validate phía client và phía server (tên, SĐT VN, số khách, ngày giờ không ở quá khứ), thông báo lỗi dễ hiểu, trạng thái loading/thành công/thất bại. Gửi tới route nội bộ `/api/reservations` và lưu vào PostgreSQL. Không gọi dịch vụ ngoài (email, Zalo, Maps API). Nếu chưa kết nối được database, API trả 503, form hiện thông báo kèm nút gọi [SĐT], các trang khác vẫn hoạt động bình thường; phần đặt bàn ghi rõ "chưa kiểm chứng với PostgreSQL" cho đến khi chạy được test thật.
- Google Maps: dùng link/chỉ đường từ [LINK], không dùng API.
- A11y: HTML ngữ nghĩa, alt text, focus visible, điều hướng bàn phím, label cho mọi input, hit area tối thiểu 44px.
- SEO: title, meta description, heading đúng cấp, Open Graph, JSON-LD schema Restaurant (chỉ điền trường đã có dữ liệu).

# QUY TRÌNH (làm theo thứ tự)
Bước 1: Nêu 3 quyết định thiết kế then chốt (font, màu, ý tưởng bố cục chủ đạo) kèm lý do ngắn, rồi làm tiếp ngay.
Bước 2: Làm hoàn chỉnh trang chủ.
Bước 3: Tự rà soát theo checklist dưới, mỗi mục nêu bằng chứng cụ thể (section/dòng/quyết định nào) và sửa luôn điểm yếu tìm được.
Bước 4: Liệt kê nội dung/tài sản còn thiếu. Dừng lại; chỉ làm các trang phụ sau khi tôi xác nhận.

# CHECKLIST TỰ RÀ SOÁT
1. Người lạ hiểu nhà hàng bán gì trong 5 giây đầu? (trích tiêu đề hero)
2. Từ trang chủ, xem menu và đặt bàn trong tối đa 2 thao tác?
3. Có section nào trông như template (lưới card lặp, gradient, icon trang trí)? Sửa.
4. Có nội dung nào bịa hoặc chưa có nguồn? Liệt kê và thay bằng placeholder.
5. Trên màn hình 375px: chữ, nút gọi, form, menu điều hướng có dùng thoải mái không?
6. Dấu tiếng Việt hiển thị đúng ở mọi font và cỡ chữ?
