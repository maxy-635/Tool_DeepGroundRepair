# DeepGroundRepair — ICSE 2027 Tool Demonstration 论文大纲

> 写作目标：将 DeepGroundRepair 呈现为一个**可使用、可检查、可复现的端到端工具**，而不是 TOSEM research paper 的缩略版。
>
> 全文主线：
>
> **Problem → Tool → Use → Evidence → Conclusion**

---

## Abstract

建议控制为一个紧凑段落，包含：

- LLM-generated DL programs 面临的两类核心问题：API-level issues 与 runtime tensor-shape failures。
- DeepGroundRepair 的工具定位：API-grounded generation、failure localization、shape-aware repair。
- 目标用户：使用 LLM 生成 DL code 的开发者，以及研究 LLM-generated DL programs 的软件工程研究者。
- 一个最重要的代表性验证结果。
- Tool URL 与 Demo Video URL。

不要在摘要中展开 RACR、AST tracing、intra/inter-path repair 或完整实验设置。

```text
Tool URL: <public repository or Zenodo URL>
Tool Video: <YouTube URL>
```

---

# 1. Introduction

建议篇幅：**约 0.4–0.5 页**

本节只回答三个问题：

1. 为什么需要这个工具？
2. 谁会使用这个工具？
3. DeepGroundRepair 提供什么？

建议内容：

- DL programs 同时受到 library-specific API constraints 和 tensor-shape dependencies 的约束。
- LLM-generated programs 可能包含 API hallucination / parameter misuse，也可能在运行时出现 tensor-shape mismatch。
- traceback 往往只暴露 failure site，而不能直接指出 upstream root cause。
- 因此，用户需要在 generation、execution、diagnosis、repair 和 re-execution 之间反复切换。
- DeepGroundRepair 将这些过程集成为一个统一工具流程。

随后明确说明：

- **Input**：natural-language DL programming requirement、target DL library、LLM configuration。
- **Output**：retrieved API information、draft program、failure diagnosis、runtime shape context、repaired program、validation result。
- **Tool-paper focus**：
  - 将已有的 generation–localization–repair 方法实现并呈现为一个可直接运行的工具流程；
  - 通过 representative demo 展示用户在 generation、localization 和 repair 中实际看到的关键结果；
  - 交代公开 artifact、运行条件和复现方式。

这里不要把 generation–localization–repair workflow 本身重新表述为 ICSE 的新算法贡献；ICSE 的重点是其 **tool realization、usage 和 reproducibility**。

Related Work 不单独成节，只在 Introduction 中用 3–4 句简要说明：

- retrieval / grounding for code generation；
- execution-feedback / self-repair；
- DL debugging / program analysis；
- DeepGroundRepair 的研究方法已经形成 generation–localization–repair workflow；本工具论文只需简要定位相关工作，并把重点放在该 workflow 的工具化实现、实际使用与复现。

---

# 2. DeepGroundRepair Tool

建议篇幅：**约 0.8 页**

本节是全文唯一的 tool design section，只需要简洁介绍 **Generation–Localization–Repair Backend**。

不要为了强调 tool contribution 再增加额外的设计模块。不要使用 2.1、2.2、2.3 等细粒度 subsection；用一张紧凑的 workflow figure + 三个 run-in paragraph headings 即可。

## 建议主图

```text
Natural-Language Requirement
            ↓
   API-Grounded Generation
            ↓
       Draft Program
            ↓
     Failure Localization
            ↓
   Root-Cause + Shape Context
            ↓
     Shape-Aware Repair
            ↓
      Repaired Program
            ↓
       Re-execution
```

图中可以同时标出用户可观察的关键输出：

- retrieved APIs；
- draft code；
- traceback；
- root-cause API；
- suspicious parameter；
- runtime tensor shapes；
- repaired code；
- final execution status。

### **API-Grounded Generation.**

只写高层工具行为：

- 根据 natural-language requirement 获得候选 APIs；
- 对候选 API 做可用性核验并检索 library-specific API knowledge；
- 将检索到的 API information 作为 grounding context；
- 输出 draft DL program。

