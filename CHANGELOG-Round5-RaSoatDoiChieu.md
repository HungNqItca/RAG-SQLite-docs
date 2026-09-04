# CHANGELOG — Round 5: Rà soát chất lượng, đối chiếu tài liệu ↔ DESIGN.md/README.md ↔ codebase

> **Quy trình 3 bước (theo yêu cầu Quốc Hùng):**
> 1. Đối chiếu tài liệu ↔ `DESIGN.md`/`README.md` → lập danh sách **nghi vấn** sai lệch.
> 2. Với mỗi nghi vấn → đối chiếu **codebase thực tế** (không tin DESIGN.md mù quáng — nó cũng có thể cũ).
> 3. Chỉ khi **xác nhận đúng là sai lệch** → mới cập nhật tài liệu.
>
> Nguyên tắc: nghi vấn chưa verify KHÔNG được coi là sai lệch; số liệu phải đọc từ code, không suy từ trí nhớ.

## A. Đã kiểm và XÁC NHẬN ĐÚNG — KHÔNG sửa (tránh "sửa" cái đang đúng)

| Hạng mục | Doc nói | Code thực tế | Kết luận |
|---|---|---|---|
| Số liệu dữ liệu | 5 VB·460 chunk · 980 dòng · 4 tài liệu·396 chunk | khớp `README.md` gốc | ✅ đúng |
| Số bảng TABULAR | 7 bảng | migrate script tạo đúng 7 (3 hoa + 4 thường) | ✅ đúng |
| Bảng cờ production (P6 §10.2) | 10 cờ với giá trị | khớp `generation_config.yaml` từng dòng | ✅ đúng |
| Corpus agentic | 41 câu | `agentic_eval_corpus.json` = 41 (flat list) | ✅ đúng |
| Frontend `step` handling | onStep/currentStep/MessageBubble | có trong `useChat.js`, `MessageBubble.jsx` | ✅ đúng |
| **Adaptive retrieval "8 rules"** (P4 glossary) | 8 rules chọn strategy | `_select_adaptive_strategy` có đúng Rule 1–8 | ✅ **đúng** (suýt sửa nhầm — đây KHÁC ContentTypeClassifier) |
| RBAC (P3) | 4 tầng, 40 mentions | khớp | ✅ đúng |
| Migration on-prem (P5) | secrets/storage provider | khớp | ✅ đúng |

## B. XÁC NHẬN LÀ SAI LỆCH (đã đối chiếu code) — ĐÃ SỬA

### B1. ContentTypeClassifier: **8 rule → 11 rule**

- **Doc cũ:** "8 rule (R1–R6, R_DOC_REF, R7)".
- **Code thực tế** (`shared/classifiers/query_classifier.py`, đếm `matched_rule=`): **11 rule** — R1, R2, **R2.5**, **R2.6**, **R2.7**, R3, R4, R5, R6, R_DOC_REF, R7. Docstring code cũng ghi "11 rules".
- **Ba rule thiếu:**
  - **R2.5** (conf 0.75, `type_tab_hint='PHI'`) — ngữ cảnh chuyển tiền liên ngân hàng, không cần từ khóa `'phí'`.
  - **R2.6** (conf 0.70, `PHI`) — hành vi dịch vụ PHI ngầm (rút ATM, phát hành thẻ, SMS banking).
  - **R2.7** (conf 0.70, `LAI_SUAT`) — hành vi dịch vụ lãi suất ngầm (gửi tiết kiệm, vay vốn).
- **Đã sửa tại:**
  - **P1:** §5.1a caption, §6 intro prose, §6.1 tiêu đề ("Tám rule"→"Mười một rule") + thêm 3 dòng R2.5/R2.6/R2.7 vào bảng + sửa mô tả thứ tự ưu tiên, §mục lục module (dòng `query_classifier.py`).
  - **P2:** §4.1.2 ("8 rules"→"11 rules" với danh sách đầy đủ).

### B2. Số tool Agentic: **"5 tool" → tối đa 6 tool** (bổ sung `search_general`)

- **Doc cũ:** nói "5 tool" (hoặc "4 tool" cho khối A–F); bảng tool §3.2 chỉ có 4 dòng.
- **Code thực tế** (`core/agent/tools.py::build_default_tools`): đăng ký **5 tool cố định** (SearchLegal, SearchTabular, FetchArticle, Compute, **CheckValidity**) + **`append(SearchGeneralTool)` có điều kiện** khi `gd_enabled` (`general_dispatch.enabled`). Có 6 class Tool cụ thể (trừ base `Tool` abstract).
- **Vì production có `general_dispatch.enabled=true`** ⇒ hệ chạy **6 tool**. `search_general` có được nhắc ở P6 §15 nhưng cách đếm tool không phản ánh cấu hình production, và không nêu tính "đăng ký có điều kiện".
- **Đã sửa tại P6:** §1.2 (ràng buộc đóng kín — nêu đủ tool thứ 5 `check_validity` + tool thứ 6 `search_general` đăng ký có điều kiện), §3.2 (đoạn "phạm vi đóng kín" — thêm mô tả tool 5 & 6 với điều kiện `general_dispatch.enabled`), §18 Tổng kết (2 chỗ: liệt kê tool thứ 6 + "tối đa 6 tool" trong ràng buộc đóng kín).
- **Giữ nguyên** các chỗ "4 tool" mô tả **khối A–F** (§3.2 tiêu đề, §mục lục, cây thư mục, ADR-6.3) vì đúng bối cảnh lịch sử của khối A.

## C. File đã build lại
- **P1, P2, P6:** rebuild `.docx` + `.pdf`. P4/P3/P5/P7 không đụng (đã xác nhận đúng).
- Đóng gói lại `Agentic-RAG-docs-upgrade.zip`.

## D. Ghi chú phương pháp
- Bước 2 (verify code) đã **chặn 1 lần sửa nhầm**: "Adaptive retrieval 8 rules" (P4) trùng con số 8 với lỗi ContentTypeClassifier, nhưng là component KHÁC và con số 8 **đúng** (`_select_adaptive_strategy` có Rule 1–8). Không sửa.
- Không bịa: 3 rule mới (R2.5/6/7) lấy nguyên điều kiện + confidence + `type_tab_hint` từ code; framing tool thứ 6 lấy từ logic `build_default_tools` thực tế.
