# -0-1-medical-text-cleaner-
一个用于清洗非结构化临床文本的 Python 工具包。处理医疗 AI 交付中的去噪、分句和数据格式化。
# Medical Text Cleaner (临床文本清洗工具箱)

![Python](https://img.shields.io/badge/Python-3.8%2B-blue)
![License](https://img.shields.io/badge/License-MIT-green)
![Status](https://img.shields.io/badge/Status-Active-success)

## 📖 简介 (Introduction)

**Medical Text Cleaner** 是一个专为 **医疗 AI 交付 (Medical AI Delivery)** 场景设计的 Python 文本预处理工具包。

在处理电子病历 (EMR)、出院小结或医学文献时，原始数据往往包含大量的格式噪声（如 OCR 乱码、异常换行、排版混乱）。这些噪声会严重影响大模型 (LLM) 的 RAG 检索效果和生成准确率。

本项目旨在提供一套轻量级、可复用的清洗脚本，**将非结构化的脏数据转化为适合 LLM 理解的高质量语料**。

## ✨ 核心功能 (Features)

该工具包集成了医疗数据治理中常用的清洗逻辑：

- **智能分段 (Smart Segmentation)**:
    - 针对病历文本特点，在特定标点前自动插入换行符，优化语义切片 (Chunking)。
    - *核心逻辑*: 自动识别句号前的字符环境，确保断句符合临床阅读习惯。
- **引用格式标准化 (Citation Formatting)**:
    - 专门处理医学文献中的 "出处" (Reference) 字段，确保知识库构建时的元数据对齐。
    - *核心逻辑*: 自动定位 "出处" 关键词，并在其前序句号后强制分段。
- **噪声降维 (Noise Reduction)**:
    - 自动检测并合并冗余的空白字符，节省 Token 开销。
    - *核心逻辑*: 将连续 3 个及以上的换行符标准化为 2 个，去除无意义的排版空隙。
- **OCR 纠错 (OCR Correction)**: (开发中)
    - 针对常见医疗术语的 OCR 识别错误进行规则修正。

## 🛠️ 快速开始 (Quick Start)

### 1. 核心处理逻辑示例

```python
import re

class TextCleaner:
    def __init__(self, text):
        self.text = text

    def clean_newlines(self):
        """
        降噪：将3个及以上的连续换行符替换为2个
        场景：处理OCR识别后产生的过度空行，节省Token
        """
        self.text = re.sub(r'\n{3,}', '\n\n', self.text)
        return self

    def format_citations(self):
        """
        格式化：在'出处'关键词前的第一个句号后添加换行
        场景：将正文与参考文献元数据分离，便于RAG检索
        """
        # 查找 '出处' 前的句号并在其后添加换行
        self.text = re.sub(r'。(?=.*?出处)', '。\n', self.text)
        return self

    def semantic_segmentation(self):
        """
        分段：在句号前的字符前添加换行（特殊排版需求）
        场景：针对特定格式的病历报告进行结构化重排
        """
        # 此处为演示逻辑，实际通过正则捕获组实现
        self.text = re.sub(r'(.{1})(。)', r'\n\1\2', self.text)
        return self

    def get_text(self):
        return self.text

# 使用示例
raw_data = """
患者既往有高血压病史3年。。。   
    
文献参考。出处：XX医院病历系统
"""

cleaner = TextCleaner(raw_data)
processed_data = cleaner.clean_newlines().format_citations().get_text()
print(processed_data)


medical-text-cleaner/
├── data/               # 存放测试用的脱敏病历样本
├── src/
│   ├── cleaners.py     # 核心清洗逻辑 (Regex rules)
│   ├── utils.py        # 文件 I/O 工具
│   └── validators.py   # 数据质量校验
├── notebooks/          # Jupyter 演示文档 (Demo)
├── README.md           # 项目说明
└── requirements.txt    # 依赖库





