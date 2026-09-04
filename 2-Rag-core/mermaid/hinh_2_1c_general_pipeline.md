flowchart TB
    SRC[".docx / .pdf gốc<br/>(sổ tay, công văn, báo cáo — KHÔNG có Điều/Khoản)"] --> CONV["convert_to_markdown.py --profile<br/>→ .md (cấp bậc heading do người soạn KHAI)"]
    CONV --> ING["ingest_general_docs.py --dir ... --yes<br/>backup → dry-run + xác nhận → nạp<br/>→ rebuild BM25 một lần → cổng H-1…H-16"]
    ING --> SPLIT["GeneralDocumentSplitter<br/>section lá → chunk (part_index/part_total)"]
    SPLIT --> SQL["SQLite general_*<br/>(dedup theo source_checksum)"]
    SPLIT --> CHR["ChromaDB general_chunks"]
    SQL --> BM["BM25 general riêng<br/>general_bm25_statistics / _term_frequencies"]
    CHR --> SIG["general_reload_signal.txt<br/>hot-reload vector; BM25 vẫn cần restart"]
