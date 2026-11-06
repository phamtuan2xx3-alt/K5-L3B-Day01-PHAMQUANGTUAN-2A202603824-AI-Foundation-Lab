# Báo cáo cá nhân — Day 01: Lọc tin nhắn rác

- **SEED:** 7646.
- **Nguồn:** output lưu trong [day01_lab.ipynb](day01_lab.ipynb), đối chiếu với JSON xuất cuối. Chỉ số ô dưới đây đếm từ 0 trong danh sách cells của file, không phải số lần thực thi Colab.
- Báo cáo được tổng hợp với AI hỗ trợ. Khi văn bản trả lời trong notebook khác output, dùng output làm bằng chứng.

## 1. Bằng chứng

| Tệp | Trạng thái |
| --- | --- |
| [day01_lab.ipynb](day01_lab.ipynb) | 102 ô, 49 ô có output; không còn raise NotImplementedError, không có output loại error |
| [lab1_report.json](lab1_report.json) | File xuất gốc, kết quả phù hợp bảng tổng hợp trong notebook |
| [hand_calc.jpg](hand_calc.jpg) | Ảnh tính tay câu 1–4 |
| [hand_calc_2.jpg](hand_calc_2.jpg) | Ảnh tính tay câu 5 |

Ô 100 ghi SEED=7646, đề #3, 14/14 check đạt và đủ 10 dự đoán. Danh sách không có check 4.1 + 4.4, nên 14/14 chưa chứng minh đủ mọi check của đề. Ô 95 xác nhận năm giá trị tính tay: 0.87, 0.50, 0.73, 0.60, 10.50.

## 2. Kết quả chính

Ô 10: sau bỏ trùng có 5.169 tin, 653 spam, 4.516 ham. Train/val/test là 3.619/775/775; train có 457 spam, test có 101 spam; SUB có 40 tin.

Bảng chép output ô 79, giữ ba chữ số thập phân:

| Phương án | Precision | Recall | F1 test | F1 SUB | Train (s) | Tin/s cả lô | p50 ms | p95 ms | USD/1k |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| BoW + LogReg | 1.000 | 0.822 | 0.902 | 0.947 | 7.597 | 7852.556 | 0.051 | 0.121 | 0.000 |
| BoW + MLP | 1.000 | 0.941 | 0.969 | 1.000 | 8.146 | 24820.638 | 0.329 | 0.412 | 0.000 |
| Encoder + kNN | 0.832 | 0.931 | 0.879 | 0.947 | 2.101 | 48.330 | 23.957 | 64.557 | 0.000 |
| Encoder + LogReg | 0.949 | 0.921 | 0.935 | 1.000 | 2.481 | 1134.131 | 9.511 | 10.496 | 0.000 |
| Encoder + MLP head | 0.960 | 0.950 | 0.955 | 1.000 | 3.546 | 1112.644 | 8.511 | 16.613 | 0.000 |
| Sonnet zero-shot | 1.000 | 0.950 | 0.974* | 0.974 | 0.000 | 0.501 | 1965.063 | 2495.185 | 0.226 |
| Opus Thinking zero-shot | 1.000 | 1.000 | 1.000* | 1.000 | 0.000 | 0.463 | 2155.892 | 2852.545 | 0.377 |
| Sonnet few-shot | 1.000 | 1.000 | 1.000* | 1.000 | 0.000 | 0.678 | 1437.746 | 2027.748 | 0.642 |

* LLM chỉ đo trên SUB, không phải toàn bộ test. Ô 62 chọn 9router_claude với ag/claude-sonnet-4-6 và ag/claude-opus-4-6-thinking, giới hạn 2048 token. Chi phí là ước tính theo số từ và giá tham khảo, chưa gồm suy luận ẩn; không phải hóa đơn 9Router. Model cục bộ chưa tính chi phí hạ tầng.

Ô 73: Sonnet zero-shot $0.00905/40 tin, Opus $0.01508/40 tin. Ô 76: few-shot $0.02569/40 tin. Cả ba invalid=0. Tính từ số tiền hiển thị: Opus đắt khoảng 1.67 lần Sonnet; few-shot đắt khoảng 2.84 lần zero-shot.

## 3. Dự đoán và thực tế

Ba dự đoán và thời điểm có trong output ô 100:

