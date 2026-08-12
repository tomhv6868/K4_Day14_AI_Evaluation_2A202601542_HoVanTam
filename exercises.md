# Day 14 — Exercises

## AI Evaluation & Benchmarking · Lab Worksheet

**Thời gian làm bài:** 14:15–17:00

**Domain:** OrbitTech Store Customer Support

Điền trực tiếp câu trả lời vào file này. Golden dataset 20 QA được viết một lần
duy nhất trong `golden_dataset.json`, không chép lại toàn bộ vào Markdown.

---

Từ 14:15–14:30, cài môi trường và chạy baseline tests theo `guide_lab.md`.

---

## Part 1 — Warm-up (14:30–14:45)

### Exercise 1.1 — RAGAS Metric Thresholds

Theo bài giảng:

- 0.8–1.0: Good — monitor, maintain.
- 0.6–0.8: Needs work — analyze failures, iterate.
- Dưới 0.6: Significant issues — investigate.

Với từng metric, xác định khi nào score thấp có thể chấp nhận và khi nào là
critical.

| Tiêu chí | Trường hợp điểm thấp nhưng vẫn chấp nhận được | Trường hợp điểm thấp nghiêm trọng | Cần làm gì |
| --- | --- | --- | --- |
| Độ chính xác theo nguồn (Faithfulness) | Có một vài chi tiết chưa được nguồn hỗ trợ rõ ràng, nhưng ý chính vẫn đúng và bám sát thông tin được cung cấp. | Câu trả lời có thông tin sai, tự suy diễn hoặc bịa thêm, làm ảnh hưởng đến kết luận. | Kiểm tra lại cách sử dụng thông tin từ nguồn và hạn chế tạo ra nội dung không có căn cứ. |
| Mức độ liên quan của câu trả lời (Answer Relevance) | Câu trả lời đúng trọng tâm nhưng có thêm một vài thông tin không cần thiết. | Câu trả lời hiểu sai câu hỏi hoặc không giải quyết đúng vấn đề người dùng đang hỏi. | Cải thiện khả năng hiểu câu hỏi và yêu cầu mô hình trả lời tập trung hơn vào nội dung chính. |
| Khả năng lấy đủ thông tin cần thiết (Context Recall) | Bỏ sót một vài thông tin phụ, nhưng vẫn lấy được đủ dữ liệu để trả lời đúng. | Bỏ sót thông tin quan trọng, khiến câu trả lời thiếu hoặc sai. | Cải thiện cách tìm kiếm dữ liệu, chia nhỏ nội dung hoặc lấy thêm các đoạn thông tin liên quan. |
| Độ chính xác của thông tin được lấy (Context Precision) | Có lấy thêm một số thông tin không liên quan, nhưng phần thông tin cần thiết vẫn chiếm phần lớn. | Phần lớn thông tin được lấy về không liên quan hoặc gây nhiễu, làm ảnh hưởng đến câu trả lời. | Cải thiện cách xếp hạng, lọc kết quả và chỉ giữ lại những thông tin thực sự liên quan. |
| Mức độ đầy đủ (Completeness) | Câu trả lời đã đáp ứng phần lớn yêu cầu nhưng còn thiếu một vài chi tiết nhỏ. | Câu trả lời thiếu những nội dung quan trọng, khiến người dùng không thể sử dụng kết quả một cách đầy đủ. | Kiểm tra lại toàn bộ yêu cầu của người dùng và đảm bảo câu trả lời không bỏ sót các ý quan trọng. |

### Exercise 1.2 — Bias trong LLM-as-a-Judge

Ba bias thường gặp:

- Position bias: judge ưu tiên answer xuất hiện trước.
- Verbosity bias: judge ưu tiên answer dài hơn.
- Self-preference: judge ưu tiên output giống chính model đó.

**Câu 1: Thiết kế experiment phát hiện position bias với ít nhất hai conditions.**

Câu trả lời:
Chuẩn bị hai câu trả lời A và B cho cùng một câu hỏi, sau đó cho LLM judge đánh giá trong hai điều kiện:

Condition 1: A xuất hiện trước, B xuất hiện sau.
Condition 2: Đổi vị trí, B xuất hiện trước, A xuất hiện sau.

