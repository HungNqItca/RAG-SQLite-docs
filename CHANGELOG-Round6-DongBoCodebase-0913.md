# CHANGELOG — Round 6: Đồng bộ tài liệu với codebase mới nhất (tới 2026-09-13)

> **Bối cảnh:** Bản tài liệu nhận vào ở trạng thái ~2026-09-03/04. Source code đã tiến tới 2026-09-13. Rà soát tổng thể: fingerprint ngày trong mọi DESIGN.md → khoanh vùng thay đổi → đối chiếu code (Bước 2) → chỉ sửa khi xác nhận.
> **Vùng thay đổi chính (theo ngày):** phase1 (09-05→09-11), phase3 (09-11/12), DESIGN_TABULAR (09-11/13), DESIGN_GENERAL (09-11/12), performance_evaluation (09-11/12/13).

## A. Part 2 — RAG Core (nhiều thay đổi nhất)

### A1. Legal pipeline: `.docx` regex → `.md` (IBM Docling)
- 5 VB·460 chunk → **9 VB·1.073 chunk** (2026-09-05); nguồn chính `.md`, `.docx`/`.txt`/`.pdf` là đường lùi.
- Lý do: `_load_docx` bỏ `<w:p>` trong `<w:tbl>` → **337 bảng + 21 Phụ lục (10 VB) mất khỏi index**, hỏng im lặng.
- Thay 1 sơ đồ cũ (2.1a "7 bước") bằng **2 sơ đồ** (2.1a1 convert Docling + 2.1a2 ingest), 3 callout (mất bảng / `_nang_cap_dieu` một-luật-hai-đường / H1 hai pha temporal). Cập nhật bảng dữ liệu + bảng thành phần (`_load_md`, thêm `convert_legal_to_markdown.py`).

### A2. General: converter thứ ba `convert_faq_to_markdown.py` (2026-09-11)
- Bộ câu hỏi Q&A dạng bảng (5 nhóm A–E, 295 hàng): mỗi HÀNG thành một heading, vì general splitter không có khái niệm hàng → `convert_to_markdown.py` gộp 105 câu vào 1 heading_path.
- Ghi 3 kỷ luật: không chèn dòng trống giữa ô · `**Căn cứ:**` là cầu chì · không đè `.md` đã có (exit 3).

### A3. Provenance & drift (§2.1.4 mới)
- Front-matter `source_checksum` (tệp gốc) ở cả 3 converter; dedup theo trục này.
- Nêu khoảng trống: chưa có drift-check tự động (không so `sha256(.docx hiện tại)` vs `source_checksum` đã lưu) + khuyến nghị script (Windows-friendly).

### A4. Tabular filter (2026-09-13)
- `shared/type_tab_resolver` chuẩn hoá khoá (`LAI_SUAT ≡ LAISUAT ≡ lai-suat`); SQL lọc `TYPE_TAB IN (…)` thay `LIKE`.
- **3 regex nhóm khách hàng (DCTC/TOCHUC/CANHAN) đã GỠ** — đo trên 6 câu: bỏ hard-filter tốt hơn mọi trục (recall@5 0.667→1.000).
- `type_tab_filter.{enabled,min_confidence}` (từng là tham số chết) nay nối vào `_gate_hint` (Guard 3 ý định, phase3). Sửa §4.4.2 + chú thích config.

### A5. query_rewriting + HyDE ĐÃ GỠ (2026-09-12)
- Code chết (chỉ đường legacy gọi tới). Sửa: config block, §5.8 (đánh dấu lịch sử), file tree, danh sách handler, dòng "Multi-query rewriting" ở §4.1.

## B. Part 6 — Agentic
- Bảng cờ §10.2: `query_rewriting` → **ĐÃ GỠ (2026-09-12)** (đồng bộ với A5).

## C. Part 7 — Performance Evaluation
- `t_rewrite_done` **LUÔN NULL** (query_rewriting gỡ; cột giữ cho tương thích nền đo).
- Nhánh `agentic` chỉ có metric **từ 2026-09-11** (trước đó 0/8.254 dòng, câu agent vô hình); mọi nền trước 2026-09-11 đo trên tập câu khác — không so trực tiếp.

## D. Part 1 — Tổng quan
- Dòng mô tả `phase1_indexing`: "parse .docx" → "convert .docx→.md (giữ bảng, Docling)".

## E. Đã kiểm và ĐÚNG — không sửa
- General 4 tài liệu·396 chunk, tabular 980 dòng/7 bảng, corpus agentic 41 câu — khớp.
- bff-service (Part 3) + agribank-chat (Part 4): DESIGN mốc 09-03/04, khớp tài liệu — không đụng.

## F. Build & đóng gói
- Build lại `.docx`+`.pdf` cả 7 phần (bản upload không kèm docx/pdf).
- Sơ đồ: bỏ `hinh_2_1a_legal_pipeline`, thêm `hinh_2_1a1_legal_convert` + `hinh_2_1a2_legal_ingest`.
- Đóng gói **`RAG-SQLite-docs.zip`** (tên mới theo yêu cầu).

## G. Ghi chú phương pháp
- Bước 2 tránh 1 sai sót: `type_tab_filter` KHÔNG còn là "dead config" như Round trước tưởng — đã được nối vào `_gate_hint` (đọc code `generation_orchestrator.py:418` xác nhận), nên tài liệu mô tả nó là "cổng ý định", không phải "tham số chết".
- Mọi số liệu/tên hàm lấy nguyên từ DESIGN.md + source; không bịa.
