# Phần 7 — Performance Evaluation

Tài liệu hóa phân hệ `performance_evaluation/` — hệ thống đo lường hiệu năng và chất lượng của RAG-Chatbot, phục vụ nghiên cứu khoa học và chuyển giao công nghệ. Phân hệ này **ngang hàng** với `rag-core`, `bff-service`, `agribank-chat` (sibling của `rag-core/`).

## Trạng thái

| Hạng mục | Giá trị |
|---|---|
| Số trang | ~25 |
| Số sơ đồ | 7 (8 file, do 7.2 chia a/b) |
| Nguồn | `performance_evaluation/DESIGN.md` (1096 dòng, cập nhật 2026-09-03) + `BC_NCKH.md` |
| Trạng thái | ✅ Hoàn thành |

## Cấu trúc nội dung

| § | Tiêu đề | Sơ đồ |
|:-:|---|:-:|
| 1 | Giới thiệu: 5 RQ + 4 ràng buộc + nguyên tắc bao trùm | — |
| 2 | Kiến trúc thành phần + vị trí trong hệ thống | Hình 7.1 |
| 3 | Mô hình đo — 10 timestamp + 3 dispatch path | Hình 7.2a, 7.2b, 7.3 |
| 4 | Database schema | Hình 7.4 |
| 5 | Instrumentation Layer (QueryMetrics + MetricsLogger) | — |
| 6 | Test Corpus G1–G8 + reproducibility | — |
| 7 | Runners + Analysis + kiểm định thống kê | Hình 7.5, 7.6 |
| 8 | Điểm tích hợp RAG Core | — |
| 9 | Kỷ luật đo lường — chống "xanh giả" | Hình 7.7 |
| 10 | Bộ eval Agentic | — |
| 11 | ADR (7 quyết định) | — |

## 5 câu hỏi nghiên cứu (RQ)

RQ1 phân rã latency theo phase · RQ2 latency p50/p95/p99 ở 1 user · RQ3 latency khi tải 1→20 user · RQ4 overhead RAG vs Vanilla Gemini · RQ5 chất lượng (citation accuracy, hallucination, relevance).

## Bốn ràng buộc thiết kế

Non-intrusive · Reproducible · Offline-compatible · Unified format.

> Nguyên tắc bao trùm: *một con số chỉ có giá trị khi phép đo sinh ra nó đã được chứng minh là chạy thật và đo đúng thứ cần đo.*

## Sơ đồ — 7 hình (tất cả TB)

| # | Sơ đồ | Mục |
|:-:|---|---|
| 1 | hinh_7_1_kien_truc_pe | §2 Kiến trúc + vị trí |
| 2-3 | hinh_7_2a / hinh_7_2b | §3 10 timestamp (tiền-LLM / LLM) |
| 4 | hinh_7_3_dispatch_paths | §3 Ba dispatch path + fallback |
| 5 | hinh_7_4_schema | §4 Schema metrics.db |
| 6 | hinh_7_5_dataflow | §7 Luồng dữ liệu end-to-end |
| 7 | hinh_7_6_stat_tests | §7 Kiểm định phi tham số |
| 8 | hinh_7_7_measurement_discipline | §9 Chống "xanh giả" |

Hình 7.2 (10 mốc thời gian) được chia a/b tại ranh giới prompt-built theo ràng buộc §3.5 Docs-Design-Requirement; hai sơ đồ chuỗi tuyến tính được `build_docx.py` auto-scale fit A4 — đã verify chữ đọc rõ.

## Rebuild

```bash
cd <workspace>
for f in 7-Performance-eval/mermaid/*.mmd; do
  name=$(basename "$f" .mmd)
  mmdc -i "$f" -o "7-Performance-eval/png/${name}.png" \
       -c so_do_RAG_TaiLieu/mermaid_config.json \
       -p so_do_RAG_TaiLieu/puppeteer.json -b white -w 1600
done
cd 7-Performance-eval/source
python3 build_docx.py phan_7_performance_eval.md phan_7_performance_eval.docx \
    --title "TÀI LIỆU THIẾT KẾ HỆ THỐNG" \
    --subtitle "RAG-CHATBOT PHÁP LÝ NỘI BỘ AGRIBANK — PHẦN 7" \
    --version "Bản v1 — Phần 7 (Performance Evaluation)"
soffice --headless --convert-to pdf phan_7_performance_eval.docx
```

## Tham khảo source

- `performance_evaluation/DESIGN.md` — thiết kế đầy đủ (§1–9 + phụ lục Agentic).
- `performance_evaluation/BC_NCKH.md`, `BC_NCKH_v2.md` — báo cáo nghiên cứu khoa học.
- `performance_evaluation/agentic_eval/` — bộ eval nhánh Agentic (liên hệ Phần 6 §8).