Nội dung A và B phải được giữ nguyên, chỉ thay đổi thứ tự. Nếu judge thường xuyên chọn câu trả lời đứng trước, dù nội dung không thay đổi, thì có dấu hiệu của position bias.

Có thể chạy experiment trên nhiều câu hỏi và tính tỷ lệ judge thay đổi lựa chọn sau khi đảo vị trí để đánh giá mức độ bias.

**Câu 2: Làm thế nào giảm verbosity bias bằng rubric design?**

Câu trả lời:
Rubric cần nói rõ rằng câu trả lời dài hơn không đồng nghĩa với tốt hơn. Judge nên chấm dựa trên các tiêu chí cụ thể như:

Có trả lời đúng câu hỏi không.
Thông tin có chính xác không.
Có đầy đủ các ý quan trọng không.
Có thông tin thừa hoặc lan man không.

Ngoài ra, có thể thêm tiêu chí về độ súc tích, ví dụ: câu trả lời nên cung cấp đủ thông tin cần thiết nhưng không thêm nội dung không liên quan. Như vậy judge sẽ ít ưu tiên một câu trả lời chỉ vì nó dài hơn.

**Câu 3: Tại sao cần calibrate LLM judge với human labels?**

Câu trả lời:
Vì đánh giá của LLM judge không phải lúc nào cũng giống đánh giá của con người. Model có thể bị ảnh hưởng bởi position bias, verbosity bias hoặc các bias khác.

Human labels đóng vai trò như một mốc tham chiếu để kiểm tra xem LLM judge đang đánh giá có hợp lý hay không. Bằng cách so sánh kết quả của judge với đánh giá của con người, ta có thể điều chỉnh prompt, rubric hoặc cách tính điểm.

Mục tiêu của calibration là làm cho kết quả của LLM judge gần với tiêu chuẩn đánh giá của con người hơn và ổn định hơn.

### Exercise 1.3 — Evaluation trong CI/CD

**Câu 1: Chọn threshold để block deployment.**

| Metric           | Threshold | Lý do                                                                                                               |
| ---------------- | --------: | ------------------------------------------------------------------------------------------------------------------- |
| Faithfulness     |      0.90 | Đây là tiêu chí quan trọng nhất vì câu trả lời không nên chứa thông tin sai hoặc không có trong nguồn.              |
| Answer Relevance |      0.80 | Câu trả lời cần đúng trọng tâm câu hỏi, nhưng có thể chấp nhận một ít thông tin phụ.                                |
| Completeness     |      0.80 | Câu trả lời cần bao phủ phần lớn các ý quan trọng, nhưng việc thiếu một vài chi tiết nhỏ vẫn có thể chấp nhận được. |


**Câu 2: Khi nào dùng offline evaluation, online evaluation và human review?**

> * **Offline evaluation:** dùng trước khi đưa hệ thống ra production, để kiểm tra model trên một tập dữ liệu có sẵn. Phù hợp khi muốn so sánh nhiều phiên bản model, prompt hoặc RAG pipeline một cách nhanh và ổn định.
> * **Online evaluation:** dùng khi hệ thống đã chạy thực tế, để theo dõi chất lượng trên dữ liệu và hành vi thật của người dùng. Phù hợp để phát hiện lỗi hoặc vấn đề mà offline evaluation chưa bao phủ.
> * **Human review:** dùng khi cần đánh giá những trường hợp khó, mang tính chủ quan hoặc có rủi ro cao, ví dụ câu trả lời nhạy cảm, phức tạp hoặc khi LLM judge không đủ đáng tin cậy.
>
> Thực tế nên kết hợp cả ba: **offline để kiểm tra trước khi release, online để giám sát sau khi release, và human review để xác minh các trường hợp quan trọng hoặc khó đánh giá tự động.**


## Part 2 — Core Coding (14:45–15:40)

Hoàn thiện các TODO bắt buộc trong `template.py`.

### Task 1 — Data Models

- `QAPair`: question, expected answer, gold context, metadata và retrieved contexts.
- `EvalResult`: answer-side scores, optional retrieval scores, pass/failure fields.
- `overall_score()`: trung bình Faithfulness, Relevance và Completeness.

