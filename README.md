# Awesome MGT Detection

## TOC

- [Key Conclusions, Findings, and Important Research Questions Based on Existing Research](#key-conclusions-findings-and-important-research-questions-based-on-existing-research)
- [Surveys](#surveys)
- [Detection Methods](#detection-methods)
  - [Metric-based](#metric-based)
  - [Neural-based](#neural-based)
- [Resources and Evaluation](#resources-and-evaluation)
  - [Pure MGT](#pure-mgt)
  - [Human-AI Mixed Text](#human-ai-mixed-text)
- [Attacks](#attacks)
- [Analysis](#analysis)
- [Systems and Tools](#systems-and-tools)
- [Other Related Works](#other-related-works)
- [Workshops and Shared Tasks](#workshops-and-shared-tasks)

This repository is developed from and builds upon the [ICTMCG/Awesome-Machine-Generated-Text](https://github.com/ICTMCG/Awesome-Machine-Generated-Text) repository.

While retaining some representative papers from the earlier collection, this repository focuses primarily on adding recent papers accepted to top-tier conferences.

Given the strong and efficient document-reading capabilities of modern LLMs, I have chosen not to follow the practice of similar repositories by providing a paper summary for each work or documenting further details such as motivation, ideas, experiments and results, analysis and findings, limitations, and future prospects. In my view, doing so would provide relatively limited additional value.

Instead, I share my own synthesis of the important findings from existing research, as well as the major open problems that remain unresolved. Of course, this synthesis cannot be fully comprehensive; I have simply tried to cover the most important aspects as much as possible. I hope it can help readers gain a rough understanding of the development and direction of this research field.

One additional note: this repository does not cover the special subfield of watermarking.

## Key Conclusions, Findings, and Important Research Questions Based on Existing Research

**The following summary is organized from simple to complex. The preceding text covers the most basic fundamentals of the field and can be skipped.**

* **Finding: Humans have difficulty distinguishing Machine-Generated Text (MGT) from Human-Written Text (HWT).**
  * *"Automatic Detection of Generated Text is Easiest when Humans are Fooled"* (ACL 2020) shows that detectors can perform substantially better than humans.
  * *"All That’s ‘Human’ Is Not Gold"* (ACL 2021) shows that crowd workers are almost unable to distinguish MGT from HWT. Although training can improve performance, the improvement remains limited.
  * ROFT (AAAI 2023) shows that human performance is above random guessing, but there is substantial variation across individuals.
  * TuringBench (EMNLP 2021) shows that human performance is close to random guessing, with reported accuracies such as 0.513 and 0.535.
  * MAGE (ACL 2024) shows that both human detection and asking ChatGPT are only slightly better than random guessing, with an accuracy of approximately 54.5%.
  
* **A widely accepted view is that models can distinguish MGT from HWT because the generation process leaves detectable artifacts (which some papers refer to as "patterns").**

  * Representative works: GROVER (NeurIPS 2019), *"Reverse Engineering Configurations of Neural Text Generation Models"* (ACL 2020).
  * *Position: On the Possibilities of AI-Generated Text Detection* (ICML 2024) argues that AI-generated text is, in principle, detectable. The authors argue that the only condition under which AI-generated text detection would be impossible is when the distribution of AI-generated text is exactly identical to that of human-written text. Therefore, AI-generated text detection remains theoretically possible in practice.

* **Finding: In most cases, detectors substantially outperform humans.**

  * Representative works: *"Automatic Detection of Generated Text is Easiest when Humans are Fooled"* (ACL 2020), *"Limitations of Human Identification of Automatically Generated Text"* (COLING 2024).

* **Author Attribution is an important problem.**

  * Identifying which generator produced a piece of MGT may be more difficult than simply distinguishing HWT from MGT.
  * This problem also involves the black-box vs. white-box setting, i.e., whether the set of candidate generators is known. Direct multi-class classification is limited when the number of candidate classes is small.
  * Representative works: *"Reverse Engineering Configurations of Neural Text Generation Models"* (ACL 2020), AA (EMNLP 2020).

* **Neural-based detectors are highly powerful, but usually have poor generalization; metric-based detectors generally have better generalization.**

  * This is a widely recognized problem in the field. Many works, including DPIC (NeurIPS 2024), explicitly discuss this issue.

* **Black-box limitation of metric-based detectors: they cannot directly access white-box features such as the original generation probabilities of commercial generators. Instead, they have to use surrogate generators to compute metrics such as PPL, which may result in worse-than-expected detection performance.**

  * One direction is to design a new method that does not require logits; another is to directly address this limitation, as in DALD (NeurIPS 2024).
  * Representative works: DNA-GPT (ICLR 2024), DPIC (NeurIPS 2024), Raidar, DALD (NeurIPS 2024), TOCSIN (EMNLP 2024), GECSCORE (COLING 2025), GLIMPSE (ICLR 2025).

* **Finding: SLMs are more suitable than LLMs as base models for metric-based detectors.**

  * *Smaller Language Models are Better Zero-shot Machine-Generated Text Detectors* (EACL 2024).

* **Finding: For neural-based detectors, transfer to an unseen domain (or, more precisely, an unseen distribution) can be achieved effectively by fine-tuning with only a small amount of text from that domain.**

  * Representative works: *"Cross-Domain Detection of GPT-2-Generated Technical Text"* (NAACL 2022), MAGE (ACL 2024).

* **Finding: For metric-based detectors, good performance on an unseen distribution can also be achieved by updating the threshold with very few samples.**

  * Representative work: MGTBench (arXiv 2023).

* **Finding: Text generated by medium-sized LLMs (around 7B parameters) is more beneficial for detector generalization than text generated by large-scale/commercial LLMs.**
  * Representative work: *On the Zero-Shot Generalization of Machine-Generated Text Detectors* (EMNLP Findings 2023).
  
* **Finding: AI-generated text exhibits a type of "previous-token memorization" anomaly pattern.**

  * Representative work: BiScope (NeurIPS 2024).

* **Finding: Human writing exhibits greater individual variation, while LLMs are becoming increasingly human-like and increasingly similar to one another.**
  * Representative work: *Linguistic and Embedding-Based Profiling of Texts Generated by Humans and Large Language Models* (EMNLP 2025).
  
* **Finding: DMHM (ACL 2026) finds that MGT has a clear hierarchical distribution structure: easily detectable MGT forms tight, high-density clusters, whereas difficult-to-detect MGT overlaps with human-written text.**

* **Finding: MRF Calibration (ICLR 2026) finds that token logits are unstable and noisy at the beginning of a sentence. As the sequence becomes longer, the differences between the logits of tokens and their neighbors become smaller and smoother.**

* **Finding: D&R (ICLR 2026) finds that if the word order of a passage is shuffled and a black-box LLM is asked to restore the original order only once, machine-generated text can be reconstructed more accurately than human-written text.**

* **Finding: *Utterance-level Detection Framework for LLM-Involved Content Detection in Conversational Setting* (EACL 2026) finds that, in utterance-level detection under conversational settings, the linguistic features of the second speaker more directly determine whether that speaker is human or an LLM.**

* **Finding: *Identifying Bias in Machine-generated Text Detection* (ACL 2026) finds that detectors are more likely to misclassify human-written text as AI-generated depending on the author's background. For example, essays written by English Language Learner students are more likely to be classified as AI-generated.**

* **Finding: NTS (ACL 2026) finds that MGT has higher temperature sensitivity. Specifically, when LogPPL is calculated under low-temperature and high-temperature settings, the difference is generally larger for MGT than for HWT.**

* **IRM (NeurIPS 2025) argues that the transition from base models to instruction-tuned models is essentially a form of implicit reward.**

  * I think this may be one of the reasons why Binoculars works so well.

* **M-RangeDetector (ACL 2025) points out that although LLMs generate text autoregressively, human writing is not necessarily strictly left-to-right.**

* **Text length is an important factor.**

  * TuringBench (EMNLP 2021) finds that word count is associated with the performance of RoBERTa-based models, but has no obvious relationship with the performance of other models. (I believe this issue has been largely addressed by ModernBERT.)
  * MGTBench (arXiv 2023) finds that excessively short texts reduce detection accuracy, with around 200 words being close to optimal.
  * MPU (ICLR 2024) formulates MGT detection as a Positive-Unlabeled (PU) learning problem and argues that MGT can be viewed as partially unlabeled, with the degree determined based on text length.
  * Easy2Hard (NeurIPS 2025) and *Position: On the Possibilities of AI-Generated Text Detection* (ICML 2024) also find that longer texts are easier to detect.

* **Sentence-level detection.**
  * Representative works: SeqXGPT (EMNLP 2023), AdaLoc (ACL 2024), MPU (ICLR 2024), SimLLM (EMNLP 2024).
  
* **Utterance-level detection in conversational settings.**
  * Representative work: *Utterance-level Detection Framework for LLM-Involved Content Detection in Conversational Setting* (EACL 2026).
  
* **Detection of Long-Term Dialogue Agents.**
  * Representative works: Online LLM Detection (ICML 2025), X-TURING (ACL 2025), ChatbotID (NeurIPS 2025).
  
* **Detection of high-entropy MGT (the "Capybara Problem") and low-entropy HWT (Binoculars, ICML 2024).**
  * High-entropy machine-generated text includes generations on obscure or unusual topics, such as a story about "a capybara that is an astrophysicist."
  * Low-entropy human-written text includes writing by non-native speakers.
  
* **Linguistic feature differences between MGT and HWT.**

  * These include semantic similarity, text readability, lexical diversity, sentiment analysis, LIWC, RST, and so on.
  * Many benchmarks and analysis-oriented papers investigate these differences. Representative works: AA-NTG (EMNLP 2020), HC3 (arXiv 2023), MAGE (ACL 2024), MAGA (arXiv 2026).
  * Some detection methods explicitly exploit findings about linguistic features. Representative works: emoPLMsynth (EMNLP 2023), GPT-who (NAACL 2024), *Threads of Subtlety* (ACL 2024), DART (NAACL 2025).
  * Some studies show that linguistic errors in MGT differ from those in HWT. Representative works: SCARECROW (ACL 2022), LAMP (CHI 2025).

* **Interpretability of detectors.**

  * Representative works: TDA4ATD (EMNLP 2021), DNA-GPT (ICLR 2024).

* **Computational efficiency of perturbation-based metric detectors (building on DetectGPT, ICML 2023).**

  * Representative works: DetectLLM (EMNLP Findings 2023), Fast-DetectGPT (ICLR 2024).

* **Closed-form solutions for perturbation-based detection.**

  * Representative works: Fast-DetectGPT (ICLR 2024), DNA-DetectLLM (NeurIPS 2025).

* **Calibration, normalization, and bias removal for metrics used by metric-based detectors.**

  * Representative works: DetectGPT (ICML 2023), Fast-DetectGPT (ICLR 2024), Binoculars (ICML 2024), Lastde (ICLR 2025), MRF Calibration (ICLR 2026).

* **Leveraging style detection.**

  * LUAR (ICLR 2024) finds that MGT and HWT largely overlap semantically but differ substantially in style.
  * Representative works: LUAR (ICLR 2024), DeTeCtive (NeurIPS 2024), dpo-ling (ACL 2025), MoSEs (EMNLP 2025).

* **Leveraging rewriting/continuation for binary classification.**

  * Machine-rewritten machine-generated text tends to remain relatively close to the original text, whereas machine-rewritten human text tends to differ substantially from the original.
  * Representative works: *Beat LLMs at Their Own Game* (EMNLP 2023), DNA-GPT (ICLR 2024), Raidar (ICLR 2024), SimLLM (EMNLP 2024), L2R (ACL 2025), DNA-DetectLLM (NeurIPS 2025), L2D (ICLR 2026).

* **Treating logits as a time series for detection.**

  * This can be done using methods such as sliding windows and wavelet transforms.
  * Representative works: SeqXGPT (EMNLP 2023), FourierGPT (EMNLP 2024), Lastde (ICLR 2025), WAVEDETECT (ACL 2026).

* **Increasing the distance between MGT and HWT, while reducing the distance within MGT and within HWT, helps detection.**

  * More generally, any method based on comparing distances can be formulated in terms of pulling certain distributions closer together and pushing others farther apart. For example, one can push human-written text farther away from machine-rewritten human text (L2D, ICLR 2026).
  * Representative works: MMD-MP (ICLR 2024), R-Detect (ICLR 2025), L2R (ACL 2025), L2D (ICLR 2026).

* **Leveraging RLHF and reward models for detection.**

  * ReMoDetect shows that the reward model itself can serve as an efficient LLM text detector.
  * Representative works: ReMoDetect (NeurIPS 2024), IRM (NeurIPS 2025), HAPDA (ACL 2026).

* **Leveraging multi-level contrastive learning for detection.**

  * Representative works: DeTeCtive (NeurIPS 2024), DETree (NeurIPS 2025).

* **Leveraging retrieval for detection.**

  * Representative works: DeTeCtive (NeurIPS 2024), DETree (NeurIPS 2025), HALO (EMNLP 2025).

* **Leveraging search engines for detection.**

  * Representative work: SearchLLM (EACL 2026).

* **Removing shortcut neurons associated with topics/domains and other spurious correlations to improve detection.**

  * Representative work: *How to Generalize the Detection of AI-Generated Text: Confounding Neurons* (EMNLP 2025).

* **Generators-Detector Adversarial Reinforcement Learning.**
  * Representative works: RADAR (NeurIPS 2023), llm-detector-evasion (ICLR 2024), HUMPA (ICLR 2025), MAGA (arXiv 2026).
  
* **Expanding the dimensions of data distributions and improving cross-distribution generalization.**

  * Representative works: M4 (EACL 2024), BUST (NAACL 2024), MAGE (ACL 2024), *Who Writes What* (ACL 2025), EvoBench (ACL 2025), MAGA (arXiv 2026).

* **Detection problems in specific domains with distinctive characteristics, as well as more realistic, application-oriented benchmarks.**

  * Representative works: DetectRL (NeurIPS 2024), PlagBench (NAACL 2025), MixRevDetect (NAACL 2025), MultiSocial (ACL 2025), AI-Peer-Review-Detection-Benchmark (ICLR 2026), AICD Bench (EACL 2026), DetectRL-X (ACL 2026).

* **Adversarial attacks and robustness.**

  * Representative benchmarks: MGTBench (arXiv 2023), RAID (ACL 2024).
  * Representative detector: SCRN (ACL 2024).
  * *HowYouPromptMatters* (EMNLP 2024) finds that detectors are sensitive to prompts.
  * Perturbing only a small number of keywords can be highly effective: HMGC (COLING 2024), GREATER (ACL 2025), RAFT (EMNLP 2024).
  * *Authorship Obfuscation in Multilingual Machine-Generated Text Detection* (EMNLP 2024) points out a trade-off between attack success rate and text quality: some attacks are highly effective but substantially damage the text, for example by inserting unusual characters. Such modifications can be easily noticed by humans, reduce readability, and make the text less like a normal article.

* **Generation decoding parameters and extreme random generation.**

  * The higher the randomness of MGT generation, the easier it is for humans to detect (ROFT, AAAI 2023).
  * Binoculars (ICML 2024) finds that its Binoculars detector completely misclassifies fully random tokens as HWT.
  * TempParaphraser (EMNLP 2025) finds that the effect of high-temperature generation can be simulated through "multiple rounds of normal-temperature paraphrasing + detector-based selection."
  * Other representative works: RAID (ACL 2024), *How Sampling Affects the Detectability of Machine-written Texts: A Comprehensive Study* (EMNLP 2025).

* **The problem of determining detector thresholds.**

  * MCP (ACL 2025) formulates threshold selection as a conformal prediction (CP) problem and sets multi-scale thresholds according to different text lengths.
  * Other representative work: MoSEs (EMNLP 2025).

* **Classification of human-machine mixed text and treating it as a separate class.**
  * Representative works: MixSet (NAACL 2024), *Ship of Theseus* (ACL 2024), Beemo (NAACL 2025), APT-Eval (ACL 2025), RACE (ACL 2026).
  
* **Detection of human-machine mixed text at the sentence and word levels.**

  * Representative works: HACo-Det (ACL 2025), SenDetEX (EMNLP 2025).




## Surveys

- **Automatic Detection of Machine Generated Text: A Critical Survey.** [[paper]](https://aclanthology.org/2020.coling-main.208) ![](https://img.shields.io/badge/COLING%202020-orange)
- **A Survey of AI-generated Text Forensic Systems: Detection, Attribution, and Characterization.** [[paper]](https://arxiv.org/abs/2403.01152) ![](https://img.shields.io/badge/arXiv%202024-orange)
- **A Survey on Detection of LLMs-Generated Content.** [[paper]](https://aclanthology.org/2024.findings-emnlp.572/) ![](https://img.shields.io/badge/EMNLP%20Findings%202024-orange)
- **Are AI Detectors Good Enough? A Survey on Quality of Datasets With Machine-Generated Texts.** [[paper]](https://openreview.net/forum?id=WDhwO4Hhq2) ![](https://img.shields.io/badge/AAAI%202025%20Workshop%20PDLM-orange)
- **A Survey on LLM-Generated Text Detection: Necessity, Methods, and Future Directions.** [[paper]](https://aclanthology.org/2025.cl-1.8/) ![](https://img.shields.io/badge/CL%202025-orange)



## Detection Methods

###  Metric-based

Note: Detectors built upon multiple metrics (rather than a single metric) often train a classification head instead of relying on a single threshold. Examples include Threads of Subtlety (ACL 2024) and DART (NAACL 2025), both of which I categorize as metric-based detectors. Detectors that train a scorer, such as DALD (NIPS 2024), are also categorized as metric-based detectors. It is worth noting, however, that the categorization of these detectors may be somewhat debatable from an objective standpoint.

- **Artificial Text Detection via Examining the Topology of Attention Maps.** [[paper]](https://aclanthology.org/2021.emnlp-main.50v2) ![](https://img.shields.io/badge/EMNLP%202021-orange)
- **DetectGPT: Zero-Shot Machine-Generated Text Detection using Probability Curvature.** [[paper]](https://proceedings.mlr.press/v202/mitchell23a.html) ![](https://img.shields.io/badge/ICML%202023-orange) ![](https://img.shields.io/badge/Oral-purple)
- **LLMDet: A Third Party Large Language Models Generated Text Detection Tool.** [[paper]](https://aclanthology.org/2023.findings-emnlp.139) ![](https://img.shields.io/badge/EMNLP%20Findings%202023-orange)
- **DetectLLM: Leveraging Log Rank Information for Zero-Shot Detection of Machine-Generated Text.** [[paper]](https://aclanthology.org/2023.findings-emnlp.827) ![](https://img.shields.io/badge/EMNLP%20Findings%202023-orange)
- **Beat LLMs at Their Own Game: Zero-Shot LLM-Generated Text Detection via Querying ChatGPT.** [[paper]](https://aclanthology.org/2023.emnlp-main.463) ![](https://img.shields.io/badge/EMNLP%202023-orange) ![](https://img.shields.io/badge/Short-gray)
- **Detecting AI-Generated Code Assignments Using Perplexity of Large Language Models.** [[paper]](https://ojs.aaai.org/index.php/AAAI/article/view/30361) ![](https://img.shields.io/badge/AAAI%202024-orange)
  - DetectGPT for AI Code
- **Efficient Detection of LLM-generated Texts with a Bayesian Surrogate Model.** [[paper]](https://aclanthology.org/2024.findings-acl.366) ![](https://img.shields.io/badge/ACL%20Findings%202024-orange)
- **DNA-GPT: Divergent N-Gram Analysis for Training-Free Detection of GPT-Generated Text.** [[paper]](https://proceedings.iclr.cc/paper_files/paper/2024/hash/d4ce6738e84876aa79f13c8bc8b7c5eb-Abstract-Conference.html) ![](https://img.shields.io/badge/ICLR%202024-orange)
- **Raidar: geneRative AI Detection viA Rewriting.** [[paper]](https://proceedings.iclr.cc/paper_files/paper/2024/hash/1888b9df34b0d9eea6e009a1fdd55c4f-Abstract-Conference.html) ![](https://img.shields.io/badge/ICLR%202024-orange)
- **Fast-DetectGPT: Efficient Zero-Shot Detection of Machine-Generated Text via Conditional Probability Curvature.** [[paper]](https://proceedings.iclr.cc/paper_files/paper/2024/hash/6b8c6f846c3575e1d1ad496abea28826-Abstract-Conference.html) ![](https://img.shields.io/badge/ICLR%202024-orange)
- **Ghostbuster: Detecting Text Ghostwritten by Large Language Models.** [[paper]](https://aclanthology.org/2024.naacl-long.95) ![](https://img.shields.io/badge/NAACL%202024-orange)
- **GPT-who: An Information Density-based Machine-Generated Text Detector.** [[paper]](https://aclanthology.org/2024.findings-naacl.8) ![](https://img.shields.io/badge/NAACL%202024-orange)
- **Spotting LLMs With Binoculars: Zero-Shot Detection of Machine-Generated Text.** [[paper]](https://proceedings.mlr.press/v235/hans24a.html) ![](https://img.shields.io/badge/ICML%202024-orange)
- **Threads of Subtlety: Detecting Machine-Generated Texts Through Discourse Motifs.** [[paper]](https://aclanthology.org/2024.acl-long.298) ![](https://img.shields.io/badge/ACL%202024-orange)
- **BiScope: AI-generated Text Detection by Checking Memorization of Preceding Tokens.** [[paper]](https://proceedings.neurips.cc/paper_files/paper/2024/hash/bc808cf2d2444b0abcceca366b771389-Abstract-Conference.html) ![](https://img.shields.io/badge/NIPS%202024-orange)
- **DALD: Improving Logits-based Detector without Logits from Black-box LLMs.** [[paper]](https://proceedings.neurips.cc/paper_files/paper/2024/hash/62e5f22b5dae99ec700be622df4fbe0d-Abstract-Conference.html) ![](https://img.shields.io/badge/NIPS%202024-orange)
  - A method that uses a surrogate model to imitate the probability distribution of a black-box LLM
- **DPIC: Decoupling Prompt and Intrinsic Characteristics for LLM Generated Text Detection.** [[paper]](https://proceedings.neurips.cc/paper_files/paper/2024/hash/1d35af80e775e342f4cd3792e4405837-Abstract-Conference.html) ![](https://img.shields.io/badge/NIPS%202024-orange)
- **Detecting Subtle Differences between Human and Model Languages Using Spectrum of Relative Likelihood.** [[paper]](https://aclanthology.org/2024.emnlp-main.564) ![](https://img.shields.io/badge/EMNLP%20Findings%202024-orange)
- **SimLLM: Detecting Sentences Generated by Large Language Models Using Similarity between the Generation and its Re-generation.** [[paper]](https://aclanthology.org/2024.emnlp-main.1246) ![](https://img.shields.io/badge/EMNLP%202024-orange)
- **Zero-Shot Detection of LLM-Generated Text using Token Cohesiveness.** [[paper]](https://aclanthology.org/2024.emnlp-main.971) ![](https://img.shields.io/badge/EMNLP%202024-orange)
- **Imitate Before Detect: Aligning Machine Stylistic Preference for Machine-Revised Text Detection.** [[paper]](https://ojs.aaai.org/index.php/AAAI/article/view/34525) ![](https://img.shields.io/badge/AAAI%202025-orange)
  - For Machine-Rewritten Text Detection; includes a dataset named Machine Revision Dataset
- **MAGRET: Machine-generated Text Detection with Rewritten Texts.** [[paper]](https://aclanthology.org/2025.coling-main.557) ![](https://img.shields.io/badge/COLING%202025-orange)
  - For Machine-Rewritten Text Detection; includes a MAGRET-Bench
- **Who Wrote This? The Key to Zero-Shot LLM-Generated Text Detection Is GECScore.** [[paper]](https://aclanthology.org/2025.coling-main.684/) ![](https://img.shields.io/badge/COLING%202025-orange)
- **Training-free LLM-generated Text Detection by Mining Token Probability Sequences.** [[paper]](https://proceedings.iclr.cc/paper_files/paper/2025/hash/2fcd27d2806a6d4f616cb4e6084d74f3-Abstract-Conference.html) ![](https://img.shields.io/badge/ICLR%202025-orange)
- **Glimpse: Enabling White-Box Methods to Use Proprietary Models for Zero-Shot LLM-Generated Text Detection.** [[paper]](https://proceedings.iclr.cc/paper_files/paper/2025/hash/806e8e4ea77000a1486eeddeb1b90a6e-Abstract-Conference.html) ![](https://img.shields.io/badge/ICLR%202025-orange)
  - It's actually not a detector; I just temporarily place it under this heading in this readme.
  - The Business LLM API can only return the probabilities of a limited top k tokens in the vocabulary. Glimpse proposes estimating the probabilities of the remaining tokens, allowing the business LLM to be better applied to metric-based detectors.
- **PaLD: Detection of Text Partially Written by Large Language Models.** [[paper]](https://proceedings.iclr.cc/paper_files/paper/2025/hash/d799b1d6a5e43546e67e7afdeffc067d-Abstract-Conference.html) ![](https://img.shields.io/badge/ICLR%202025-orange)
  - It's actually not a detector; I just temporarily place it under this heading in this readme.
  - PaLD utilizes the binary classification detector to detect the proportion of machine-written text in mixed text and to identify which sentences are machine-written.
- **MixRevDetect: Towards Detecting AI-Generated Content in Hybrid Peer Reviews.** [[paper]](https://aclanthology.org/2025.naacl-short/)![](https://img.shields.io/badge/NAACL%202025-orange) ![](https://img.shields.io/badge/Short-gray) 
  - Detect whether each review comment is AI or human using continuation like DNA-GPT; dataset is also a contribution.
- **DART: An AIGT Detector using AMR of Rephrased Text.** [[paper]](https://aclanthology.org/2025.naacl-short.59/) ![](https://img.shields.io/badge/NAACL%202025-orange) ![](https://img.shields.io/badge/Short-gray)
- **Online Detection of LLM-Generated Texts via Sequential Hypothesis Testing by Betting.** [[paper]](https://proceedings.mlr.press/v267/chen25bn.html) ![](https://img.shields.io/badge/ICML%202025-orange)
  - It's actually not a detector; I just temporarily place it under this heading in this readme.
  - It proposes a statistical inference framework that determines whether a text source is machine based on a sequence of texts, rather than detecting a single text.
- **MOSAIC: Multiple Observers Spotting AI Content.** [[paper]](https://aclanthology.org/2025.findings-acl.1244) ![](https://img.shields.io/badge/ACL%20Findings%202025-orange)
  - It's actually not a detector; I just temporarily place it under this heading in this readme.
  - It is a method for weighting the logits of scoring LLMs, utilizing the Blahut–Arimoto algorithm.
- **Mitigating Paraphrase Attacks on Machine-Text Detection via Paraphrase Inversion.** [[paper]](https://aclanthology.org/2025.findings-acl.227/) ![](https://img.shields.io/badge/ACL%20Findings%202025-orange)
  - It's actually not a detector; I just temporarily place it under this heading in this readme.
  - It trains an inversion model to revert paraphrased text to its original form before feeding it into various binary classifiers, thereby improving detection performance.
- **Reliably Bounding False Positives: A Zero-Shot Machine-Generated Text Detection Framework via Multiscaled Conformal Prediction.** [[paper]](https://aclanthology.org/2025.acl-long.601/) ![](https://img.shields.io/badge/ACL%202025-orange)
  - A threshold determination method for multiscale text lengths. 
  - It's actually not a detector; I just temporarily place it under this heading in this readme.
  - Includes a benchmark named RealDet.
- **Learning to Rewrite: Generalized LLM-Generated Text Detection.** [[paper]](https://aclanthology.org/2025.acl-long.322) ![](https://img.shields.io/badge/ACL%202025-orange)
- **AdaDetectGPT: Adaptive Detection of LLM-Generated Text with Statistical Guarantees.** [[paper]](https://proceedings.neurips.cc/paper_files/paper/2025/hash/808bc5e3d076c1125a87f81b42d5e52d-Abstract-Conference.html) ![](https://img.shields.io/badge/NIPS%202025-orange)
  - A method of remapping raw log-probabilities before applying them to metric-based detectors.
- **Zero-Shot Detection of LLM-Generated Text via Implicit Reward Model.** [[paper]](https://proceedings.neurips.cc/paper_files/paper/2025/hash/f50258b34f1c5080e43281e05050034e-Abstract-Conference.html) ![](https://img.shields.io/badge/NIPS%202025-orange)
- **DNA-DetectLLM: Unveiling AI-Generated Text via a DNA-Inspired Mutation-Repair Paradigm.** [[paper]](https://proceedings.neurips.cc/paper_files/paper/2025/hash/0287c393907259ae6a269fca5e3509cd-Abstract-Conference.html) ![](https://img.shields.io/badge/NIPS%202025-orange) ![](https://img.shields.io/badge/Spotlight-blue)
- **MoSEs: Uncertainty-Aware AI-Generated Text Detection via Mixture of Stylistics Experts with Conditional Thresholds.** [[paper]](https://aclanthology.org/2025.emnlp-main.294) ![](https://img.shields.io/badge/EMNLP%202025-orange)
  - A method for determining thresholds for texts of different styles.
  - It's actually not a detector; I just temporarily place it under this heading in this readme.
- **DivScore: Zero-Shot Detection of LLM-Generated Text in Specialized Domains.** [[paper]](https://aclanthology.org/2025.emnlp-main.971) ![](https://img.shields.io/badge/EMNLP%202025-orange)
- **Profiler: Black-box AI-generated Text Origin Detection via Context-aware Inference Pattern Analysis.** [[paper]](https://aclanthology.org/2025.emnlp-main.1265/) ![](https://img.shields.io/badge/EMNLP%202025-orange)
- **Enhancing LLM Text Detection with Retrieved Contexts and Logits Distribution Consistency.** [[paper]](https://aclanthology.org/2025.emnlp-main.503) ![](https://img.shields.io/badge/EMNLP%202025-orange)
- **Unsupervised Detection of LLM-Generated Text in Korean Using Syntactic and Semantic Cues.** [[paper]](https://aclanthology.org/2026.findings-eacl.77/) ![](https://img.shields.io/badge/EACL%20Findings%202026-orange)
- **SearchLLM: Detecting LLM Paraphrased Text by Measuring the Similarity with Regeneration of the Candidate Source via Search Engine.** [[paper]](https://aclanthology.org/2026.eacl-long.79/) ![](https://img.shields.io/badge/EACL%202026-orange) ![](https://img.shields.io/badge/Oral-blue)
- **HLD: Approximate Hierarchical Linguistic Distribution Modeling for LLM-Generated Text Detection.** [[paper]](https://proceedings.iclr.cc/paper_files/paper/2026/hash/836cf992a71f7a0bda218c180f942902-Abstract-Conference.html) ![](https://img.shields.io/badge/ICLR%202026-orange)
- **Beyond Raw Detection Scores: Markov-Informed Calibration for Boosting Machine-Generated Text Detection.** [[paper]](https://proceedings.iclr.cc/paper_files/paper/2026/hash/50b4eb974283cf1a7a60980ed06cf1be-Abstract-Conference.html) ![](https://img.shields.io/badge/ICLR%202026-orange)
  - A calibration method for token logits.
  - It's actually not a detector; I just temporarily place it under this heading in this readme.
- **D&R: Recovery-based AI-Generated Text Detection via a Single Black-box LLM Call.** [[paper]](https://proceedings.iclr.cc/paper_files/paper/2026/hash/f0a6b46b0183a62a2db973014e3429f4-Abstract-Conference.html) ![](https://img.shields.io/badge/ICLR%202026-orange)
- **ExaGPT: Example-Based Machine-Generated Text Detection for Human Interpretability.** [[paper]](https://aclanthology.org/2026.findings-acl.380) ![](https://img.shields.io/badge/ACL%20Findings%202026-orange)
- **Leveraging Human and Machine Preferences for Zero-shot Detection of AI-Generated Text.** [[paper]](https://aclanthology.org/2026.findings-acl.671) ![](https://img.shields.io/badge/ACL%20Findings%202026-orange)
- **DMHM: Density-aware Manifold Learning and Hybrid Mahalanobis Energy for LLMs-generated Text Detection.** [[paper]](https://aclanthology.org/2026.acl-long.180) ![](https://img.shields.io/badge/ACL%202026-orange)
- **Verifiable LLM-Generated Text Detection via Projected Semantic-Structural Distributions.** [[paper]](https://aclanthology.org/2026.acl-long.638) ![](https://img.shields.io/badge/ACL%202026-orange)
- **Exons-Detect: Identifying and Amplifying Exonic Tokens via Hidden-State Discrepancy for Robust AI-Generated Text Detection.** [[paper]](https://aclanthology.org/2026.acl-long.1211) ![](https://img.shields.io/badge/ACL%202026-orange)
  - A token reweighting method for dual-LLM metric-based detector
- **Zero-Shot Detection of LLM-Generated Text using Temperature Sensitivity.** [[paper]](https://aclanthology.org/2026.acl-long.1748) ![](https://img.shields.io/badge/ACL%202026-orange)
- **CodeRipple: Wavelet-Based Detection of LLM-Generated Code.** [[paper]](https://aclanthology.org/2026.acl-long.1777) ![](https://img.shields.io/badge/ACL%202026-orange)



### Neural-based

- **Defending Against Neural Fake News.** [[paper]](https://proceedings.neurips.cc/paper/2019/hash/3e9f0fc9b2f89e043bc6233994dfcf76-Abstract.html) ![](https://img.shields.io/badge/NIPS%202019-orange)
  - Includes a dataset named GROVER
- **Neural Deepfake Detection with Factual Structure of Text.** [[paper]](https://aclanthology.org/2020.emnlp-main.193) ![](https://img.shields.io/badge/EMNLP%202020-orange)
- **Matching Pairs: Attributing Fine-Tuned Models to their Pre-Trained Large Language Models.** [[paper]](https://aclanthology.org/2023.acl-long.410) ![](https://img.shields.io/badge/ACL%202023-orange)
- **RADAR: Robust AI-Text Detection via Adversarial Learning.** [[paper]](https://proceedings.neurips.cc/paper_files/paper/2023/hash/30e15e5941ae0cdab7ef58cc8d59a4ca-Abstract-Conference.html) ![](https://img.shields.io/badge/NIPS%202023-orange)
  - Generator-Detector Adversarial Reinforcement Learning, paraphrased text, human-AI mixed text
- **Do Stochastic Parrots have Feelings Too? Improving Neural Detection of Synthetic Text via Emotion Recognition.** [[paper]](https://aclanthology.org/2023.findings-emnlp.665/) ![](https://img.shields.io/badge/EMNLP%20Findings%202023-orange)
- **Token Prediction as Implicit Classification to Identify LLM-Generated Text.** [[paper]](https://aclanthology.org/2023.emnlp-main.810) ![](https://img.shields.io/badge/EMNLP%202023-orange) ![](https://img.shields.io/badge/Short-gray)
  - Includes a dataset named OpenLLMText
- **CoCo: Coherence-Enhanced Machine-Generated Text Detection Under Data Limitation With Contrastive Learning.** [[paper]](https://aclanthology.org/2023.emnlp-main.1005) ![](https://img.shields.io/badge/EMNLP%202023-orange)
- **SeqXGPT: Sentence-Level AI-Generated Text Detection.** [[paper]](https://aclanthology.org/2023.emnlp-main.73) ![](https://img.shields.io/badge/EMNLP%202023-orange)
  - Sentence-Level. Includes a SeqXGPT-Bench
- **Few-Shot Detection of Machine-Generated Text using Style Representations.** [[paper]](https://proceedings.iclr.cc/paper_files/paper/2024/hash/7e54a2592e3f51b9bed625a780310424-Abstract-Conference.html) ![](https://img.shields.io/badge/ICLR%202024-orange)
- **Multiscale Positive-Unlabeled Detection of AI-Generated Texts.** [[paper]](https://proceedings.iclr.cc/paper_files/paper/2024/hash/95ab5c3e26fd82c7de3230bbad087d2d-Abstract-Conference.html) ![](https://img.shields.io/badge/ICLR%202024-orange) ![](https://img.shields.io/badge/Spotlight-blue)
  - Multiscale Text Length
- **IDEATE: Detecting AI-Generated Text Using Internal and External Factual Structures.** [[paper]](https://aclanthology.org/2024.lrec-main.751) ![](https://img.shields.io/badge/COLING%202024-orange)
- **Machine-generated Text Localization.** [[paper]](https://aclanthology.org/2024.findings-acl.495) ![](https://img.shields.io/badge/ACL%20Findings%202024-orange)
  - Sentence-Level
- **Does DetectGPT fully utilize perturbation? Bridging selective perturbation to fine-tuned contrastive learning detector would be better** [[paper]](https://aclanthology.org/2024.acl-long.103) ![](https://img.shields.io/badge/ACL%202024-orange)
- **Are AI-Generated Text Detectors Robust to Adversarial Perturbations?** [[paper]](https://aclanthology.org/2024.acl-long.327) ![](https://img.shields.io/badge/ACL%202024-orange)
- **ReMoDetect: Reward Models Recognize Aligned LLM's Generations.** [[paper]](https://proceedings.neurips.cc/paper_files/paper/2024/hash/058373e239c542eae6cb796e4dc521da-Abstract-Conference.html) ![](https://img.shields.io/badge/NIPS%202024-orange)
- **DeTeCtive: Detecting AI-generated Text via Multi-Level Contrastive Learning.** [[paper]](https://proceedings.neurips.cc/paper_files/paper/2024/hash/a117a3cd54b7affad04618c77c2fb18b-Abstract-Conference.html) ![](https://img.shields.io/badge/NIPS%202024-orange)
- **Text Fluoroscopy: Detecting LLM-Generated Text through Intrinsic Features.** [[paper]](https://aclanthology.org/2024.emnlp-main.885) ![](https://img.shields.io/badge/EMNLP%202024-orange) ![](https://img.shields.io/badge/Short-gray)
- **Detecting Machine-Generated Long-Form Content with Latent-Space Variables.** [[paper]](https://aclanthology.org/2024.findings-emnlp.608/) ![](https://img.shields.io/badge/EMNLP%20Findings%202024-orange)
- **Robust AI-Generated Text Detection by Restricted Embeddings.** [[paper]](https://aclanthology.org/2024.findings-emnlp.992) ![](https://img.shields.io/badge/EMNLP%20Findings%202024-orange)
- **Multi-Loss Fusion: Angular and Contrastive Integration for Machine-Generated Text Detection.** [[paper]](https://aclanthology.org/2024.findings-emnlp.421/) ![](https://img.shields.io/badge/EMNLP%20Findings%202024-orange)
  - A hybrid optimization of loss function
- **AIDER: a Robust and Topic-Independent Framework for Detecting AI-Generated Text.** [[paper]](https://aclanthology.org/2025.coling-main.625) ![](https://img.shields.io/badge/COLING%202025-orange)
- **Deep kernel relative test for machine-generated text detection.** [[paper]](https://proceedings.iclr.cc/paper_files/paper/2025/hash/e124f1547f7ac87e33d348b827d4291b-Abstract-Conference.html) ![](https://img.shields.io/badge/ICLR%202025-orange)
- **Kill two birds with one stone: generalized and robust AI-generated text detection via dynamic perturbations.** [[paper]](https://aclanthology.org/2025.naacl-long/) ![](https://img.shields.io/badge/NAACL%202025-orange)
  - Adversarial Reinforcement Learning
- **M-RangeDetector: Enhancing Generalization in Machine-Generated Text Detection through Multi-Range Attention Masks.** [[paper]](https://aclanthology.org/2025.findings-acl.469) ![](https://img.shields.io/badge/ACL%20Findings%202025-orange)
- **ChatbotID: Identifying Chatbots with Granger Causality Test.** [[paper]](https://proceedings.neurips.cc/paper_files/paper/2025/hash/743514dfa1ef705f378424bd1effb57b-Abstract-Conference.html) ![](https://img.shields.io/badge/NIPS%202025-orange)
- **Advancing Machine-Generated Text Detection from an Easy to Hard Supervision Perspective.** [[paper]](https://proceedings.neurips.cc/paper_files/paper/2025/hash/dce5d66bf88b7b7d0f11fbd9c9f16620-Abstract-Conference.html) ![](https://img.shields.io/badge/NIPS%202025-orange)
  - A suggested training framework from easy to hard.
  - It's actually not a detector; I just temporarily place it under this heading in this readme.
- **Human Texts Are Outliers: Detecting LLM-generated Texts via Out-of-distribution Detection.** [[paper]](https://proceedings.neurips.cc/paper_files/paper/2025/hash/ef52fd1e24634cb8f7003ebbfb3644d9-Abstract-Conference.html) ![](https://img.shields.io/badge/NIPS%202025-orange)
- **IPAD: Inverse Prompt for AI Detection - A Robust and Interpretable LLM-Generated Text Detector.** [[paper]](https://proceedings.neurips.cc/paper_files/paper/2025/hash/f4d6932b6b9eec9b9d595e2847d095c1-Abstract-Conference.html) ![](https://img.shields.io/badge/NIPS%202025-orange)
- **DETree: DEtecting Human-AI Collaborative Texts via Tree-Structured Hierarchical Representation Learning.** [[paper]](https://proceedings.neurips.cc/paper_files/paper/2025/hash/d6f8517fceeca1e2cd61721dff786c14-Abstract-Conference.html) ![](https://img.shields.io/badge/NIPS%202025-orange)
  - Includes a benchmark named RealBench
- **How to Generalize the Detection of AI-Generated Text: Confounding Neurons.** [[paper]](https://aclanthology.org/2025.findings-emnlp.1388) ![](https://img.shields.io/badge/EMNLP%20Findings%202025-orange)
  - An erasure method that erases neurons associated with shortcuts related to a specific topic or domain.
  - It's actually not a detector; I just temporarily place it under this heading in this readme.
- **Residualized Similarity for Faithfully Explainable Authorship Verification.** [[paper]](https://aclanthology.org/2025.findings-emnlp.856) ![](https://img.shields.io/badge/EMNLP%20Findings%202025-orange)
  - An Interpretable Author Verification Method
- **Unmasking Fake Careers: Detecting Machine-Generated Career Trajectories via Multi-layer Heterogeneous Graphs.** [[paper]](https://aclanthology.org/2025.emnlp-main.1055) ![](https://img.shields.io/badge/EMNLP%202025-orange)
  - Detect Machine-Generated Career Trajectories
- **Non-Existent Relationship: Fact-Aware Multi-Level Machine-Generated Text Detection.** [[paper]](https://aclanthology.org/2025.emnlp-main.186) ![](https://img.shields.io/badge/EMNLP%202025-orange) ![](https://img.shields.io/badge/Oral-blue)
- **SenDetEX: Sentence-Level AI-Generated Text Detection for Human-AI Hybrid Content via Style and Context Fusion.** [[paper]](https://aclanthology.org/2025.emnlp-main.268) ![](https://img.shields.io/badge/EMNLP%202025-orange)
  - For human-AI mixed text, sentence-level detection (a sentence attributed to a single author)
- **DAMASHA: Detecting AI in Mixed Adversarial Texts via Segmentation with Human-interpretable Attribution.** [[paper]](https://aclanthology.org/2026.findings-eacl.326) ![](https://img.shields.io/badge/EACL%20Findings%202026-orange)
  - Machine-generated span within a mixed human-machine text
  - Includes a MAS dataset with human-AI mixed text and adversarial perturbations.
- **Utterance-level Detection Framework for LLM-Involved Content Detection in Conversational Setting.** [[paper]](https://aclanthology.org/2026.eacl-long.63/) ![](https://img.shields.io/badge/EACL%202026-orange) ![](https://img.shields.io/badge/Oral-blue)
  - Utterance-level detection in conversational setting
  - Includes a dataset
- **FAID: Fine-grained AI-generated Text Detection using Multi-task Auxiliary and Multi-level Contrastive Learning.** [[paper]](https://aclanthology.org/2026.eacl-long.151) ![](https://img.shields.io/badge/EACL%202026-orange) ![](https://img.shields.io/badge/Oral-blue)
  - Includes a benchmark named FAIDSet
- **EditLens: Quantifying the Extent of AI Editing in Text.** [[paper]](https://proceedings.iclr.cc/paper_files/paper/2026/hash/c7d936e696f57468b6b80a7b1463178c-Abstract-Conference.html) ![](https://img.shields.io/badge/ICLR%202026-orange)
  - machine-generated proportion in human-AI mixed text
- **Learning From Dictionary: Enhancing Robustness of Machine-Generated Text Detection in Zero-Shot Language via Adversarial Training.** [[paper]](https://proceedings.iclr.cc/paper_files/paper/2026/hash/e53280d73dd5389e820f4a6250365b0e-Abstract-Conference.html) ![](https://img.shields.io/badge/ICLR%202026-orange)
  - Multilingual Generalization and Adversarial Robustness
- **WaveDetect: Robust Framework for Machine-Generated Text Detection via Wavelet Transform.** [[paper]](https://aclanthology.org/2026.findings-acl.424) ![](https://img.shields.io/badge/ACL%20Findings%202026-orange)
- **Reasoning-Aware AIGC Detection via Alignment and Reinforcement.** [[paper]](https://aclanthology.org/2026.findings-acl.1043) ![](https://img.shields.io/badge/ACL%20Findings%202026-orange)
  - Includes a dataset named AIGC-text-bank
- **Breaking the Generator Barrier: Disentangled Representation for Generalizable AI-Text Detection.** [[paper]](https://aclanthology.org/2026.acl-long.120) ![](https://img.shields.io/badge/ACL%202026-orange)
- **Beyond the Final Actor: Modeling the Dual Roles of Creator and Editor for Fine-Grained LLM-Generated Text Detection.** [[paper]](https://aclanthology.org/2026.acl-long.235) ![](https://img.shields.io/badge/ACL%202026-orange) ![](https://img.shields.io/badge/Outstanding%20Paper-purple) ![](https://img.shields.io/badge/Oral-blue)
  - 4-class human-AI mixed text
- **LAMCL: A Length-aware Momentum Contrastive Learning Framework for Multiscale Machine-Revised Text Detection.** [[paper]](https://aclanthology.org/2026.acl-long.1118) ![](https://img.shields.io/badge/ACL%202026-orange)
  - Machine-revised human text detection
- **Explainable Disentangled Representation Learning for Generalizable Authorship Attribution in the Era of Generative AI.** [[paper]](https://aclanthology.org/2026.acl-long.2018) ![](https://img.shields.io/badge/ACL%202026-orange)
- **GPTZero: Robust Detection of LLM-Generated Texts.** [[paper]](https://arxiv.org/abs/2602.13042) ![](https://img.shields.io/badge/arXiv%202026-orange)



## Resources and Evaluation

### Pure MGT

- **Language Models are Unsupervised Multitask Learners.** [[paper]](https://cdn.openai.com/better-language-models/language_models_are_unsupervised_multitask_learners.pdf) ![](https://img.shields.io/badge/OpenAI%20blog%202019-orange)
- **Authorship Attribution for Neural Text Generation.** [[paper]](https://aclanthology.org/2020.emnlp-main.673) ![](https://img.shields.io/badge/EMNLP%202020-orange)
- **TweepFake: About detecting deepfake tweets.** [[paper]](https://journals.plos.org/plosone/article?id=10.1371/journal.pone.0251415) ![](https://img.shields.io/badge/PLOS%20ONE%202021-orange)
- **TURINGBENCH: A Benchmark Environment for Turing Test in the Age of Neural Text Generation.** [[paper]](https://aclanthology.org/2021.findings-emnlp.172) ![](https://img.shields.io/badge/EMNLP%20Findings%202021-orange)
- **Real or Fake Text?: Investigating Human Ability to Detect Boundaries Between Human-Written and Machine-Generated Text.** [[paper]](https://ojs.aaai.org/index.php/AAAI/article/view/26501) ![](https://img.shields.io/badge/AAAI%202023-orange)
- **How Close is ChatGPT to Human Experts? Comparison Corpus, Evaluation, and Detection.** [[paper]](https://arxiv.org/abs/2301.07597) ![](https://img.shields.io/badge/arXiv%202023-orange)
- **MGTBench: Benchmarking Machine-Generated Text Detection.** [[paper]](https://arxiv.org/abs/2303.14822) ![](https://img.shields.io/badge/arXiv%202023-orange)
- **Evaluating AIGC Detectors on Code Content.** [[paper]](https://arxiv.org/abs/2304.05193) ![](https://img.shields.io/badge/arXiv%202023-orange)
- **Distinguishing Fact from Fiction: A Benchmark Dataset for Identifying Machine-Generated Scientific Papers in the LLM Era.** [[paper]](https://aclanthology.org/2023.trustnlp-1.17/) ![](https://img.shields.io/badge/TrustNLP%202023-orange)
- **MULTITuDE: Large-Scale Multilingual Machine-Generated Text Detection Benchmark.** [[paper]](https://aclanthology.org/2023.emnlp-main.616/) ![](https://img.shields.io/badge/EMNLP%202023-orange)
- **OUTFOX: LLM-generated Essay Detection through In-context Learning with Adversarially Generated Examples.** [[paper]](https://ojs.aaai.org/index.php/AAAI/article/view/30120)![](https://img.shields.io/badge/AAAI%202024-orange)
- **M4: Multi-generator, Multi-domain, and Multi-lingual Black-Box Machine-Generated Text Detection.** [[paper]](https://aclanthology.org/2024.eacl-long.83/) ![](https://img.shields.io/badge/EACL%202024-orange) ![](https://img.shields.io/badge/Best%20Resource%20Paper-gold) ![](https://img.shields.io/badge/Oral-blue)
- **HC3 Plus: A Semantic-Invariant Human ChatGPT Comparison Corpus.** [[paper]](https://arxiv.org/abs/2309.02731) ![](https://img.shields.io/badge/arXiv%202023-orange)
- **BUST: Benchmark for the evaluation of detectors of LLM-Generated Text.** [[paper]](https://aclanthology.org/2024.naacl-long.444.pdf) ![](https://img.shields.io/badge/NAACL%202024-orange)
- **M4GT-Bench: Evaluation Benchmark for Black-Box Machine-Generated Text Detection.** [[paper]](https://aclanthology.org/2024.acl-long.218/) ![](https://img.shields.io/badge/ACL%202024-orange)
- **MAGE: Machine-generated Text Detection in the Wild.** [[paper]](https://aclanthology.org/2024.acl-long.3/) ![](https://img.shields.io/badge/ACL%202024-orange) 
- **RAID: A Shared Benchmark for Robust Evaluation of Machine-Generated Text Detectors.** [[paper]](https://aclanthology.org/2024.acl-long.674/) ![](https://img.shields.io/badge/ACL%202024-orange)
- **On the Generalization of Training-based ChatGPT Detection Methods.** [[paper]](https://aclanthology.org/2024.findings-emnlp.424) ![](https://img.shields.io/badge/EMNLP%20Findings%202024-orange)
- **TH-Bench: Evaluating Evading Attacks via Humanizing AI Text on Machine-Generated Text Detectors.** [[paper]](https://dl.acm.org/doi/abs/10.1145/3711896.3737418) ![](https://img.shields.io/badge/KDD%202025-orange)
- **Unmasking the Imposters: How Censorship and Domain Adaptation Affect the Detection of Machine-Generated Tweets.** [[paper]](https://aclanthology.org/2025.coling-main.607) ![](https://img.shields.io/badge/COLING%202025-orange)
- **CoDet-M4: Detecting Machine-Generated Code in Multi-Lingual, Multi-Generator and Multi-Domain Settings.** [[paper]](https://aclanthology.org/2025.findings-acl.550) ![](https://img.shields.io/badge/ACL%20Findings%202025-orange)
- **EvoBench: Towards Real-world LLM-Generated Text Detection Benchmarking for Evolving Large Language Models.** [[paper]](https://aclanthology.org/2025.findings-acl.754) ![](https://img.shields.io/badge/ACL%20Findings%202025-orange)
- **XDAC: XAI-Driven Detection and Attribution of LLM-Generated News Comments in Korean.** [[paper]](https://aclanthology.org/2025.acl-long.1108) ![](https://img.shields.io/badge/ACL%202025-orange)
- **Are We in the AI-Generated Text World Already? Quantifying and Monitoring AIGT on Social Media.** [[paper]](https://aclanthology.org/2025.acl-long.1120) ![](https://img.shields.io/badge/ACL%202025-orange)
- **MultiSocial: Multilingual Benchmark of Machine-Generated Text Detection of Social-Media Texts.** [[paper]](https://aclanthology.org/2025.acl-long.36/) ![](https://img.shields.io/badge/ACL%202025-orange)
- **X-TURING: Towards an Enhanced and Efficient Turing Test for Long-Term Dialogue Agents.** [[paper]](https://aclanthology.org/2025.acl-long.293) ![](https://img.shields.io/badge/ACL%202025-orange)
  - For Long-Term Dialogue Agents
- **Who Writes What: Unveiling the Impact of Author Roles on AI-generated Text Detection.** [[paper]](https://aclanthology.org/2025.acl-long.1292) ![](https://img.shields.io/badge/ACL%202025-orange)
- **AICD Bench: A Challenging Benchmark for AI-Generated Code Detection.** [[paper]](https://aclanthology.org/2026.eacl-long.325/) ![](https://img.shields.io/badge/EACL%202026-orange) ![](https://img.shields.io/badge/Oral-blue)
- **CEAID: Benchmark of Multilingual Machine-Generated Text Detection Methods for Central European Languages.** [[paper]](https://aclanthology.org/2026.findings-acl.1458) ![](https://img.shields.io/badge/ACL%20Findings%202026-orange)
- **C-ReD: A Comprehensive Chinese Benchmark for AI-Generated Text Detection Derived from Real-World Prompts.** [[paper]](https://aclanthology.org/2026.findings-acl.2119) ![](https://img.shields.io/badge/ACL%20Findings%202026-orange)
- **When Personalization Tricks Detectors: The Feature-Inversion Trap in Machine-Generated Text Detection.** [[paper]](https://aclanthology.org/2026.acl-long.1998) ![](https://img.shields.io/badge/ACL%202026-orange)
  - Actually, I think the MGT generated by Stylo-Blog's few-shots can be considered human-AI mixed text to some extent.

- **MAGA-Bench: Machine-Augment-Generated Text via Alignment Detection Benchmark.** [[paper]](https://arxiv.org/abs/2601.04633) ![](https://img.shields.io/badge/arXiv%202026-orange)



### Human-AI Mixed Text

Note: The benchmarks listed under this subsection include Human-AI Mixed Text. Most of them analyze such text by categorizing it as one of the types of text that need to be detected. A few benchmarks, however, treat it more specifically as a distinct category, such as MixSet (NAACL 2024), LAMP (CHI 2025), and Beemo (NAACL 2025).

- **CoAuthor: Designing a Human-AI Collaborative Writing Dataset for Exploring Language Model Capabilities.** [[paper]](https://dl.acm.org/doi/abs/10.1145/3491102.3502030) ![](https://img.shields.io/badge/CHI%202022-orange)

- **Towards Automatic Boundary Detection for Human-AI Collaborative Hybrid Essay in Education.** [[paper]](https://ojs.aaai.org/index.php/AAAI/article/view/30258) ![](https://img.shields.io/badge/AAAI%202024-orange)
- **LLM-as-a-Coauthor: Can Mixed Human-Written and Machine-Generated Text Be Detected?** [[paper]](https://aclanthology.org/2024.findings-naacl.29/) ![](https://img.shields.io/badge/NAACL%20Findings%202024-orange)
- **Spotting AI's Touch: Identifying LLM-Paraphrased Spans in Text.** [[paper]](https://aclanthology.org/2024.findings-acl.423) ![](https://img.shields.io/badge/ACL%20Findings%202024-orange)
- **DetectRL: Benchmarking LLM-Generated Text Detection in Real-World Scenarios.** [[paper]](https://proceedings.neurips.cc/paper_files/paper/2024/hash/b61bdf7e9f64c04ec75a26e781e2ad51-Abstract-Datasets_and_Benchmarks_Track.html) ![](https://img.shields.io/badge/NIPS%202024-orange)
- **Can AI writing be salvaged? Mitigating Idiosyncrasies and Improving Human-AI Alignment in the Writing Process through Edits.** [[paper]](https://dl.acm.org/doi/full/10.1145/3706598.3713559) ![](https://img.shields.io/badge/CHI%202025-orange)
- **Beemo: Benchmark of Expert-edited Machine-generated Outputs.** [[paper]](https://aclanthology.org/2025.naacl-long.357/) ![](https://img.shields.io/badge/NAACL%202025-orange)
- **CHEAT: A Large-Scale Dataset for Detecting CHatGPT-writtEn AbsTracts.** [[paper]](https://ieeexplore.ieee.org/abstract/document/10858415) ![](https://img.shields.io/badge/TBD%202025-orange)
- **Almost AI, Almost Human: The Challenge of Detecting AI-Polished Writing.** [[paper]](https://aclanthology.org/2025.findings-acl.1303) ![](https://img.shields.io/badge/ACL%20Findings%202025-orange) ![](https://img.shields.io/badge/Short-gray)
- **HACo-Det: A Study Towards Fine-Grained Machine-Generated Text Detection under Human-AI Coauthoring.** [[paper]](https://aclanthology.org/2025.acl-long.1069) ![](https://img.shields.io/badge/ACL%202025-orange)
- **OpenTuringBench: An Open-Model-based Benchmark and Framework for Machine-Generated Text Detection and Attribution.** [[paper]](https://aclanthology.org/2025.emnlp-main.1354) ![](https://img.shields.io/badge/EMNLP%202025-orange)
- **Droid: A Resource Suite for AI-Generated Code Detection.** [[paper]](https://aclanthology.org/2025.emnlp-main.1593) ![](https://img.shields.io/badge/EMNLP%202025-orange)
- **Is Your Paper Being Reviewed by an LLM? Benchmarking AI Text Detection in Peer Review.** [[paper]](https://proceedings.iclr.cc/paper_files/paper/2026/hash/64592277a1cbb02ed6e26b6e9e5f458c-Abstract-Conference.html) ![](https://img.shields.io/badge/ICLR%202026-orange)
- **TSM-Bench: Detecting LLM-Generated Text in Real-World Wikipedia Editing Practices.** [[paper]](https://proceedings.iclr.cc/paper_files/paper/2026/hash/7cd27a63aa7a5e7a8c79876a26ebb38b-Abstract-Conference.html) ![](https://img.shields.io/badge/ICLR%202026-orange)
- **CoCoNUTS: Concentrating on Content while Neglecting Uninformative Textual Styles for AI-Generated Peer Review Detection.** [[paper]](https://aclanthology.org/2026.acl-long.1240) ![](https://img.shields.io/badge/ACL%202026-orange)
  - Includes a detector named CoCoDet
- **Who Wrote This Line? Evaluating the Detection of LLM-Generated Classical Chinese Poetry.** [[paper]](https://aclanthology.org/2026.acl-long.245) ![](https://img.shields.io/badge/ACL%202026-orange)




## Attacks

- **How Reliable Are AI-Generated-Text Detectors? An Assessment Framework Using Evasive Soft Prompts.** [[paper]](https://aclanthology.org/anthology-files/anthology-files/pdf/findings/2023.findings-emnlp.94) ![](https://img.shields.io/badge/EMNLP%20Findings%202023-orange)
  - Adversarial Reinforcement Learning, prompt embedding injection
- **Paraphrasing evades detectors of AI-generated text, but retrieval is an effective defense.** [[paper]](https://proceedings.neurips.cc/paper_files/paper/2023/hash/575c450013d0e99e4b0ecf82bd1afaa4-Abstract-Conference.html) ![](https://img.shields.io/badge/NeurIPS%202023-orange)
- **Hidding the Ghostwriters: An Adversarial Evaluation of AI-Generated Student Essay Detection.** [[paper]](https://aclanthology.org/2023.emnlp-main.644) ![](https://img.shields.io/badge/EMNLP%202023-orange) ![](https://img.shields.io/badge/Oral-blue)
- **ALISON: Fast and Effective Stylometric Authorship Obfuscation.** [[paper]](https://ojs.aaai.org/index.php/AAAI/article/view/29901) ![](https://img.shields.io/badge/AAAI%202024-orange)
- **Humanizing Machine-Generated Content: Evading AI-Text Detection through Adversarial Attack.** [[paper]](https://aclanthology.org/2024.lrec-main.739) ![](https://img.shields.io/badge/COLING%202024-orange)
- **Language model detectors are easily optimized against.** [[paper]](https://proceedings.iclr.cc/paper_files/paper/2024/hash/1f9f07df0992ce21698d800eaa891bd8-Abstract-Conference.html) ![](https://img.shields.io/badge/ICLR%202024-orange)
  - Generator-Detector Adversarial Reinforcement Learning
- **Humanizing the Machine: Proxy Attacks to Mislead LLM Detectors.** [[paper]](https://proceedings.iclr.cc/paper_files/paper/2025/hash/ab1ee157f7804a13f980414b644a9460-Abstract-Conference.html) ![](https://img.shields.io/badge/ICLR%202025-orange)
  - Generator-Detector Adversarial Reinforcement Learning
- **Iron Sharpens Iron: Defending Against Attacks in Machine-Generated Text Detection with Adversarial Training.** [[paper]](https://aclanthology.org/2025.acl-long.155) ![](https://img.shields.io/badge/ACL%202025-orange)
- **AuthorMist: Evading AI Text Detectors with Reinforcement Learning.** [[paper]](https://arxiv.org/abs/2503.08716) ![](https://img.shields.io/badge/arXiv%202025-orange)
  - Generator-Detector Adversarial Reinforcement Learning, paraphrased text
- **Adversarial Paraphrasing: A Universal Attack for Humanizing AI-Generated Text.** [[paper]](https://proceedings.neurips.cc/paper_files/paper/2025/hash/443f314cd420ce621b6e748fd1194ed8-Abstract-Conference.html) ![](https://img.shields.io/badge/NIPS%202025-orange)
  - Generator-Detector Adversarial, training free, paraphrased text
- **Your Language Model Can Secretly Write Like Humans: Contrastive Paraphrase Attacks on LLM-Generated Text Detectors.** [[paper]](https://aclanthology.org/2025.emnlp-main.433) ![](https://img.shields.io/badge/EMNLP%202025-orange)
  - paraphrased text
- **TempParaphraser: “Heating Up” Text to Evade AI-Text Detection through Paraphrasing.** [[paper]](https://aclanthology.org/2025.emnlp-main.1607/) ![](https://img.shields.io/badge/EMNLP%202025-orange)
  - paraphrased text, data selection based on detector-adversarial objectives
- **MASH: Evading Black-Box AI-Generated Text Detectors via Style Humanization.** [[paper]](https://aclanthology.org/2026.findings-acl.1487) ![](https://img.shields.io/badge/ACL%20Findings%202026-orange)




## Analysis

- **Automatic Detection of Generated Text is Easiest when Humans are Fooled.** [[paper]](https://aclanthology.org/2020.acl-main.164) ![](https://img.shields.io/badge/ACL%202020-orange)
- **Reverse Engineering Configurations of Neural Text Generation Models.** [[paper]](https://aclanthology.org/2020.acl-main.25) ![](https://img.shields.io/badge/ACL%202020-orange)
- **All That's 'Human' Is Not Gold: Evaluating Human Evaluation of Generated Text.** [[paper]](https://aclanthology.org/2021.acl-long.565) ![](https://img.shields.io/badge/ACL%202021-orange)
- **Cross-Domain Detection of GPT-2-Generated Technical Text.** [[paper]](https://aclanthology.org/2022.naacl-main.88) ![](https://img.shields.io/badge/NAACL%202022-orange)
- **Is GPT-3 Text Indistinguishable from Human Text? Scarecrow: A Framework for Scrutinizing Machine Text.** [[paper]](https://aclanthology.org/2022.acl-long.501) ![](https://img.shields.io/badge/ACL%202022-orange)
- **On the Zero-Shot Generalization of Machine-Generated Text Detectors.** [[paper]](https://aclanthology.org/2023.findings-emnlp.318) ![](https://img.shields.io/badge/EMNLP%20Findings%202023-orange) ![](https://img.shields.io/badge/Short-gray)
- **Counter Turing Test CT^2: AI-Generated Text Detection is Not as Easy as You May Think -- Introducing AI Detectability Index.** [[paper]](https://aclanthology.org/2023.emnlp-main.136) ![](https://img.shields.io/badge/EMNLP%202023-orange) ![](https://img.shields.io/badge/Outstanding%20Paper-purple) ![](https://img.shields.io/badge/Oral-blue)
- **Smaller Language Models are Better Black-box Machine-Generated Text Detectors.** [[paper]](https://aclanthology.org/2024.eacl-short.25) ![](https://img.shields.io/badge/EACL%202024-orange)
- **Limitations of Human Identification of Automatically Generated Text.** [[paper]](https://aclanthology.org/2024.lrec-main.919) ![](https://img.shields.io/badge/COLING%202024-orange) ![](https://img.shields.io/badge/Short-gray)
- **Automatic Authorship Analysis in Human-AI Collaborative Writing.** [[paper]](https://aclanthology.org/2024.lrec-main.165) ![](https://img.shields.io/badge/COLING%202024-orange)
- **Position: On the Possibilities of AI-Generated Text Detection.** [[paper]](https://proceedings.mlr.press/v235/chakraborty24a.html) ![](https://img.shields.io/badge/ICML%202024-orange)
  - Position Paper
- **Stumbling Blocks: Stress Testing the Robustness of Machine-Generated Text Detectors Under Attacks.** [[paper]](https://aclanthology.org/2024.acl-long.160) ![](https://img.shields.io/badge/ACL%202024-orange)
- **Navigating the Shadows: Unveiling Effective Disturbances for Modern AI Content Detectors.** [[paper]](https://aclanthology.org/2024.acl-long.584) ![](https://img.shields.io/badge/ACL%202024-orange)
- **AI-generated text boundary detection with RoFT.** [[paper]](https://openreview.net/pdf?id=kzzwTrt04Z) ![](https://img.shields.io/badge/COLM%202024-orange) ![](https://img.shields.io/badge/Outstanding%20Paper-purple)
- **How You Prompt Matters! Even Task-Oriented Constraints in Instructions Affect LLM-Generated Text Detection.** [[paper]](https://aclanthology.org/2024.findings-emnlp.841/) ![](https://img.shields.io/badge/EMNLP%20Findings%202024-orange)
- **Authorship Obfuscation in Multilingual Machine-Generated Text Detection.** [[paper]](https://aclanthology.org/2024.findings-emnlp.369/) ![](https://img.shields.io/badge/EMNLP%20Findings%202024-orange)
- **Exploring the Limitations of Detecting Machine-Generated Text.** [[paper]](https://aclanthology.org/2025.coling-main.288) ![](https://img.shields.io/badge/COLING%202025-orange) ![](https://img.shields.io/badge/Short-gray)
- **Stress-testing Machine Generated Text Detection: Shifting Language Models Writing Style to Fool Detectors.** [[paper]](https://aclanthology.org/2025.findings-acl.156) ![](https://img.shields.io/badge/ACL%20Findings%202025-orange)
- **People who frequently use ChatGPT for writing tasks are accurate and robust detectors of AI-generated text.** [[paper]](https://aclanthology.org/2025.acl-long.267) ![](https://img.shields.io/badge/ACL%202025-orange)
- **Quantifying Misattribution Unfairness in Authorship Attribution.** [[paper]](https://aclanthology.org/2025.acl-short.80/) ![](https://img.shields.io/badge/ACL%202025-orange) ![](https://img.shields.io/badge/Short-gray)
- **Catch Me If You Can? Not Yet: LLMs Still Struggle to Imitate the Implicit Writing Styles of Everyday Authors.** [[paper]](https://aclanthology.org/2025.findings-emnlp.532) ![](https://img.shields.io/badge/EMNLP%20Findings%202025-orange)
- **How Sampling Affects the Detectability of Machine-written texts: A Comprehensive Study.** [[paper]](https://aclanthology.org/2025.findings-emnlp.609) ![](https://img.shields.io/badge/EMNLP%20Findings%202025-orange)
- **Linguistic and Embedding-Based Profiling of Texts Generated by Humans and Large Language Models.** [[paper]](https://aclanthology.org/2025.emnlp-main.1163) ![](https://img.shields.io/badge/EMNLP%202025-orange)
- **Explaining Generalization of AI-Generated Text Detectors Through Linguistic Analysis.** [[paper]](https://aclanthology.org/2026.eacl-long.307) ![](https://img.shields.io/badge/EACL%202026-orange) ![](https://img.shields.io/badge/Oral-blue)
- **Identifying Bias in Machine-generated Text Detection.** [[paper]](https://aclanthology.org/2026.acl-long.109) ![](https://img.shields.io/badge/ACL%202026-orange) ![](https://img.shields.io/badge/Oral-blue)
- **Can You Make It Sound Like You? Post-Editing LLM-Generated Text for Personal Style.** [[paper]](https://aclanthology.org/2026.acl-long.2030) ![](https://img.shields.io/badge/ACL%202026-orange)



## Systems and Tools

- **GLTR: Statistical Detection and Visualization of Generated Text.** [[paper]](https://aclanthology.org/P19-3019) ![](https://img.shields.io/badge/ACL%202019-orange) ![](https://img.shields.io/badge/Demos-gray) 
- **RoFT: A Tool for Evaluating Human Detection of Machine-Generated Text.** [[paper]](https://aclanthology.org/2020.emnlp-demos.25) ![](https://img.shields.io/badge/EMNLP%202020-orange) ![](https://img.shields.io/badge/Demos-gray) 
- **IMGTB: A Framework for Machine-Generated Text Detection Benchmarking.** [[paper]](https://aclanthology.org/2024.acl-demos.17) ![](https://img.shields.io/badge/ACL%202024-orange) ![](https://img.shields.io/badge/Demos-gray) 
- **LLM-DetectAIve: a tool for fine-grained machine-generated text detection.** [[paper]](https://aclanthology.org/2024.emnlp-demo.35/) ![](https://img.shields.io/badge/EMNLP%202024-orange) ![](https://img.shields.io/badge/Demos-gray)
  - Contains a new benchmark




## Other Related Works

- **MAUVE: Measuring the Gap Between Neural Text and Human Text using Divergence Frontiers.** [[paper]](https://proceedings.neurips.cc/paper_files/paper/2021/hash/260c2432a0eecc28ce03c10dadc078a4-Abstract.html) ![](https://img.shields.io/badge/NIPS%202021-orange) ![](https://img.shields.io/badge/Oral-purple)
- **Automatic Detection of Entity-Manipulated Text using Factual Knowledge.** [[paper]](https://aclanthology.org/2022.acl-short.10) ![](https://img.shields.io/badge/ACL%202022-orange) ![](https://img.shields.io/badge/Short-gray) 
- **HANSEN: Human and AI Spoken Text Benchmark for Authorship Analysis.** [[paper]](https://aclanthology.org/2023.findings-emnlp.916) ![](https://img.shields.io/badge/EMNLP%20Findings%202023-orange)
- **Can Large Language Models Identify Authorship?.** [[paper]](https://aclanthology.org/2024.findings-emnlp.26) ![](https://img.shields.io/badge/EMNLP%20Findings%202024-orange)
- **Multi-View Collaborative Learning Network for Speech Deepfake Detection.** [[paper]](https://ojs.aaai.org/index.php/AAAI/article/view/32094) ![](https://img.shields.io/badge/AAAI%202025-orange)
- **Collaborative Evolution: Multi-Round Learning Between Large and Small Language Models for Emergent Fake News Detection.** [[paper]](https://ojs.aaai.org/index.php/AAAI/article/view/32109) ![](https://img.shields.io/badge/AAAI%202025-orange)
- **Improving Generalization for AI-Synthesized Voice Detection.** [[paper]](https://ojs.aaai.org/index.php/AAAI/article/view/34221) ![](https://img.shields.io/badge/AAAI%202025-orange)
- **Towards Human Understanding of Paraphrase Types in Large Language Models.** [[paper]](https://aclanthology.org/2025.coling-main.421) ![](https://img.shields.io/badge/COLING%202025-orange)
- **Why Does ChatGPT “Delve” So Much? Exploring the Sources of Lexical Overrepresentation in Large Language Models.** [[paper]](https://aclanthology.org/2025.coling-main.426) ![](https://img.shields.io/badge/COLING%202025-orange)
- **Detecting deepfakes and false ads through analysis of text and social engineering techniques.** [[paper]](https://aclanthology.org/2025.coling-main.564/) ![](https://img.shields.io/badge/COLING%202025-orange)
- **SONICS: Synthetic Or Not - Identifying Counterfeit Songs.** [[paper]](https://proceedings.iclr.cc/paper_files/paper/2025/hash/36e2967f87c3362e37cf988781a887ad-Abstract-Conference.html) ![](https://img.shields.io/badge/ICLR%202025-orange)
- **PlagBench: Exploring the Duality of Large Language Models in Plagiarism Generation and Detection.** [[paper]](https://aclanthology.org/2025.naacl-long.384/) ![](https://img.shields.io/badge/NAACL%202025-orange) ![](https://img.shields.io/badge/Oral-blue)
- **AID: Adaptive Integration of Detectors for Safe AI with Language Models.** [[paper]](https://aclanthology.org/2025.naacl-long.229/) ![](https://img.shields.io/badge/NAACL%202025-orange) ![](https://img.shields.io/badge/Oral-blue)
- **Structure-adaptive Adversarial Contrastive Learning for Multi-Domain Fake News Detection.** [[paper]](https://aclanthology.org/2025.findings-acl.505/) ![](https://img.shields.io/badge/ACL%20Findings%202025-orange)
- **Human Bias in the Face of AI: Examining Human Judgment Against Text Labeled as AI Generated.** [[paper]](https://aclanthology.org/2025.findings-acl.1329/) ![](https://img.shields.io/badge/ACL%20Findings%202025-orange) ![](https://img.shields.io/badge/Short-gray)
- **Detection of Human and Machine-Authored Fake News in Urdu.** [[paper]](https://aclanthology.org/2025.acl-long.170) ![](https://img.shields.io/badge/ACL%202025-orange)
- **TripleFact: Defending Data Contamination in the Evaluation of LLM-driven Fake News Detection.** [[paper]](https://aclanthology.org/2025.acl-long.431) ![](https://img.shields.io/badge/ACL%202025-orange)
- **Comparing LLM-generated and human-authored news text using formal syntactic theory.** [[paper]](https://aclanthology.org/2025.acl-long.443) ![](https://img.shields.io/badge/ACL%202025-orange)
- **SpeechFake: A Large-Scale Multilingual Speech Deepfake Dataset Incorporating Cutting-Edge Generation Methods.** [[paper]](https://aclanthology.org/2025.acl-long.493) ![](https://img.shields.io/badge/ACL%202025-orange)
- **Generate First, Then Sample: Enhancing Fake News Detection with LLM-Augmented Reinforced Sampling.** [[paper]](https://aclanthology.org/2025.acl-long.1182/) ![](https://img.shields.io/badge/ACL%202025-orange)
- **PCoT: Persuasion-Augmented Chain of Thought for Detecting Fake News and Social Media Disinformation.** [[paper]](https://aclanthology.org/2025.acl-long.1215/) ![](https://img.shields.io/badge/ACL%202025-orange)
- **HumT DumT: Measuring and controlling human-like language in LLMs.** [[paper]](https://aclanthology.org/2025.acl-long.1261/) ![](https://img.shields.io/badge/ACL%202025-orange)
- **Stop DDoS Attacking the Research Community with AI-Generated Survey Papers.** [[paper]](https://proceedings.neurips.cc/paper_files/paper/2025/hash/74287109fd26ceb71bd4cef24c0a52c7-Abstract-Position_Paper_Track.html) ![](https://img.shields.io/badge/NIPS%202025-orange)
- **Position: Towards Bidirectional Human-AI Alignment.** [[paper]](https://proceedings.neurips.cc/paper_files/paper/2025/hash/b6ca96cdaad328fbee240e6fa8f609b0-Abstract-Position_Paper_Track.html) ![](https://img.shields.io/badge/NIPS%202025-orange)
- **Who’s the Author? How Explanations Impact User Reliance in AI-Assisted Authorship Attribution.** [[paper]](https://aclanthology.org/2025.findings-emnlp.1380) ![](https://img.shields.io/badge/EMNLP%20Findings%202025-orange)
- **A Symbolic Adversarial Learning Framework for Evolving Fake News Generation and Detection.** [[paper]](https://aclanthology.org/2025.emnlp-main.619/) ![](https://img.shields.io/badge/EMNLP%202025-orange)
- **SSA: Semantic Contamination of LLM-Driven Fake News Detection.** [[paper]](https://aclanthology.org/2025.emnlp-main.744/) ![](https://img.shields.io/badge/EMNLP%202025-orange)
- **DRES: Fake news detection by dynamic representation and ensemble selection.** [[paper]](https://aclanthology.org/2025.emnlp-main.1013/) ![](https://img.shields.io/badge/EMNLP%202025-orange) ![](https://img.shields.io/badge/Oral-blue)
- **The Stepwise Deception: Simulating the Evolution from True News to Fake News with LLM Agents.** [[paper]](https://aclanthology.org/2025.emnlp-main.1330/) ![](https://img.shields.io/badge/EMNLP%202025-orange) ![](https://img.shields.io/badge/Oral-blue)
- **Machine-generated text detection prevents language model collapse.** [[paper]](https://aclanthology.org/2025.emnlp-main.1506) ![](https://img.shields.io/badge/EMNLP%202025-orange)
- **PHPFND: Detecting Fake News via Post-Hoc Processing of LLMs Hallucination.** [[paper]](https://ojs.aaai.org/index.php/AAAI/article/view/37050) ![](https://img.shields.io/badge/AAAI%202026-orange)
- **XMAD-Bench: Cross-Domain Multilingual Audio Deepfake Benchmark.** [[paper]](https://aclanthology.org/2026.findings-eacl.162/) ![](https://img.shields.io/badge/EACL%20Findings%202026-orange)
- **Human or Machine? A Preliminary Turing Test for Speech-to-Speech Interaction.** [[paper]](https://proceedings.iclr.cc/paper_files/paper/2026/hash/5c1a8aa04c1a2cf5013f28831870dafa-Abstract-Conference.html) ![](https://img.shields.io/badge/ICLR%202026-orange)
- **ZoFia: Zero-Shot Fake News Detection with Entity-Guided Retrieval and Multi-LLM Interaction.** [[paper]](https://aclanthology.org/2026.findings-acl.1083/) ![](https://img.shields.io/badge/ACL%20Findings%202026-orange)



## Workshops and Shared Tasks

- **RuATD: Russian Artificial Text Detection.** [[paper]](https://arxiv.org/abs/2206.01583) [[github]](https://github.com/dialogue-evaluation/RuATD)
- **AuTexTification: Automated Text Identification.** [[paper]](https://arxiv.org/abs/2309.11285) [[home]](https://sites.google.com/view/autextification/home)
- **CLIN33 Shared Task.** [[paper]](https://aclanthology.org/2024.clinj-13.11.pdf) [[home]](https://sites.google.com/view/shared-task-clin33/home)

- **ALTA Shared Task 2023.** [[paper]](https://aclanthology.org/2023.alta-1.17/) [[home]](https://www.alta.asn.au/events/sharedtask2023/description.html) [[home]](https://codalab.lisn.upsaclay.fr/competitions/14327)
- **SemEval-2024 Task 8.** [[paper]](https://aclanthology.org/2024.semeval-1.279/) [[home]](https://semeval.github.io/SemEval2024/tasks) [[github]](https://github.com/mbzuai-nlp/SemEval2024-task8)
- **Voight-Kampff Generative AI Authorship Verification 2024.** [[paper]](https://downloads.webis.de/publications/papers/bevendorff_2024d.pdf) [[home]](https://pan.webis.de/clef24/pan24-web/generated-content-analysis.html)
- **DAGPap24: Detecting automatically generated scientific papers.** [[paper]](https://aclanthology.org/2024.sdp-1.2/) [[home]](https://sdproc.org/2024/sharedtasks.html#dagpap) [[home]](https://www.codabench.org/competitions/2431/)
- **GenAI Content Detection Task 1: Binary Multilingual MGT Detection.** [[paper]](https://arxiv.org/abs/2501.11012) [[home]](https://genai-content-detection.gitlab.io/sharedtasks) [[github]](https://github.com/mbzuai-nlp/COLING-2025-Workshop-on-MGT-Detection-Task1/)
- **GenAI Content Detection Task 2: AI vs. Human - Academic Essay Authenticity Challenge. ** [[paper]](https://arxiv.org/abs/2412.18274) [[home]](https://genai-content-detection.gitlab.io/sharedtasks) [[gitlab]](https://gitlab.com/genai-content-detection/genai-content-detection-coling-2025)
- **GenAI Content Detection Task 3: Cross-domain MGT detection.**  [[paper]](https://arxiv.org/abs/2501.08913) [[home]](https://genai-content-detection.gitlab.io/sharedtasks) [[github]](https://github.com/liamdugan/COLING-2025-Workshop-on-MGT-Detection-Task-3)
- **Voight-Kampff Generative AI Detection 2025.** [[paper]](https://downloads.webis.de/publications/papers/bevendorff_2025c.pdf) [[home]](https://pan.webis.de/clef25/pan25-web/generated-content-analysis.html)
- **RANLP 2025 Multi-Domain Detection of AI-Generated Text (M-DAIGT).** [[paper]](https://aclanthology.org/2025.ranlp-mdaigt.1/) [[home]](https://ezzini.github.io/M-DAIGT/)
- **AraGenEval: Arabic Authorship Style Transfer and AI Generated Text Detection Shared Task.** [[paper]](https://aclanthology.org/2025.arabicnlp-sharedtasks.1/) [[home]](https://ezzini.github.io/AraGenEval/)
- **AbjadGenEval: Abjad AI Generated Text Detection Shared Task for Languages Using Arabic Script.** [[paper]](https://aclanthology.org/2026.abjadnlp-1.68/) [[home]](https://ezzini.github.io/AbjadGenEval/)
- **Voight-Kampff Generative AI Detection 2026.** [[paper]](https://downloads.webis.de/publications/papers/bevendorff_2026a.pdf) [[home]](https://pan.webis.de/clef26/pan26-web/generated-content-analysis.html)



