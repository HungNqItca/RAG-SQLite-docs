# CHANGELOG — Round 8: Embedding model bge-m3 + metadata bìa + đồng bộ 24-27/9

> Rà codebase + DESIGN*.md sau Round 7. Ba việc chính: (1) cập nhật embedding model đúng theo README gốc; (2) sửa version/date/subtitle mặc định trong build_docx.py; (3) đồng bộ vài thay đổi production mới nhất.

## A. Embedding model: MiniLM → BAAI/bge-m3 (theo README gốc)
- README gốc: **`BAAI/bge-m3` ~2,2 GB, 1024 chiều, dùng từ 2026-09-09**; MiniLM (`paraphrase-multilingual-MiniLM-L12-v2`, 384 chiều, ~490 MB) giữ làm **đường lùi**.
- **Part 1:** sửa 6 chỗ (bảng thư viện, VectorRetriever 384→1024, CSDL ChromaDB dim, lý do chuẩn hoá, dung lượng chromadb, glossary Embedding).
- **Part 2:** sửa 6 chỗ (Bước 5 embedding, bảng tóm tắt, Model:, shared-instances ~2,2GB, _get_embedding_fn, config yaml) + thêm **callout bug đổi chiều 384→1024** (phải dựng lại CẢ BA kho vector; ChromaDB ghim chiều; biến thể tabular nhúng lẫn 666×384 + 314×1024).

## B. Metadata bìa (build_docx.py — cả 7 phần)
- `subtitle`: "RAG-CHATBOT PHÁP LÝ NỘI BỘ" → **"RAG-CHATBOT"**
- `date`: "Tháng 5/2026" → **"Tháng 10/2026"**
- `version`: giữ **"1.0"**; `cover`: giữ **True**
- Build 7 phần **không truyền cờ CLI** để bìa lấy đúng mặc định mới. Đã verify bìa render đúng.

## C. Đồng bộ thay đổi production mới (24-27/9/2026) — Part 2
- **Cổng tin cậy legal `cong_tin_cay_legal`** (enforce, `min_score=0,022`, issue-10, 24/9): thay cổng LLM `dispatch_gate` đã GỠ (8 prompt/~4.000 lượt không đạt). Cổng ĐIỂM tất định thắng cổng LLM phi tất định; đường lùi env `CONG_TIN_CAY_LEGAL_CHE_DO=shadow`.
- **Tokenizer BM25 tabular** `COMPOUND_ONLY → ALL_KHONG_TACH_MA` (issue-11, 24/9): giữ mọi token, từ vựng 276→803; COMPOUND_ONLY giữ làm di sản.

## D. Đã kiểm & ĐÚNG — không sửa
- reranker vẫn `cross-encoder/mmarco-mMiniLMv2-L12-H384-v1` (không đổi).
- Nội dung Round 3-7 (Agentic A-J, lớp lỗi, provenance…) khớp codebase.

## E. Build & đóng gói
- Build lại docx+pdf cả 7 phần bằng build_docx.py đã sửa mặc định.
- Đóng gói `RAG-SQLite-docs.zip`.

## F. Ghi chú
- Không bịa: tên model, số chiều (1024), ngày (2026-09-09), τ=0,022, vocab 276→803 lấy nguyên từ README gốc + `retrieval_config.yaml` + `bm25_builder.py` + `config.py`.
