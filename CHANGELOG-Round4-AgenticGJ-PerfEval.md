# CHANGELOG — Round 4: Mở rộng Phần 6 (Agentic G–J) + Phần 7 (Performance Evaluation)

> Đối chiếu bộ tài liệu (đã có Phần 6 cover Agentic A–F từ Round 3, và đã qua vòng "Cập nhật theo codebase") với codebase `RAG-SQLite-agentic-rag-chatbot` mới nhất.
> **Phát hiện:** (1) Phần 6 mới cover Agentic A–F; source đã tiến tới giai đoạn G–J với nhiều năng lực mới chưa tài liệu hóa. (2) Phân hệ `performance_evaluation/` — ngang hàng với 3 service chính — chưa có tài liệu thiết kế riêng.
> **Phương pháp:** đọc trực tiếp `phase3_generation/DESIGN.md` (§A.10–A.14) và `performance_evaluation/DESIGN.md` (1096 dòng); code/SQL snippet lấy nguyên văn.

## A. Mở rộng PHẦN 6 — thêm §10–§17 (giai đoạn Agentic G–J)

Phần 6 tăng từ 30 trang / 7 sơ đồ (A–F) lên **54 trang / 16 sơ đồ** (A–J). Giữ nguyên §1–§9 (khối A–F), bổ sung:

| § | Nội dung mới | Nguồn |
|---|---|---|
| 10 | Ba trục Guard + bảng cờ production ("đĩa ≠ process") | DESIGN §A.10–A.13, flag table |
| 11 | ToolContext — sự thật của lượt (bộ nhớ agent, G2) | DESIGN §A.2.0, §A.2.5 |
| 12 | search_tabular nâng cao — filter + effective_date (G1) | DESIGN §4, tools.py |
| 13 | FeeGuard — trục GIÁ TRỊ + VAI TRÒ (I) | DESIGN §A.10, fee_guard.py |
| 14 | Trục HIỆU LỰC — check_validity (tool 5) + TemporalGuard (H) | DESIGN §A.11, §A.12 |
| 15 | General dispatch + saga blend_into_legal (lật 2 lần) | DESIGN flag table, config yaml |
| 16 | Observability Prometheus (J4-b) + chống "xanh giả" (A.14) | DESIGN §A.13, §A.14 |
| 17 | 8 ADR nâng cao (ADR-6.9 … ADR-6.16) | tổng hợp |

**8 sơ đồ mới (6.8–6.15), đều TB:** ba trục Guard · ToolContext · FeeGuard · check_validity · TemporalGuard · general/blend · observability · anti-green. Hai sơ đồ chuỗi tuyến tính (6.3a, 6.4 từ trước + 6.15 mới) dùng auto-scale fit A4.

**Cập nhật §1–§9 cho nhất quán:** mục lục thêm §10–§17; ghi chú "4 tool → 5 tool" (check_validity thêm ở H) ở §1.2/§3.2; §18 Tổng kết viết lại cho scope A–J.

## B. Tạo PHẦN 7 — Performance Evaluation (mới, ~25 trang, 7 sơ đồ)

Phân hệ `performance_evaluation/` là sibling của `rag-core/`, có DESIGN.md riêng 1096 dòng (cập nhật 2026-09-03), nhưng chưa được tài liệu hóa thành phần riêng — chỉ nhắc thoáng ở P1/P2/P6. Tạo Phần 7 đầy đủ:

| § | Nội dung | Nguồn |
|---|---|---|
| 1 | 5 RQ + 4 ràng buộc + nguyên tắc bao trùm | DESIGN §1 |
| 2 | Kiến trúc + vị trí (sibling rag-core) | DESIGN §2 |
| 3 | 10 timestamp + 3 dispatch path | DESIGN §3 |
| 4 | Schema query_metrics/corpus/quality_eval + view | DESIGN §4 |
| 5 | QueryMetrics + MetricsLogger (singleton, best-effort, WAL) | DESIGN §5.1 |
| 6 | Corpus G1–G8 + reproducibility (seed 42, hash) | DESIGN §5.2 |
| 7 | Runners + Analysis + kiểm định phi tham số | DESIGN §5.3, §5.4 |
| 8 | 4 điểm tích hợp RAG Core (non-breaking) | DESIGN §6 |
| 9 | Kỷ luật chống "xanh giả" (4 lớp lỗi thật) | DESIGN §5.2, phụ lục Agentic |
| 10 | Bộ eval Agentic (N1–N4, 2 gate cứng) | DESIGN phụ lục |
| 11 | 7 ADR (ADR-7.1 … ADR-7.7) | DESIGN §7 |

**7 sơ đồ (8 file), đều TB:** kiến trúc PE · 10 timestamp (chia 7.2a/7.2b) · dispatch path · schema · dataflow · stat tests · measurement discipline. Sơ đồ 7.2 (10 mốc) chia a/b tại ranh giới prompt-built (tràn >50cm nếu để nguyên).

## C. Cập nhật cross-reference các phần cũ