只回答：

> 工具如何从 requirement 得到一个 API-grounded draft program？

不要展开：

- RACR 伪代码；
- sparse / dense retrieval 的公式；
- BM25、FAISS、embedding 参数；
- API database 的完整构建过程；
- 与 Hybrid-RAG 的完整比较。

### **Failure Localization.**

只写高层工具行为：

- 执行 draft program；
- 收集 failure message、traceback 和 runtime tensor information；
- 将 downstream failure site 与 upstream API configuration 联系起来；
- 输出 root-cause API、suspicious parameter 和 relevant shape context。

只回答：

> 程序失败后，工具如何告诉用户“真正需要检查哪里”？

不要展开：

- AST visitor 实现；
- sys.settrace 细节；
- masking rule 的完整设计；
- localization algorithm 的逐步推导。

### **Shape-Aware Repair.**

只写高层工具行为：

- 将 root-cause information 和 runtime shapes 提供给 repair component；
- 针对可疑参数或代码片段生成 repair candidate；
- 使用 shape consistency 和 re-execution 进行验证；
- 输出 repaired program 和 validation result。

只回答：

> 工具如何根据 diagnosis 生成并验证修复结果？

不要展开：

- intra-path / inter-path 的完整分类逻辑；
- shape-transformation formulas 的推导；
- prompt templates；
- 参数候选搜索细节。

---

# 3. Tool Usage and Representative Demonstration

建议篇幅：**约 0.85–0.95 页**

这是全文最重要的 tool-paper 部分。

本节不再拆成 Installation、Configuration、Input、Output 等多个小节。

先用一个短段落说明：

- 支持的 DL libraries；
- 支持的 LLM / model deployment 方式；
- Python / CUDA / GPU 基本要求；
- 输出文件或日志的位置；
- 运行入口。

本节只需如实说明当前工具的真实运行入口。初稿阶段不需要把统一 CLI 当作新的 tool contribution。

如果最终 artifact 仍然使用多个脚本，则直接给出真实调用链；如果后续增加统一入口，可以作为使用便利性的补充，而不需要把它提升为新的设计模块。

## Representative Demonstration

只选择 **一个固定的 tensor-shape mismatch case**。

建议优先使用 research paper 中类似 `nn.Linear` 输入维度不匹配的案例，因为它可以完整展示：

```text
Requirement
    ↓
API-grounded draft
    ↓
Runtime failure
    ↓
Root-cause localization
    ↓
Shape-aware repair
    ↓
Successful re-execution
```

需要展示的用户可观察结果：

| 阶段 | 用户看到的内容 |
|---|---|
| Generation | Retrieved APIs and draft program |
| Execution | Failure message and traceback |
| Localization | Root-cause API, suspicious parameter, runtime shape |
| Repair | Changed parameter / code fragment |
| Validation | Re-execution result |

可以用一个紧凑的 before/after code diff：

```text
Before:
self.fc1 = nn.Linear(in_features=25088, out_features=1024)

Observed input shape:
(1, 401408)

After:
self.fc1 = nn.Linear(in_features=401408, out_features=1024)

Re-execution:
SUCCESS
```

这一节的目标不是解释算法，而是让 reviewer 快速理解：

> 用户运行 DeepGroundRepair 后，到底发生了什么？

---

# 4. Validation and Reproducibility

建议篇幅：**约 0.5–0.6 页**

本节只保留能够证明工具有效且可复现的最小证据。

## Compact Validation Table

建议只保留 3–4 个最能支持工具能力的指标：

| Tool capability | Evidence |
|---|---|
| API-grounded generation | Reduction in API-level issues |
| Failure localization | Root-Cause API Localization Accuracy |
| Shape-aware repair | Shape-Mismatch Repair Success Rate |
| End-to-end workflow | Code Executability Rate improvement |

不要重新出现完整 RQ1–RQ5。

不要重新放：

- 六个模型 × 三个 DL libraries 的完整结果；
- ablation study；
- generalizability analysis；
- token-efficiency scatter plot。

这些内容保留在 research paper 中。

随后用一个简短段落交代 reproducibility：