### Task 2 — RAGASEvaluator

Answer-side:

- `evaluate_faithfulness(answer, context)`
- `evaluate_relevance(answer, question)`
- `evaluate_completeness(answer, expected)`

Retrieval-side:

- `evaluate_context_recall(contexts, expected)`
- `evaluate_context_precision(contexts, expected)`

Full pipeline:

- `run_full_eval(..., contexts=None)` luôn tính ba answer metrics.
- Nếu có `contexts`, tính và lưu thêm Context Recall và Context Precision.
- Retrieval scores không làm thay đổi `overall_score()` và pass rule gốc.

### Task 3 — LLMJudge

- `score_response(question, answer, rubric)`
- `detect_bias(scores_batch)`

### Task 4 — BenchmarkRunner

- `run(qa_pairs, agent_fn, evaluator)`
- `generate_report(results)`
- `run_regression(new_results, baseline_results)`
- `identify_failures(results, threshold)`

`BenchmarkRunner.run()` phải truyền `pair.retrieved_contexts` vào
`run_full_eval()`. Report phải có average của hai retrieval metrics.

### Task 5 — FailureAnalyzer

- `categorize_failures(failures)`
- `find_root_cause(failure)`
- `generate_improvement_suggestions(failures)`
- `generate_improvement_log(failures, suggestions)`

Kiểm tra:

```bash
pytest tests/ -v
```

`rerank_by_overlap()` là TODO bonus của Exercise 3.5. Test tương ứng được skip
nếu bạn chưa làm bonus.

---

## Part 3 — Golden Dataset & Real Benchmark (15:40–16:35)

### Exercise 3.1 — Build the Golden Dataset

Thiết kế và validate dataset theo Mục 5–6 trong `guide_lab.md`. Nội dung 20 QA
được điền trực tiếp trong `golden_dataset.json`; phần dưới chỉ ghi lại kết quả
và quyết định thiết kế, không chép lại toàn bộ QA.

**Kết quả dataset**

| Hạng mục | Kết quả |
|---|---|
| Tổng số records | ____ / 20 |
| Easy | ____ / 5 |
| Medium | ____ / 7 |
| Hard | ____ / 5 |
| Adversarial | ____ / 3 |
| Source documents được sử dụng | ____ / 10 |
| Validator status | PASS / FAIL |

**Ba case đại diện cho quyết định thiết kế**

| ID | Difficulty | Source document(s) | Vì sao case phù hợp với difficulty/attack type? |
|---|---|---|---|
| | | | |
| | | | |
| | | | |

**Điểm khó nhất khi xây dựng expected answer hoặc evidence là gì?**

> *Câu trả lời:*

**Xác nhận:**

- [ ] Mọi claim trong expected answer đều có evidence hỗ trợ.
- [ ] Không có questions trùng ý và không dùng kiến thức ngoài corpus.
- [ ] `python validate_golden_dataset.py` báo `PASS`.

### Exercise 3.2 — Benchmark Run

Chạy:

```bash
python domain_assistant.py
python evaluate_answers.py
```

Copy bảng terminal vào đây hoặc điền từ `artifacts/benchmark_results.json`.

| ID | Question (short) | Ctx Recall | Ctx Precision | Faithfulness | Relevance | Completeness | Overall | Passed? | Failure Type |
|---|---|---:|---:|---:|---:|---:|---:|---|---|
| E01 | | | | | | | | | |
| E02 | | | | | | | | | |
| E03 | | | | | | | | | |
| E04 | | | | | | | | | |
| E05 | | | | | | | | | |
| M01 | | | | | | | | | |
| M02 | | | | | | | | | |
| M03 | | | | | | | | | |
| M04 | | | | | | | | | |
| M05 | | | | | | | | | |
| M06 | | | | | | | | | |
| M07 | | | | | | | | | |
| H01 | | | | | | | | | |
| H02 | | | | | | | | | |
| H03 | | | | | | | | | |
| H04 | | | | | | | | | |
| H05 | | | | | | | | | |
| A01 | | | | | | | | | |
| A02 | | | | | | | | | |
| A03 | | | | | | | | | |

**Aggregate Report**