- **P1 §1:** "sáu phần" → "bảy phần"; bảng cấu trúc cập nhật P6 (5 tool, ba trục Guard, A–J) + thêm dòng P7.
- **P2 §4:** callout Agentic mở rộng — "4 tool → 5 tool, ba trục Guard", liệt kê module `shared/` mới (fee_calculator, legal_temporal_parser, general_helpers, system_scope, vn_currency) trỏ sang Phần 6.
- **P4 §5.4:** callout event `step` giữ nguyên (vẫn đúng).

## D. Rà soát P1–P5 theo code mới nhất — kết quả
- **P5 (Hạ tầng):** đã có Grafana/Prometheus/HTTPS đầy đủ (12–14 mentions) — **current, không sửa**.
- **P3 (BFF):** permanent history đã tài liệu hóa (Round 2) — **current, không sửa**.
- **P2 (RAG Core):** `aggregation_intent`/`numeric_entities` đã đúng (vòng "Cập nhật theo codebase") — chỉ thêm cross-ref module shared mới.
- **P1/P4:** cross-ref Agentic từ Round 3 còn nguyên, chỉ cập nhật bảng phần.

## E. File đã build lại / tạo mới
- **Phần 6:** rebuild — .md (971 dòng) + 16 PNG + .docx (846 KB) + .pdf (54 trang).
- **Phần 7:** mới — .md + 8 PNG + .docx (419 KB) + .pdf (25 trang) + README.
- **Phần 1, 2:** rebuild .docx + .pdf (cross-ref P7 + module shared).
- **README.md (root):** bảng trạng thái 7 phần + mục Phần 6 (A–J) + mục Phần 7 + cây thư mục.
- **so_do_RAG_TaiLieu/:** thêm `mermaid/phan_7/` (8 .mmd) + `png/phan_7/` (8 PNG); `phan_6/` cập nhật 16 .mmd/PNG.
- Output đóng gói vào **`Agentic-RAG-docs-upgrade.zip`** (tên mới theo yêu cầu Round 4).

## F. Bug/bài học đã tài liệu hóa thêm (từ chu trình G–J)
- ToolContext: LLM diễn giải mất tên ngân hàng (UAT-FEE-MULTI-01) → sự thật của lượt phải đi theo request.
- FeeGuard: `AMOUNT_CCY="VNĐ"` hardcode → biểu phí ngoại tệ bị từ chối oan; không quy đổi tỉ giá.
- check_validity: ca C không được trả "active" (closed-world).
- TemporalGuard: `TEMPORAL_BLOCK_MARKER` là hợp đồng scorer, không regex "hiệu lực" chung.
- blend_into_legal: lật 2 lần rồi lùi — không corpus nào chạm blend (giao 3 đặc tả là một lỗ).
- PE: hash corpus chép lệch → báo động giả; runner ở scratchpad → gate chưa chạy lần nào; `_is_refusal("")=False` → hallucination_rate xanh giả khi answer rỗng.

## G. Sửa lỗi thực chất: Phase 1 có BA pipeline (không phải hai) — theo phản hồi

**Phát hiện (từ README gốc + `phase1_indexing/DESIGN.md`):** Phase 1 Indexing thực chất có **BA pipeline song song, tách hẳn nhau** (bảng SQLite riêng, index BM25 riêng) — legal, tabular, **general** — không phải hai. Pipeline `general` (tài liệu nội bộ `.md`, 4 tài liệu · 396 chunk) được bổ sung ở giai đoạn GENERAL và **KHÔNG đi qua `ContentTypeClassifier`** — nó vào cùng pool ở `retrieve_unified_sync()` và giành handler bằng rerank, gác bởi `general_dispatch.enabled`.

**Đã sửa:**
- **P2 §2.1:** "hai pipeline" → "ba pipeline"; thêm bảng 3 pipeline (legal/tabular/general) với nguồn + đích lưu + số liệu đo 2026-09-03; thêm tiểu mục **§2.1.3 Pipeline General** (convert .md, 3 ràng buộc riêng: dedup theo source_checksum, doc_family khai tường minh, 3 điều không chép từ khuôn legal); thêm **Hình 2.1c** (sơ đồ pipeline general, TB, 22.1cm fit A4); bổ sung 4 thành phần general vào bảng §2.1.4; sửa ghi chú cross-contamination §N5 thành "ba pipeline".
- **P2 §3.4.2:** thêm ghi chú nhánh thứ ba `include_general` trong `retrieve_unified_sync()` (thân cũ giữ nguyên khi `False`; `max_workers=3` + `_run_general()` khi `True`) — không sửa snippet cũ (vẫn đúng cho `include_general=False`), tránh bịa code.
- **P1 §6:** "Hai pipeline song song" → "Ba pipeline dữ liệu song song"; làm rõ `ContentTypeClassifier` là bộ phân loại **nhị phân** (legal/tabular) còn general giành handler bằng rerank ngoài classifier; sửa **N5** thành ba pipeline; sửa text quanh Hình 10.1 (ghi rõ general tuân cùng mô hình cách ly, không vẽ để giữ sơ đồ gọn).

**Nguyên tắc giữ đúng:** không bịa code (snippet unified giữ nguyên phần đúng, chỉ thêm ghi chú nhánh mới); phân biệt rõ "ContentTypeClassifier nhị phân" (đúng) với "hệ có 3 pipeline dữ liệu" (đúng) — không đánh đồng hai khái niệm.