1. **ml_f1, 16:18:33:** dự đoán F1 khoảng 0.84, precision cao hơn recall. Ô 25 cho F1=0.902, precision=1.000, recall=0.822. F1 cao hơn dự đoán khoảng 0.062; hướng precision/recall đúng. Mô hình chưa bắt hết spam thật.
2. **dl_curve, 16:19:10:** dự đoán MLP gần bằng hoặc nhỉnh hơn LogReg. Ô 40 cho F1=0.969 so với 0.902, tăng khoảng 0.067. Lớp ẩn có thể khai thác tổ hợp feature hữu ích; chưa chứng minh luôn tốt hơn. Chỉ số val loss thấp nhất là 7, tức vòng học thứ 8.
3. **dl_relu, 16:19:18:** dự đoán bỏ ReLU làm giảm F1. Ô 43 vẫn ghi F1=0.969, bằng mạng có ReLU ở độ chính xác hiển thị. Hai Linear không phi tuyến vẫn gộp thành một phép affine; cách tối ưu khác nên không nhất thiết bằng LogReg NumPy.

Dự đoán llm_models lúc 16:19:57 cho rằng Opus hơn Sonnet 0.02–0.05 F1; output chênh khoảng 0.026, nhưng SUB nhỏ nên chưa đủ khẳng định ưu thế ổn định. Dự đoán few-shot lúc 16:22:46 nói zero-shot đã đạt 1.000, không khớp output đang lưu 0.974. Giữ nguyên log gốc, không sửa dự đoán sau khi biết kết quả. Output few-shot thực tế tăng F1 lên 1.000, chi phí tăng khoảng 2.84 lần.

## 4. Phân tích stress test

Ô 88 lưu kết quả **50 case gốc**:

| Model | F1 SUB làm mốc | F1 50 case | Accuracy 50 case |
| --- | ---: | ---: | ---: |
| BoW + LogReg | 0.947 | 0.378 | 0.54 |
| BoW + MLP | 1.000 | 0.410 | 0.54 |
| Encoder + kNN | 0.947 | 0.737 | 0.70 |
| Encoder + LogReg | 1.000 | 0.667 | 0.66 |
| Encoder + MLP head | 1.000 | 0.706 | 0.70 |
| Sonnet zero-shot | 0.974 | 0.982 | 0.98 |
| Sonnet + prompt phòng thủ | 0.974** | 1.000 | 1.00 |

** Mốc SUB của prompt phòng thủ được code lấy từ zero-shot; chưa đo prompt phòng thủ trên SUB.

BoW + MLP tụt nhiều nhất: 1.000 xuống 0.410, giảm 0.590 theo số hiển thị. Test thường cùng phân phối với train; mẫu gần trùng vẫn có thể còn sau bỏ trùng tuyệt đối. BoW nhìn token rời rạc, embedding biểu diễn nghĩa, LLM xử lý ngữ cảnh; đây là cách giải thích xu hướng, không phải bằng chứng nguyên nhân nội bộ của từng dự đoán.

Ô 89 ghi 0 case tất cả model cùng sai, và prompt phòng thủ sai 0/50. Bộ case viết tay giúp phát hiện điểm yếu nhưng không đại diện tỷ lệ spam thực tế.

Hai lỗi cụ thể được output xác nhận, dùng minh họa cơ chế lỗi; không gán chúng thành lỗi trong bộ stress test:

- Ô 58: “Last chance! Your account rewards expire tonight…” là spam, BoW đoán ham, Encoder + LogReg đoán spam. BoW không biểu diễn đầy đủ ý nghĩa kết hợp hạn chót và yêu cầu xác nhận thông tin. Tổng thí nghiệm: BoW 5/6, Encoder + LogReg 6/6.
- Ô 73: Sonnet bỏ sót spam bắt đầu “Dear Voucher holder Have your next meal on us…”. Nội dung ưu đãi có thể bị đọc như thông báo hợp lệ; output không chứng minh nguyên nhân cụ thể. Đây là lỗi SUB, không phải case đã xác định trong stress test.

Output hiện chưa liệt kê hai ID lỗi cụ thể của BoW/embedding trên bộ 50 case. Cần chạy ô đọc lỗi với model tương ứng để bổ sung; không tự gán dự đoán chưa quan sát được.

### Case bổ sung

Ô 92 chứa 5 case ID 100–104 có AI hỗ trợ đề xuất; output xác nhận tổng 55 case. Nhưng chưa có output đánh giá lại 55 case trong file này. Vì vậy không đưa kết quả 55 case hoặc khẳng định #101 bị đoán sai vào báo cáo. Muốn có bằng chứng cần chạy lại ô 88 và 89 sau ô 92.

## 5. Thử thách cá nhân

Ô 97 xác nhận đề #3. Code ô 98 đặt lại seed trước mỗi mạng, giữ 30 epoch, lr=0.001, batch size=64 và cùng dữ liệu. Output ô 98:

| Hidden | Số tham số | F1 test | Train (s) |
| --- | ---: | ---: | ---: |
| 4 | 8009 | 0.964 | 2.329 |
| 512 | 1025025 | 0.959 | 20.044 |

