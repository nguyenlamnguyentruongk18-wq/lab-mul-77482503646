# Lab 2 — Trả lời câu hỏi


## Phần 1: Linux

### Q1
- **Lệnh đã dùng:**
    head -n 1 logs/*.log
    tail -n 1 logs/*.log
- **Kết quả & trả lời:**
  - Commit `a1f3c09` (`run_01.log`): mô phỏng bị **treo** (`FAIL: het thoi gian sau 0 phep nhan`).
  - Commit `5be2d14` (`run_02.log`, `run_03.log`): cho kết quả **sai** (103/206 và 88/206 phép nhân sai).
  - Commit `c7e90aa` (`run_04.log`, `run_05.log`): chạy **đúng** (`PASS: 206/206 phep nhan dung`).
  - Theo thông báo testbench (`req_ready hoac resp_valid khong len 1?`), phiên bản bị treo có thể hỏng ở tín hiệu **`req_ready`** hoặc **`resp_valid`**.

### Q2
- **Lệnh đã dùng:**
    grep -h 'phep nhan sai' logs/*.log | awk '{s += $2} END {print s}'
    grep -n -C 2 "LOI" tb/tb_mul16_smoke.sv
- **Kết quả & trả lời:**
  - Tổng cộng có **191** phép nhân sai (`103 + 88 = 191`).
  - Số dòng `LOI` chỉ có **20** (nhỏ hơn 191) vì trong testbench `tb_mul16_smoke.sv` có giới hạn chỉ in tối đa **10** lỗi đầu tiên cho mỗi lần chạy (2 file `run_02.log`, `run_03.log` × 10 dòng = 20 dòng).

### Q3
- **Lệnh đã dùng:**
    grep -oh 'b=0x[0-9a-f]' logs/*.log | sort | uniq -c
- **Kết quả & trả lời:**
  - Mọi phép nhân sai đều có chữ số hex đầu tiên của `b` nằm từ `0x8` đến `0xf` (`8, 9, a, b, d, e, f`), nghĩa là bit cao nhất **`b[15]` luôn bằng `1`**.
  - Bit bị bỏ qua là **`b[15]`** (bit thứ 15, chu kỳ tính cuối cùng).
  - Dự đoán lỗi nằm ở điều kiện chuyển trạng thái từ `CALC` sang `DONE` trong **FSM** (bộ điều khiển kết thúc sớm 1 chu kỳ, ví dụ kiểm tra `cnt == W-2` thay vì `cnt == W-1`).

### Q4
- **Lệnh đã dùng:**
    grep "b=0x8001" logs/*.log
    python3 -c "print(hex(0x0003 * (0x8001 % 0x8000)))"
- **Kết quả & trả lời:**
  - Kết quả từ Python: `0x3` (tức `0x00000003`).
  - Giá trị `p` thực tế trong log của phép nhân `a=0x0003 b=0x8001` đúng bằng `0x00000003` (trùng khớp với `a * (b mod 0x8000)` vì bit `b[15]` đã bị bỏ qua).

### Q5
- **Lệnh đã dùng:**
    grep -h LOI logs/*.log > doc/loi-mau.txt
    grep -h LOI logs/*.log > doc/loi-mau.txt
    wc -l doc/loi-mau.txt
    grep -h LOI logs/*.log >> doc/loi-mau.txt
    wc -l doc/loi-mau.txt
- **Kết quả & trả lời:**
  - `>` dùng để **ghi đè** (xóa toàn bộ nội dung cũ trước khi ghi mới), nên chạy 2 lần liên tiếp `wc -l` vẫn cho kết quả **20** dòng.
  - `>>` dùng để **ghi nối** (thêm vào cuối file, giữ nguyên nội dung cũ), nên chạy thêm 1 lần nữa `wc -l` tăng lên **40** dòng.