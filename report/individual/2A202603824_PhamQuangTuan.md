# Báo cáo cá nhân — Day 01: Lọc tin nhắn rác

- **SEED:** 7646
- Thông tin định danh nằm trong tên file theo quy định bảo mật của repo.
- Báo cáo được biên soạn với AI hỗ trợ, từ kết quả học viên cung cấp trong phiên làm bài.

## 1. Bằng chứng

| Tệp | Trạng thái |
| --- | --- |
| [day01_lab.ipynb](day01_lab.ipynb) | Đã có bản Colab hoàn chỉnh với output, không còn NotImplementedError hoặc output lỗi |
| [lab1_report.json](lab1_report.json) | Đã có file gốc xuất từ Colab: SEED 7646, challenge=2 (đề #3), đủ 10 dự đoán, 14/14 check được ghi đạt |
| [hand_calc.jpg](hand_calc.jpg) | Đã có ảnh viết tay câu 1–4, có bước tính |
| [hand_calc_2.jpg](hand_calc_2.jpg) | Đã có ảnh viết tay câu 5, tổng 10.50 USD/ngày |

## 2. Kết quả chính

### Kết quả lần xuất cuối — dùng làm mốc chính

Các số liệu dưới đây lấy trực tiếp từ `lab1_report.json` được xuất cuối cùng. Bảng và phân tích lần chạy trước ở các mục sau được giữ để phân biệt biến động giữa các lần đo, không thay thế dữ liệu gốc này.

| Phương án | F1 test | F1 SUB 40 | Train (s) | Tin/s cả lô | p95 (ms/tin) | USD/1.000 tin |
| --- | ---: | ---: | ---: | ---: | ---: | ---: |
| BoW + LogReg | 0.902 | 0.947 | 7.597 | 7852.556 | 0.121 | 0 |
| BoW + MLP | 0.969 | 1.000 | 8.146 | 24820.638 | 0.412 | 0 |
| Encoder + kNN | 0.879 | 0.947 | 2.101 | 48.330 | 64.557 | 0 |
| Encoder + LogReg | 0.935 | 1.000 | 2.481 | 1134.131 | 10.496 | 0 |
| Encoder + MLP head | 0.955 | 1.000 | 3.546 | 1112.644 | 16.613 | 0 |
| Sonnet zero-shot | 0.974* | 0.974 | 0 | 0.501 | 2495.185 | 0.2262 |
| Opus Thinking zero-shot | 1.000* | 1.000 | 0 | 0.463 | 2852.545 | 0.3770 |
| Sonnet few-shot | 1.000* | 1.000 | 0 | 0.678 | 2027.748 | 0.6422 |

Ở lần cuối, Opus cao hơn Sonnet khoảng 0.026 F1 và few-shot tăng Sonnet từ 0.974 lên 1.000. Chênh lệch nhỏ trên 40 tin chưa đủ khẳng định ưu thế chắc chắn. kNN không đạt SLA ở lần này (p95 64.557 ms); BoW + LogReg, BoW + MLP, Encoder + LogReg và Encoder + MLP head đạt. Các LLM vẫn vượt SLA. Chi phí/tháng tham khảo với 300 triệu tin lần lượt khoảng 67.860, 113.100 và 192.660 USD theo các số chưa làm tròn.

JSON có đủ 10 dự đoán với thời gian. Dự đoán `llm_fewshot` vẫn nói zero-shot đã đạt 1.000, phù hợp với lần chạy trước nhưng khác lần xuất cuối 0.974; giữ nguyên log gốc thay vì sửa lại dự đoán. JSON chưa có check `4.1 + 4.4 call_openai / llm_classify_batch`, nên 14/14 không chứng minh đã đủ mọi check của đề. Cần chạy lại ô kiểm tra đó và xuất lại JSON để bổ sung bằng chứng.

Notebook hiện lưu kết quả đánh giá 50 case gốc; các kết quả 55 case ở mục 4 là kết quả học viên đã gửi từ lần chạy trước. Muốn notebook chứa bằng chứng 55 case cần chạy lại ô đánh giá sau ô thêm `MY_CASES`, rồi tải lại notebook.

### Số liệu lần chạy trước — dùng đối chiếu

Dữ liệu sau bỏ trùng: 5.169 tin, gồm 4.516 ham và 653 spam. Train/val/test lần lượt 3.619/775/775 tin. SUB có 40 tin cân bằng.

| Phương án | F1 test | F1 SUB 40 | Train (s) | Tin/s cả lô | p95 (ms/tin) | USD/1.000 tin |
| --- | ---: | ---: | ---: | ---: | ---: | ---: |
| BoW + LogReg | 0.902 | 0.947 | 9.140 | 10651.483 | 0.241 | 0 |
| BoW + MLP | 0.969 | 1.000 | 10.906 | 15134.281 | 0.374 | 0 |
| Encoder + kNN | 0.879 | 0.947 | 2.240 | 115.349 | 18.470 | 0 |
| Encoder + LogReg | 0.935 | 1.000 | 2.588 | 1287.705 | 7.036 | 0 |
| Encoder + MLP head | 0.955 | 1.000 | 3.285 | 1646.787 | 9.428 | 0 |
| Sonnet zero-shot | 1.000* | 1.000 | 0 | 0.628 | 2200.783 | 0.226 |
| Opus Thinking zero-shot | 1.000* | 1.000 | 0 | 0.520 | 2319.178 | 0.377 |
| Sonnet few-shot | 1.000* | 1.000 | 0 | 0.544 | 2388.752 | 0.642 |

\* LLM chỉ được đánh giá trên SUB, không phải toàn bộ test. Model thực dùng qua 9Router: `ag/claude-sonnet-4-6` và `ag/claude-opus-4-6-thinking`. Chi phí là ước tính theo số từ và giá API tham khảo Sonnet 3/15 USD, Opus 5/25 USD mỗi triệu token input/output; chưa tính token suy luận ẩn và không phải hóa đơn 9Router. Giá 0 của model cục bộ không bao gồm hạ tầng.

## 3. Dự đoán và thực tế

Các dự đoán dưới đây được đối chiếu theo nội dung trao đổi; thời điểm và nguyên văn cần được xác nhận bằng JSON xuất từ notebook.

1. **Learning rate:** dự đoán lr=50 có thể dao động hoặc tăng mạnh. Thực tế loss tăng vọt ban đầu rồi giảm đến 0.006; learning rate lớn không đồng nghĩa luôn phân kỳ. lr=0.001 học chậm, loss cuối 0.627, còn lr=0.5 đạt 0.086.
2. **Bỏ ReLU:** dự đoán F1 giảm so với MLP. Thực tế hai mạng cùng F1 0.969 ở ba chữ số thập phân. Hai Linear không ReLU vẫn tương đương một phép affine; khả năng biểu diễn phi tuyến chưa cải thiện F1 ở phép đo này. Cách tối ưu khác khiến kết quả không nhất thiết bằng LogReg NumPy.
3. **Model lớn/nhỏ:** dự đoán Opus có thể cao hơn Sonnet 0.02–0.05 F1. Thực tế cả hai đạt 1.000 trên SUB. Tập nhỏ và tác vụ đơn giản chưa cho thấy lợi ích model lớn; Opus có chi phí tham khảo cao hơn khoảng 1.67 lần. Few-shot cũng giữ F1 nhưng tăng chi phí khoảng 2.84 lần.

## 4. Phân tích stress test và case bổ sung

| Model | F1 SUB | F1 50 case gốc | Accuracy 50 case | F1 55 case | Accuracy 55 case |
| --- | ---: | ---: | ---: | ---: | ---: |
| BoW + LogReg | 0.947 | 0.378 | 0.54 | 0.341 | 0.509 |
| BoW + MLP | 1.000 | 0.410 | 0.54 | 0.409 | 0.527 |
| Encoder + kNN | 0.947 | 0.737 | 0.70 | 0.738 | 0.691 |
| Encoder + LogReg | 1.000 | 0.667 | 0.66 | 0.607 | 0.600 |
| Encoder + MLP head | 1.000 | 0.706 | 0.70 | 0.643 | 0.636 |
| Sonnet zero-shot | 1.000 | 0.982 | 0.98 | 0.984 | 0.982 |
| Sonnet + phòng thủ | 1.000** | 1.000 | 1.00 | 0.985 | 0.982 |

\** Cột SUB của phương án phòng thủ được code lấy từ zero-shot làm mốc; chưa đo prompt phòng thủ trên SUB.

BoW + MLP giảm mạnh nhất: F1 giảm 0.590 trên 50 case gốc. Test thường cùng phân phối với train, còn các mẫu spam gần trùng có thể khác số hoặc mã thưởng, nên kết quả dễ lạc quan. BoW chỉ nhìn token rời rạc: trên 50 case, MLP đúng 0% spam giọng tự nhiên và 17% né từ khóa. Embedding biểu diễn nghĩa tốt hơn nhưng vẫn gặp khó khi phân biệt ý định và lời trích dẫn.

Hai lỗi cụ thể:

- Tin thúc giục giữ phần thưởng tài khoản trước khi hết hạn tối nay bị BoW đoán ham, còn Encoder + LogReg đoán đúng spam trong thí nghiệm 3.B. Sự kết hợp áp lực thời gian và yêu cầu xác nhận thông tin không được BoW biểu diễn đầy đủ.
- Case bổ sung **#101** là yêu cầu kiểm thử bộ lọc, trích một câu nhận thưởng và `Label: spam`, nhãn thật ham. Sonnet có prompt phòng thủ đoán spam. Quy tắc xem nhãn giả là dấu hiệu spam mạnh có thể làm model chặn nhầm nội dung kiểm thử hợp lệ.

Trên 55 case, cả hai prompt LLM đều đúng 54/55: phòng thủ cải thiện nhóm spam tự nhiên từ 91% lên 100% nhưng làm nhóm nói về spam giảm từ 100% xuống 80%. Nhóm injection đều 100%, nên chưa thấy cải thiện ở nhóm này. Không có case tất cả model cùng sai.

Năm case bổ sung được AI đề xuất, gồm trích ví dụ injection trong lớp học, trao đổi dữ liệu kiểm thử, giả người cùng nhóm nhờ chuyển tiền, giả hoàn tiền yêu cầu thông tin thẻ và giả thu phí giao lại hàng. Đây không phải các case hoàn toàn tự nghĩ. Để bổ sung bằng chứng, cần giữ nội dung và giải thích bẫy riêng cho từng case trong notebook. Chuẩn hóa ký tự như `fr€€` có thể hỗ trợ BoW nhưng có nguy cơ làm sai tên riêng hoặc số hợp lệ. Bộ case viết tay phục vụ tìm điểm yếu, không đại diện tần suất spam thật và không đủ để ước lượng chất lượng triển khai.

## 5. Thử thách cá nhân

SEED 7646 cho đề #3: thay hidden MLP bằng 4 và 512, so tham số, F1 và thời gian. Giữ 30 epoch, lr=0.001, batch size=64, cùng cách chia dữ liệu và đặt lại seed trước mỗi mạng.

| Hidden | Số tham số | F1 test | Train (s) |
| --- | ---: | ---: | ---: |
| 4 | 8009 | 0.964 | 3.249 |
| 512 | 1025025 | 0.959 | 19.672 |

Với d=2000, số tham số là d×h+h+h+1. Mạng lớn có khoảng 128 lần tham số, train lâu hơn khoảng 6 lần nhưng không tăng F1. Chênh 0.005 chưa đủ khẳng định mạng nhỏ luôn tốt hơn. Tôi ưu tiên mạng nhỏ khi chất lượng tương đương, dữ liệu ít hoặc tài nguyên hạn chế.

Phần thử thách có AI hỗ trợ gợi ý code; kết quả trong bảng được học viên cung cấp sau khi chạy. Đề bài quy định phần này làm không dùng AI.

## 6. Phương án cho sản phẩm thật

Với 10 triệu SMS/ngày (~116 tin/giây trung bình), mọi model phần 1–3 đạt p95 dưới 50 ms ở lần đo; kNN có throughput 115.349 tin/giây, còn thấp hơn tải trung bình nếu chỉ dùng cấu hình đo này. Cần thêm công suất cho giờ cao điểm và kiểm thử đồng thời. BoW + MLP nhanh và tốt trên test thường nhưng yếu trên stress test, nên chưa thể khẳng định đạt chất lượng thực tế. Trên biểu đồ SUB, Pareto gồm BoW + LogReg và BoW + MLP. SUB chỉ 40 tin nên sai một tin từ mức hoàn hảo đã làm 1−F1 thay đổi khoảng 2.44–2.56 điểm phần trăm.

Đề xuất router: model cục bộ xử lý tin chắc chắn, chuyển tin lưỡng lự hoặc có dấu hiệu ngoài phân phối sang LLM, kết hợp kiểm tra URL và đầu số. Không chỉ dựa vào xác suất vì model có thể tự tin khi sai. Nhánh LLM có p95 hơn 2 giây nên không đảm bảo SLA 50 ms; cần đo toàn hệ thống, xử lý bất đồng bộ hoặc thay bằng model cục bộ nếu mọi quyết định phải đáp ứng SLA.

Với 30 ngày, 300 triệu tin/tháng, chi phí tham khảo từ bảng làm tròn là khoảng 67.800 USD Sonnet zero-shot, 113.100 USD Opus và 192.600 USD Sonnet few-shot. Chưa gồm suy luận ẩn, vận hành hoặc giá nguồn 9Router thực tế. Nếu chỉ một phần tin đi LLM, chi phí còn phụ thuộc tỷ lệ chuyển tuyến và độ dài tin.

## 7. AI log phần 4 và điều đã học

Bảng dưới đây chỉ ghi các tương tác ở Phần 4, không phải AI log đầy đủ cho toàn bộ bài. Báo cáo này được tổng hợp với AI hỗ trợ.

| **Phần** | **Bạn hỏi AI gì (prompt)** | **AI trả lời/viết gì** | **Bạn đã sửa / kiểm chứng gì** |
| --- | --- | --- | --- |
| 4 — Cấu hình API | Dùng 9Router cho Colab và chọn model Claude như thế nào? | Hướng dẫn cấu hình endpoint, Secret và hai model Sonnet/Opus Thinking. | Chạy thử API và xác nhận cả hai model trả lời được; lưu key trong Colab Secrets. |
| 4.1 — Gọi API | Viết `call_openai` sát template. | Viết hàm gọi Chat Completions, lấy nội dung và đo độ trễ. | Chạy bộ kiểm tra giả lập; sửa vị trí import để mock hoạt động đúng. |
| 4.2–4.3 — Prompt và parse | Viết prompt zero/few-shot và hàm đọc nhãn. | Viết prompt phân loại, chèn ví dụ và chuẩn hóa nhãn spam/ham/not spam. | Chạy các test của notebook, kiểm tra trường hợp phản hồi không rõ trả `None`. |
| 4.4 — Phân loại theo lô | Hoàn thiện `llm_classify_batch`. | Viết vòng lặp gọi API, retry, đọc nhãn, lưu latency và ước tính chi phí. | Chạy test giả lập và phân loại 40 tin SUB bằng API thật. |
| 4.A–4.B — Thí nghiệm | Gợi ý dự đoán và giải thích kết quả zero-shot/few-shot. | Gợi ý nhận định về F1, độ trễ và chi phí. | Đối chiếu kết quả: cả hai model đạt F1 = 1.000; few-shot giữ nguyên F1 nhưng chi phí ước tính tăng khoảng 2.84 lần. |
| 4 — Chi phí | Thêm giá cho Sonnet và Opus. | Cung cấp bảng giá API tham khảo và code cập nhật `PRICE_PER_1M`. | Chạy lại thí nghiệm; ghi rõ chi phí chỉ là ước tính, chưa tính token suy luận ẩn và không phải hóa đơn 9Router. |

Tôi hiểu rằng precision cao vẫn có thể đi cùng recall thấp, và F1 không phải accuracy. Mạng lớn, model lớn hoặc few-shot không tự động cải thiện chất lượng; đánh giá trên case khó có thể thay đổi lựa chọn so với test thường. Prompt phòng thủ cũng có thể tạo lỗi chặn nhầm, nên cần kiểm tra cả lợi ích và tác dụng phụ.