- Overall pass rate: ____%
- Avg Context Recall: ____
- Avg Context Precision: ____
- Avg Faithfulness: ____
- Avg Relevance: ____
- Avg Completeness: ____
- Failure type distribution: ____

**Ba cases có Overall Score thấp nhất**

1. ID: ____ | Score: ____ | Failure type: ____
2. ID: ____ | Score: ____ | Failure type: ____
3. ID: ____ | Score: ____ | Failure type: ____

**Nhận xét ngắn:** Metric nào yếu nhất? Kết quả gợi ý vấn đề nằm ở retrieval
hay generation?

> *Câu trả lời:*

### Exercise 3.3 — LLM-as-a-Judge Rubric Design

Thiết kế rubric domain-specific cho OrbitTech Customer Support. Mỗi mức phải
đủ cụ thể để hai người chấm độc lập có thể hiểu giống nhau.

Chọn 3–5 dimensions:

- [ ] Correctness
- [ ] Completeness
- [ ] Relevance
- [ ] Evidence/citation
- [ ] Actionability
- [ ] Safety/privacy
- [ ] Tone/clarity
- [ ] Dimension khác: __________

| Score | Tiêu chí domain-specific | Ví dụ response |
|---:|---|---|
| 5 | | |
| 4 | | |
| 3 | | |
| 2 | | |
| 1 | | |

**Ba edge cases khó chấm**

| Edge Case | Tại sao khó chấm? | Rubric xử lý thế nào? |
|---|---|---|
| | | |
| | | |
| | | |

**Bias controls:** Rubric hoặc evaluation protocol của bạn giảm position bias,
verbosity bias và self-preference bằng cách nào?

> *Câu trả lời:*

### Exercise 3.4 — Framework Comparison (Bonus +10)

Chỉ làm sau khi hoàn thành 3.1–3.3. Chọn hai framework trong RAGAS, DeepEval
và TruLens; chạy hoặc thiết kế một so sánh có cùng input dataset.

| Tiêu chí | Framework 1: ____ | Framework 2: ____ |
|---|---|---|
| Setup complexity | | |
| Metrics available | | |
| CI/CD integration | | |
| Kết quả trên cùng dataset | | |
| Insight rút ra | | |

- Scores có nhất quán không?
- Framework nào strict hơn và vì sao?
- Hai framework có tìm ra cùng failure cases không?

> *Phân tích:*

### Exercise 3.5 — Retrieval Reranking (Bonus +5)

Mục tiêu: kiểm tra việc đổi thứ tự chunks có tăng Context Precision mà không
thay đổi Context Recall hay không.

1. Chọn ít nhất 5 cases từ `artifacts/actual_answers.json`.
2. Tính Context Recall và Context Precision trước rerank.
3. Implement `rerank_by_overlap()` hoặc một reranker khác.
4. Rerank cùng tập chunks, không thêm hoặc xóa chunk.
5. Tính lại hai metrics và giải thích kết quả.

| ID | Recall before | Recall after | Precision before | Precision after | Delta Precision |
|---|---:|---:|---:|---:|---:|
| | | | | | |
| | | | | | |
| | | | | | |
| | | | | | |
| | | | | | |
| **Avg** | | | | | |

**Tại sao Recall dự kiến không đổi?**

> *Câu trả lời:*

**Khi nào reranking không đủ và cần sửa retriever/query/chunking?**

> *Câu trả lời:*

---

## Part 4 — Reflection (16:35–16:50)

Hoàn thành `reflection.md` bằng kết quả thật từ Exercise 3.2.

---

## Completion Checklist

Hoàn thành kiểm tra cuối trong khoảng 16:50–17:00.

- [ ] Tất cả required tests pass.
- [ ] `golden_dataset.json` validate thành công.
- [ ] Exercise 3.1 hoàn thành trong file JSON và bảng kết quả phía trên.
- [ ] Exercise 3.2 có năm metrics, aggregate report và ba cases thấp nhất.
- [ ] Exercise 3.3 có rubric 1–5 và bias controls.
- [ ] `reflection.md` có ba failure analyses và regression strategy.
- [ ] Đã copy `template.py` thành `solution/solution.py`.
- [ ] Exercise 3.4 và 3.5 chỉ làm nếu chọn bonus.
