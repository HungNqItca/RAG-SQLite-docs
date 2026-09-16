flowchart TB
    L_MD["Bản .md đã có bảng<br/>(nối từ Hình 2.1a1)"]
    L_PARSE["_load_md → parse Điều &gt; Khoản &gt; Phụ lục<br/>+ parse_legal_temporal (H1 Pha A: hiệu lực VB)"]
    L_WRITE["Saga ghi 3 store<br/>MongoDB · SQLite chunks · ChromaDB"]
    L_BM25["BM25Builder → index từ khóa legal"]
    L_BACK["backfill_legal_temporal.py (H1 Pha B)<br/>quan hệ liên-VB — BẮT BUỘC sau ingest"]
    L_MD --> L_PARSE --> L_WRITE --> L_BM25 --> L_BACK
    classDef legal fill:#E1F5FE,stroke:#0277BD,stroke-width:2px,color:#01579B
    classDef io fill:#FFF3E0,stroke:#E65100,stroke-width:2px,color:#BF360C
    classDef write fill:#F3E5F5,stroke:#6A1B9A,stroke-width:2px,color:#4A148C
    class L_PARSE,L_BM25,L_BACK legal
    class L_MD io
    class L_WRITE write