Mạng lớn có khoảng 128 lần tham số, train lâu hơn khoảng **8.61 lần**, F1 không tăng. Chênh 0.005 còn nhỏ nên chưa khẳng định mạng nhỏ luôn tốt hơn. Chọn mạng nhỏ khi chất lượng tương đương và cần tiết kiệm tài nguyên.

Phần chữ ô 99 còn ghi 3.249/19.672 giây và khoảng 6 lần từ lần chạy khác; báo cáo dùng output 2.329/20.044 giây. Phần thử thách có AI hỗ trợ gợi ý code như đã trao đổi; không trình bày đây là bài làm độc lập không AI.

## 6. Phương án cho sản phẩm thật

Ô 82: Pareto gồm BoW + LogReg và BoW + MLP. Đạt p95 dưới 50 ms: BoW + LogReg, BoW + MLP, Encoder + LogReg, Encoder + MLP head. kNN không đạt (64.557 ms), throughput 48.330 tin/giây cũng thấp hơn tải trung bình khoảng 116 tin/giây nếu chỉ dùng cấu hình đo này. LLM có p95 khoảng 2.03–2.85 giây, vượt SLA.

BoW + MLP nhanh (24820.638 tin/giây, p95 0.412 ms, train 8.146 giây) và F1 test 0.969 nhưng F1 stress test 0.410. Chưa thể khẳng định đạt chất lượng triển khai; cần dữ liệu đại diện và kiểm thử tải đồng thời, giờ cao điểm.

Đề xuất router: model cục bộ xử lý tin chắc chắn, chuyển tin lưỡng lự hoặc bất thường sang LLM, kết hợp kiểm tra URL/đầu số. Không chỉ dựa vào khoảng xác suất vì model có thể tự tin khi sai. Chuẩn hóa từ biến dạng có thể hỗ trợ BoW nhưng cũng có nguy cơ làm sai tên riêng/số hợp lệ. Nhánh LLM chậm, cần đo p95 toàn hệ thống; nếu mọi quyết định phải dưới 50 ms thì xử lý LLM bất đồng bộ hoặc dùng model cục bộ khác.

Với 30 ngày là 300 triệu tin/tháng. Tính từ chi phí 40 tin hiển thị ở ô 73/76: Sonnet zero-shot khoảng **67.875 USD/tháng**, Opus **113.100 USD/tháng**, Sonnet few-shot **192.675 USD/tháng**. Công thức: chi phí 40 tin × 300.000.000 / 40. Đây là phép ước tính từ số đã làm tròn trong output, chưa gồm suy luận ẩn và hạ tầng, không phải hóa đơn router. Nếu chỉ chuyển một phần tin sang LLM, chi phí phụ thuộc tỷ lệ chuyển tuyến và độ dài tin.

## 7. AI log phần 4 và điều đã học

Bảng giới hạn ở phần 4, không phải nhật ký AI đầy đủ toàn bài. Đã điều chỉnh nhận xét để khớp output thay vì phần trả lời cũ ở ô 99.

| Phần | Bạn hỏi AI gì | AI trả lời/viết gì | Đối chiếu trong notebook |
| --- | --- | --- | --- |
| 4 — Cấu hình API | Dùng 9Router cho Colab và chọn Claude thế nào? | Endpoint, Secret và Sonnet/Opus | Ô 62 xác nhận cấu hình; ô 65/73 có phản hồi API |
| 4.1 — Gọi API | Viết call_openai sát template | Hàm Chat Completions và đo latency; sửa import cho mock | Có code; danh sách cuối chưa ghi check 4.1 + 4.4 |
| 4.2–4.3 | Viết prompt và parser | Prompt zero/few-shot, chuẩn hóa nhãn | Ô 67 ghi test đạt |
| 4.4 | Hoàn thiện llm_classify_batch | Batch, retry, latency, chi phí | Ô 73/76 có kết quả thật, invalid=0; chưa có output test giả lập 4.1 + 4.4 |
| 4.A–4.B | Gợi ý dự đoán và giải thích kết quả | Nhận định F1, tốc độ, chi phí | Sonnet 0.974, Opus 1.000, few-shot 1.000; tỷ lệ chi phí few-shot khoảng 2.84 |
| 4 — Chi phí | Thêm giá Sonnet/Opus | Code bảng giá tham khảo | Có chi phí trong output; ghi rõ ước tính chưa gồm token suy luận ẩn |

Tôi hiểu precision cao có thể đi cùng recall thấp, và F1 không phải accuracy. Mạng lớn hơn không tự đảm bảo tốt hơn; ở lần chạy này Opus và few-shot nhỉnh hơn Sonnet zero-shot nhưng SUB quá nhỏ để khẳng định ưu thế ổn định. Điểm stress test cho thấy chất lượng trên dữ liệu quen thuộc chưa chứng minh chất lượng ngoài thực tế.