- repository / Zenodo；
- environment；
- model / API database / index 获取方式；
- fixed demo input；
- 实际运行命令与 expected output；
- 单次 demo 的大致资源与运行时间。

这里不需要额外设计新的 packaging / reporting 模块；重点是说明现有工具如何被外部用户复现。

最后用 2–3 句交代限制：

- 当前主要针对 API-level issues 和 tensor-shape mismatches；
- successful execution 不等于完整 semantic correctness；
- dtype、device、training-time 和 deployment failures 不属于当前主要支持范围。

---

# 5. Conclusion

建议篇幅：**约 0.1–0.15 页**

只保留一个短段落。

需要总结：

- 用户从 natural-language requirement 开始；
- DeepGroundRepair 生成 API-grounded draft program；
- 工具通过执行和 runtime information 定位 upstream root cause；
- 工具利用 shape context 生成并验证 repair；
- 公开 artifact 和 demo 支持工具复现与使用。

建议结尾突出 tool contribution，而不是算法 novelty：

> DeepGroundRepair operationalizes API grounding, failure localization, and shape-aware repair as a reproducible end-to-end tool workflow for LLM-generated deep learning programs.

---

# 推荐的最终目录

```text
Abstract

1. Introduction

2. DeepGroundRepair Tool
   API-Grounded Generation.
   Failure Localization.
   Shape-Aware Repair.

3. Tool Usage and Representative Demonstration

4. Validation and Reproducibility

5. Conclusion
```

注意：

- Section 2 中三个能力使用 **run-in headings / bold paragraph headings**；
- 不建议编号为 2.1、2.2、2.3；
- Related Work 压入 Introduction；
- 不单独设置 Discussion。

---

# 推荐的篇幅控制

考虑到 4 页限制包含 references、figures 和 tables，建议按以下比例控制：

| Section | 建议篇幅 |
|---|---:|
| Abstract | ~0.20 page |
| 1. Introduction | ~0.45 page |
| 2. DeepGroundRepair Tool | ~0.80 page |
| 3. Tool Usage and Representative Demonstration | ~0.90 page |
| 4. Validation and Reproducibility | ~0.55 page |
| 5. Conclusion | ~0.10 page |
| Figures + Table + References | ~1.00 page |

正文目标约为 **2.8–3.0 页**。

---

# 图表建议

全文尽量控制为：

## Figure 1 — Tool Workflow + Representative Demo

一张图同时承担：

- overall workflow；
- key user-visible outputs；
- representative failure-to-repair path。

不要再额外放一张复杂 architecture figure。

## Table 1 — Compact Validation

只保留 3–4 行关键 evidence。

因此全文最好只有：

> **1 Figure + 1 Table**

---

# 明确留在 Research Paper / README 中的内容

以下内容不应在 tool paper 中展开：

- RACR 的完整算法；
- Root-Cause Localization 的详细算法；
- Shape-Aware Repair 的伪代码；
- AST / dynamic tracing 实现细节；
- sparse / dense retrieval 公式；
- prompt templates；
- shape-transformation formulas；
- 完整 benchmark / baseline / ablation；
- 六个模型 × 三个 libraries 的完整结果矩阵；
- 详细 token-cost analysis。

Tool paper 只保留：

> **Purpose → Input → Key Processing → Output → User-Visible Result → Reproducibility**

---

# 全文核心叙事

最终 reviewer 应当在 4 页内清楚看到：

```text
用户输入 DL programming requirement
        ↓
工具生成 API-grounded draft
        ↓
工具执行程序并发现 runtime failure
        ↓
工具定位 upstream root cause
        ↓
工具利用 runtime shapes 生成 repair
        ↓
工具重新执行并报告最终结果
```

这篇论文的核心不是重新提出 DeepGroundRepair 的算法设计，也不是额外堆叠新的 tool modules，而是展示：

> **DeepGroundRepair 已被实现为一个能够被实际运行、观察和复现的 generation–localization–repair tool.**

因此，ICSE 相对 TOSEM 的新增重点应保持集中：**tool realization + representative usage + reproducibility**。
