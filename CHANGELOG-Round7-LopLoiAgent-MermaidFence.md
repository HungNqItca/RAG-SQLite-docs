# CHANGELOG — Round 7: Cập nhật LỚP LỖI agent-path (09-16) + bọc fence Mermaid

> **Bối cảnh:** Round 6 đồng bộ codebase tới 2026-09-13. Rà lại: một file đổi sau đó — `performance_evaluation/DESIGN.md` (2026-09-16) + `phase3_generation/DESIGN.md §A.15`. Đối chiếu code → cập nhật. Đồng thời bọc fence Mermaid theo yêu cầu hiển thị VS Code.

## A. Nội dung mới (đối chiếu DESIGN §A.15 + perf-eval §09-16)

### A1. Part 6 — §16.4 Sửa agent-path theo LỚP lỗi (mới)
- Phân tích 2.008 lượt agent (11/6–16/9): 151 lượt dừng sớm. Tài liệu hóa **5 lớp lỗi** L1/L1′/L2/L3/L4/L5 với cơ chế hỏng + bản vá + cờ:
  - L1: compute tính sai dòng → `compute_row_select` (`tabular_<n>#k`, observation đánh số)
  - L1′: FeeGuard chỉ hỏi "số tồn tại?" → `compute_match` (shadow)
  - L2: lượt sai khuôn không để lại nội dung (67/72 văn xuôi không nhãn) → `lenient_final`
  - L3: dừng sớm/sửa-từ-đầu bịa căn cứ → `early_stop: continue`, `repair_mode: resume`, `strip_unverified`, `verify_doc_only`, `doc_ref_by_position`
  - L4: làn tabular/general khởi tạo lười (nguội 117s) → `warmup.lazy_retrievers`
  - L5: LLM đoán `domain` lọc cứng loại nhầm VB đúng (TT50/CNTT) → `domain_filter: off`
- Kết quả đo: thân còn trích dẫn chưa kiểm chứng 15·18→0·1; dừng sớm 8·9→2·1; lỗi parse 72→2-3.
- Nhấn giới hạn gốc: CitationGuard **không kiểm nghĩa** — "có TT50 trong thân" ≠ "đúng".
- Bảng cờ §10.2: thêm 8 cờ mới của §A.15.

### A2. Part 7 — §9.6, §9.7 (mới)
- **§9.6 `do_lop_loi_agent.py`** (99 câu × 2 lượt): đo 4 lớp lỗi trên TẬP. Bài học: `con_trong_than` (không đếm khối ⚠️ — vì ⚠️ nối SAU khi lược, "16→18" giả trong khi thân thật "15→3"); `lech_dong` có dương-tính-giả; log service không ghi tham số tool → phải chẩn đoán ngoài tiến trình.
- **§9.7 `kiem_do_phu.py`** (cổng độ phủ, 4 bất biến) + **`san_nhieu.py`** (sàn nhiễu). Bài học nguồn kỳ vọng: `corpus` (per-CÂU) vs `run_corpus` (per-LƯỢT) — sửa 09-13.

## B. Bọc fence Mermaid (yêu cầu hiển thị VS Code)
- **74 file `.md` trong `*/mermaid/` các phần: đã fenced sẵn** từ trước (```mermaid ... ```) — kiểm 148/148 OK.
- **Thêm 74 file `.md` fenced trong `so_do_RAG_TaiLieu/mermaid/phan_*/`** (song song `.mmd`) để mọi sơ đồ đều xem được trực tiếp trên VS Code ở cả thư mục trung tâm.
- Giữ nguyên 74 file `.mmd` thô làm nguồn render cho `mmdc` (mmdc cần mermaid thô, không fenced).
- Tổng: 148 file `.md` fenced (0 lỗi) + 74 `.mmd` render sources.

## C. Đã kiểm & ĐÚNG — không sửa
- P1–P5: DESIGN mốc ≤09-13, khớp Round 6.
- `blend_into_legal` vẫn `false` (§A.15 xác nhận lại: ~6/160 câu chạm, dưới sàn nhiễu 3,3).

## D. Build & đóng gói
- Build lại docx+pdf cả 7 phần (bản upload không kèm docx/pdf).
- Đóng gói `RAG-SQLite-docs.zip`.

## E. Ghi chú
- Mọi số liệu/tên cờ/tên file lấy nguyên từ `phase3_generation/DESIGN.md §A.15` và `performance_evaluation/DESIGN.md`; không bịa.
