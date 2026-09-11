# AGENTS.md — YuE2 Console

## Mục tiêu dự án
Xây dựng web app thật cho **YuE2 Console** — bộ soạn nhạc chạy trên GPU cục bộ, dựa trên model YuE2 (https://github.com/multimodal-art-projection/YuE) và tham khảo giao diện/luồng của Ladypoly/YuE2_WebUI (https://github.com/Ladypoly/YuE2_WebUI).

## Tài liệu tham khảo bắt buộc
- `prototype/yue2_console.html`: **prototype UI tương tác** (mock, chưa nối model thật), song ngữ EN/VI. Đây là bản thiết kế/UX chuẩn — **mọi AI hoặc engineer tiếp theo phải mở file này trong trình duyệt trước khi code**, và dùng nó làm tiêu chí đối chiếu khi acceptance-test từng phần đã build xong.
- Repo gốc tham khảo: Ladypoly/YuE2_WebUI (giao diện), multimodal-art-projection/YuE (model).

## Quy trình cho AI / engineer mới
1. Mở `prototype/yue2_console.html` trực tiếp trong trình duyệt (double-click hoặc `file://`), thử toàn bộ luồng: Start from an idea → Compose → Run 4 giai đoạn → Now playing — ở cả hai ngôn ngữ EN/VI.
2. Đối chiếu mã nguồn thật đang có trong repo (nếu đã có phần nào được code) với hành vi/giao diện của prototype để xác định đang ở tiến độ nào.
3. Khi nhận task mới, luôn ưu tiên khớp UI/UX/behavior với prototype, trừ khi có ghi chú khác trong issue/PR.
4. Sau khi hoàn thành một phần, tick checkbox tương ứng trong danh sách Task bên dưới và ghi chú ngắn dạng "Done in <path/PR>".

## Task cần làm (theo tiến độ)

### 0. Hạ tầng dự án
- [ ] Khởi tạo cấu trúc repo: `backend/`, `frontend/`, `scripts/`, `docs/`.
- [ ] Chọn stack backend (khuyến nghị Python/FastAPI vì model chạy Python) + frontend (có thể tái dùng HTML/CSS/JS của prototype hoặc build lại bằng React).
- [ ] Thiết lập môi trường: dependencies cho YuE2, llama.cpp/Ollama, stable-diffusion.cpp.

### 1. Backend — tích hợp model YuE2
- [ ] Load & chạy model YuE2 cho 4 giai đoạn: Score → Music tokens → Synthesis → Decode (khớp thứ tự trong prototype).
- [ ] Expose API/WebSocket để frontend nhận progress theo thời gian thực: số token, tok/s, ETA cho từng giai đoạn (giống format hiển thị trong `stage-meta` của prototype).
- [ ] Hỗ trợ 3 Planning mode: Full plan (`cot=full`), Melody only (`cot=melody`), Direct (`cot=off`).
- [ ] Hỗ trợ upload ABC score (input tùy chọn).
- [ ] Hỗ trợ seed field (-1 = random).
- [ ] Advanced: cho phép chỉnh sampling (temperature, top-p) riêng cho từng giai đoạn.

### 2. Backend — Writer (sinh lyrics/title/style)
- [ ] Kết nối LLM backend thật cho nút "Generate" (ứng với ô "Start from an idea"): sinh Title, Style prompt, Lyrics từ một dòng ý tưởng.
- [ ] Hỗ trợ chọn backend llama.cpp hoặc Ollama (segmented control trong prototype).

### 3. Backend — Cover art
- [ ] Tích hợp stable-diffusion.cpp để vẽ ảnh bìa trước khi nhạc chạy, làm nét dần theo % hoàn thành từng giai đoạn (khớp hiệu ứng blur giảm dần trong prototype).

### 4. Backend — GPU/VRAM & artifacts
- [ ] API đọc VRAM thực tế của GPU, hiển thị thanh & text (khớp `vram-fill` / `vram-text`).
- [ ] Nút "Free VRAM" thực sự giải phóng model khỏi GPU.
- [ ] Lưu kết quả (audio, cover, metadata) vào `save_artifacts/<timestamp>/` — khớp format hiển thị trong `artifacts-note`.

### 5. Frontend
- [ ] Nối toàn bộ UI của prototype (topbar VRAM, Compose panel, Run progress, Player) với API thật qua WebSocket/polling.
- [ ] Giữ nguyên hành vi song ngữ EN/VI (toggle trong topbar) — mọi text mới thêm phải có bản dịch cả hai ngôn ngữ.
- [ ] Player thật: phát file audio thực tế thay cho mô phỏng, waveform/spectrum lấy dữ liệu thực từ audio nếu có thể.
- [ ] Responsive mobile/desktop như prototype đã kiểm chứng.

### 6. QA & release
- [ ] Test end-to-end: từ ý tưởng → chạy 4 giai đoạn → nghe kết quả → tìm thấy file trong `save_artifacts/`.
- [ ] Test cả hai ngôn ngữ EN/VI, cả desktop/mobile.
- [ ] Viết README hướng dẫn cài đặt & chạy (yêu cầu GPU, VRAM tối thiểu).
- [ ] Cập nhật checklist trên khi hoàn thành từng phần.

## Giấy phép
- Code tham khảo: Apache 2.0 (theo Ladypoly/YuE2_WebUI).
- Model weights YuE2: CC BY-NC 4.0 — không dùng cho mục đích thương mại.
