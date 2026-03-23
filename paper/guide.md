## Paper Reference Guide

### Part 1: Overview of Fuzzing
1. ✅The Art, Science, and Engineering of Fuzzing: A Survey 
	* 模糊测试领域引用量最高的综述，建立了完整的分类体系（黑盒/灰盒/白盒）和标准化的 fuzzing loop，系统评估了 AFL、LibFuzzer 等主流工具。作为综述开篇的奠基引用几乎是业界标配。
2. ✅Large Language Models Based Fuzzing Techniques: A Survey 
	* 处于"传统 fuzzing"与"LLM 辅助 fuzzing"的交界处，既回顾了经典模糊测试发展脉络，又系统梳理了LLM 介入后的新范式，适合放在第一部分末尾起承上启下的作用。
3. ❌A_systematic_overview_of_fuzzing（选读）

### Part 2: Classic Tools of Fuzzing
1. ✅AFL++: Combining Incremental Steps of Fuzzing Research 
	* AFL 是覆盖率引导灰盒模糊测试的奠基工具，但原始 AFL 无正式学术论文。AFL++这篇不仅是其学术继承者，还系统总结了 AFL 诞生以来的所有改进（调度策略、变异算子、插桩方式），相当于一篇"AFL 发展史"的技术综述
2. ❌Directed Greybox Fuzzing (AFLGo) （选读）
	* AFLGo 将模糊测试从"无目标覆盖"推进到"定向漏洞挖掘"，引入模拟退火调度机制，是传统工具向智能化演进的重要里程碑。与后续 LLM 辅助工具形成清晰的技术演进对比，适合作为"传统工具智能化尝试"的代表。
3. ✅OSS-Fuzz
	* 传统模糊测试工具的集成平台，代表了模糊测试从"单机工具"到"云端服务"的工程化演进，是传统 fuzzing工具部分的重要补充。
	* 报告：Google's Continuous Fuzzing Service for Open Source Software  Serebryany, USENIX Security Symposium, 2017 
	- Google OSS-Fuzz 官网: https://google.github.io/oss-fuzz/                    
  	- GitHub: https://github.com/google/oss-fuzz
  	- Google Scholar: https://scholar.google.com/scholar?q=OSS-Fuzz+Google+continuous+fuzzing+service+open+source 
4. ✅Hopper- Interpretative Fuzzing for Libraries
	* Hopper 将库模糊测试转化为"解释器模糊测试"，无需手写 fuzz driver即可自动测试库API。它通过二进制插桩（E9Patch）学习API间的约束关系，生成语法感知的输入，在11个真实库中发现了25个未知漏洞。这个工具代表了传统 fuzzing 在"自动化driver生成"方向的探索
5. ✅Coverage-based Greybox Fuzzing as Markov Chain（AFLfast）
	* AFLFast 是 AFL 的重要改进版本，引入了"power schedule"（能量调度）机制，通过马尔可夫链模型优化种子选择策略，将能量分配与路径频率成反比，显著提升了低频路径的探索效率。这篇论文从理论层面解释了为什么 AFL会陷入"高频路径陷阱"，并提出了数学化的解决方案，是灰盒模糊测试理论研究的里程碑。

### Part 3: Current LLM-assisting Fuzzing Tools
1. ✅PromptFuzz: Prompt Fuzzing for Fuzz Driver Generation
	* 将覆盖率反馈引入 LLM 的 prompt 变异过程，自动生成针对 C 库 API 的 fuzz driver，在openssl、curl 等真实库上发现了多个 CVE。核心创新是把传统 fuzzing的"输入变异+覆盖率引导"范式迁移到"prompt 变异+代码生成"上，是该方向设计最严谨的早期工作之一。
2. ✅PromeFuzz: A Knowledge-Driven Approach to Fuzzing Harness Generation with Large Language Models
	* 相比 PromptFuzz 的覆盖率驱动方式，PromeFuzz引入了知识驱动（knowledge-driven）的方法来生成 fuzzing harness，在 LLM 理解库 API语义方面更进一步。两篇放在一起对比，能清晰展示 LLM 辅助 fuzzing,从"覆盖率引导"到"知识引导"的演进路径。
