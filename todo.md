# VSCode + Python Plugin 启动指南

## 环境准备

### 1. 安装 Python
- 确保已安装 Python 3.10 或更高版本
- 下载地址: https://www.python.org/downloads/

### 2. 安装 VSCode 和 Python 插件
- 安装 VSCode: https://code.visualstudio.com/
- 在 VSCode 中安装 Python 插件 (Microsoft 官方插件)

## 项目启动步骤

### 1. 克隆项目 (如果还没有)
```bash
git clone https://github.com/huggingface/transformers.git
cd transformers
```

### 2. 创建虚拟环境
在项目根目录下打开终端，执行以下命令之一:

**使用 venv:**
```bash
python -m venv .my-env
# Windows 激活
.my-env\Scripts\activate
# Linux/Mac 激活
source .my-env/bin/activate
```

**使用 uv (更快):**
```bash
uv venv .my-env
# Windows 激活
.my-env\Scripts\activate
# Linux/Mac 激活
source .my-env/bin/activate
```

### 3. 安装项目依赖
在虚拟环境激活状态下执行:

**使用 pip:**
```bash
pip install '.[torch]'
```

**使用 uv:**
```bash
uv pip install '.[torch]'
```

### 4. 配置 VSCode Python 解释器

1. 在 VSCode 中打开项目文件夹
2. 按 `Ctrl+Shift+P` (Windows) 或 `Cmd+Shift+P` (Mac) 打开命令面板
3. 输入 "Python: Select Interpreter"
4. 选择刚才创建的虚拟环境中的 Python 解释器
   - 路径通常是: `.my-env\Scripts\python.exe` (Windows) 或 `.my-env/bin/python` (Linux/Mac)

### 5. 验证安装

在 VSCode 中创建一个测试文件 `test.py`:

```python
from transformers import pipeline

# 测试文本生成
pipe = pipeline(task="text-generation", model="Qwen/Qwen2.5-1.5B")
result = pipe("the secret to baking a really good cake is ")
print(result)
```

运行该文件，如果能正常输出结果，说明环境配置成功。

## 开发相关命令

### 运行代码风格检查
```bash
make style
```

### 运行类型检查
```bash
make typing
```

### 自动修复代码问题
```bash
make fix-repo
```

### 运行测试
```bash
# 运行普通测试
pytest

# 运行慢速测试
RUN_SLOW=1 pytest
```

## 常见问题

### 1. VSCode 无法找到虚拟环境
- 确保虚拟环境已激活
- 手动在 VSCode 中选择解释器: `Ctrl+Shift+P` -> "Python: Select Interpreter"

### 2. 依赖安装失败
- 确保 pip 是最新版本: `pip install --upgrade pip`
- 尝试使用 uv 代替 pip (速度更快)

### 3. PyTorch 相关问题
- 访问 https://pytorch.org/get-started/locally/ 获取适合你系统的 PyTorch 安装命令

## 模型引用指南

### 是否需要引用模型？
**是的**，Transformers 项目本身是一个模型框架，需要配合预训练模型使用。项目提供了模型定义和加载机制，但模型权重需要从 Hugging Face Hub 或其他来源下载。

### 如何选择合适的模型？

#### 按任务类型选择
- **文本生成**: GPT 系列、Llama 系列、Qwen 系列、Mistral
- **文本分类**: BERT 系列、RoBERTa、DistilBERT
- **问答系统**: BERT、RoBERTa、Longformer
- **机器翻译**: T5、mBART、NLLB
- **图像分类**: ViT、Swin Transformer、DiNO
- **语音识别**: Whisper、Wav2Vec2
- **多模态**: CLIP、BLIP、LLaVA

#### 按模型大小选择
- **小型模型** (<1B 参数): 适合快速实验、资源受限环境
  - `distilbert-base-uncased`
  - `gpt2`
  - `Qwen/Qwen2.5-0.5B-Instruct`

- **中型模型** (1B-7B 参数): 平衡性能和资源消耗
  - `meta-llama/Meta-Llama-3-8B-Instruct`
  - `Qwen/Qwen2.5-1.5B`
  - `mistralai/Mistral-7B-Instruct-v0.3`

- **大型模型** (>7B 参数): 最佳性能，需要更多资源
  - `meta-llama/Meta-Llama-3-70B-Instruct`
  - `Qwen/Qwen2.5-72B-Instruct`

### 推荐模型（2026年热门）

#### 文本生成推荐
```python
from transformers import pipeline

# 轻量级对话模型（推荐新手）
pipeline("text-generation", model="Qwen/Qwen2.5-0.5B-Instruct")

# 中等性能对话模型
pipeline("text-generation", model="meta-llama/Meta-Llama-3-8B-Instruct")

# 高性能对话模型
pipeline("text-generation", model="Qwen/Qwen2.5-7B-Instruct")
```

#### 图像处理推荐
```python
# 图像分类
pipeline("image-classification", model="facebook/dinov2-small-imagenet1k-1-layer")

# 语音识别
pipeline("automatic-speech-recognition", model="openai/whisper-large-v3")
```

### 模型加载方式

#### 1. 使用 Pipeline（最简单）
```python
from transformers import pipeline

# 自动下载并缓存模型
classifier = pipeline("sentiment-analysis", model="distilbert-base-uncased-finetuned-sst-2-english")
```

#### 2. 手动加载模型和分词器
```python
from transformers import AutoModel, AutoTokenizer

model_name = "bert-base-uncased"
tokenizer = AutoTokenizer.from_pretrained(model_name)
model = AutoModel.from_pretrained(model_name)
```

#### 3. 从本地加载
```python
from transformers import AutoModel

# 从本地目录加载
model = AutoModel.from_pretrained("./my_local_model")
```

### 模型资源要求
- **小型模型**: CPU 即可运行，内存需求 < 4GB
- **中型模型**: 推荐 GPU，内存需求 8-16GB
- **大型模型**: 必须使用 GPU，内存需求 32GB+，可能需要多GPU

### 模型下载和缓存
- 首次使用时模型会自动从 Hugging Face Hub 下载
- 模型会缓存在本地 `~/.cache/huggingface/` 目录
- 可通过 `HF_HOME` 环境变量自定义缓存路径
- 支持离线使用（模型下载后）

### 查找更多模型
访问 [Hugging Face Model Hub](https://huggingface.co/models) 浏览 100万+ 可用模型，按任务、语言、大小等筛选。

## 多模态 AI 会话工具开发指南

### Transformers 能为你的多模态会话工具提供什么？

Transformers 项目完全支持构建一个支持文字、语音、图片、视频、文档输入的 AI 会话工具。以下是具体支持的功能：

### 支持的输入类型及处理方式

#### 1. 文字输入
- **直接处理**: 所有对话模型原生支持
- **推荐模型**: Qwen2.5-Instruct, Llama-3-Instruct, Gemma-3n
- **实现方式**: 使用 `pipeline("text-generation")` 或 `pipeline("conversational")`

#### 2. 语音输入
- **语音转文字**: Whisper 系列、Wav2Vec2 系列
- **直接语音理解**: Qwen2-Audio、Qwen3-ASR
- **推荐模型**: `openai/whisper-large-v3`, `Qwen/Qwen2-Audio`

#### 3. 图片输入
- **视觉理解**: Qwen2-VL、Qwen3-VL、LLaVA 系列、Gemma-3n
- **OCR 文档识别**: PaddleOCR、LayoutLM、Nougat
- **推荐模型**: `Qwen/Qwen2-VL-7B-Instruct`, `llava-hf/llava-1.5-7b-hf`

#### 4. 视频输入
- **视频理解**: Video-LLaVA、LLaVA-NeXT-Video、Qwen2-VL
- **推荐模型**: `llava-hf/LLaVA-NeXT-Video-7B-hf`, `Qwen/Qwen2-VL-7B-Instruct`

#### 5. 文档输入
- **文档理解**: LayoutLM、Nougat（科学文档）、PaddleOCR
- **PDF 解析**: Nougat、ColPali
- **推荐模型**: `microsoft/layoutlm-base-uncased`, `facebook/nougat-base`

### 核心多模态 Pipeline

#### AnyToAnyPipeline（推荐）
这是最强大的多模态管道，支持任意模态组合：

```python
from transformers import pipeline

# 创建多模态对话管道
pipe = pipeline("any-to-any", model="google/gemma-3n-E4B-it")

# 支持多种输入格式
messages = [
    {
        "role": "user",
        "content": [
            {"type": "text", "text": "描述这张图片"},
            {"type": "image", "url": "path/to/image.jpg"},
            {"type": "audio", "url": "path/to/audio.wav"},
        ]
    }
]
response = pipe(messages)
```

#### ImageTextToTextPipeline
专门处理图像和文本的组合：

```python
pipe = pipeline("image-text-to-text", model="llava-hf/llava-1.5-7b-hf")
pipe(image="path/to/image.jpg", text="这张图片里有什么？")
```

### 推荐的多模态模型架构

#### 统一多模态模型（推荐）
这些模型可以同时处理多种输入类型：

- **Qwen2-VL / Qwen3-VL** - 文字+图片+视频，性能优秀
  ```python
  pipeline("any-to-any", model="Qwen/Qwen2-VL-7B-Instruct")
  ```

- **Gemma-3n** - Google 的原生多模态模型
  ```python
  pipeline("any-to-any", model="google/gemma-3n-E4B-it")
  ```

- **LLaVA 系列** - 专注于视觉-语言理解
  ```python
  pipeline("image-text-to-text", model="llava-hf/llava-1.5-7b-hf")
  ```

#### 专用模型组合
如果需要更好的特定模态性能，可以组合使用：

```python
# 语音识别 + 文本对话
asr = pipeline("automatic-speech-recognition", model="openai/whisper-large-v3")
chat = pipeline("text-generation", model="Qwen/Qwen2.5-7B-Instruct")

# 文档 OCR + 文本对话
ocr = pipeline("document-question-answering", model="impira/layoutlm-document-qa")
chat = pipeline("text-generation", model="Qwen/Qwen2.5-7B-Instruct")
```

### 完整的多模态会话工具架构建议

```
用户输入
    ↓
┌─────────────────────────────────────┐
│  输入预处理层                        │
│  - 文字: 直接传递                   │
│  - 语音: Whisper 转文字             │
│  - 图片: 图像处理器                 │
│  - 视频: 帧提取 + 图像处理器        │
│  - 文档: OCR/LayoutLM 提取文本      │
└─────────────────────────────────────┘
    ↓
┌─────────────────────────────────────┐
│  多模态模型层                        │
│  - Qwen2-VL / Gemma-3n              │
│  - 统一处理多种输入                 │
└─────────────────────────────────────┘
    ↓
┌─────────────────────────────────────┐
│  响应生成层                          │
│  - 文字响应                         │
│  - 语音响应（可选）                 │
└─────────────────────────────────────┘
```

### 快速开始示例

#### 基础多模态对话
```python
from transformers import pipeline

# 使用 Qwen2-VL 支持图片和文字
pipe = pipeline("any-to-any", model="Qwen/Qwen2-VL-7B-Instruct")

# 混合输入
messages = [
    {
        "role": "user",
        "content": [
            {"type": "image", "url": "https://example.com/image.jpg"},
            {"type": "text", "text": "这张图片显示了什么？请详细描述。"}
        ]
    }
]
result = pipe(messages)
print(result[0]['generated_text'])
```

#### 语音 + 文字对话
```python
from transformers import pipeline

# 语音转文字
asr = pipeline("automatic-speech-recognition", model="openai/whisper-large-v3")
# 文本对话
chat = pipeline("text-generation", model="Qwen/Qwen2.5-7B-Instruct")

# 处理语音输入
audio_text = asr("path/to/speech.wav")
# 对话
response = chat(f"用户说: {audio_text['text']}")
```

#### 文档问答
```python
from transformers import pipeline

# 文档问答
doc_qa = pipeline("document-question-answering", model="impira/layoutlm-document-qa")

question = "这个文档的主要内容是什么？"
result = doc_qa(image="path/to/document.png", question=question)
```

### 技术要求

#### 硬件要求
- **CPU**: 最低 8 核，推荐 16 核+
- **内存**: 最低 16GB，推荐 32GB+
- **GPU**: 推荐 NVIDIA GPU (16GB+ VRAM)
- **存储**: 50GB+ 用于模型缓存

#### 软件依赖
```bash
pip install transformers[torch,audio,vision]
pip install accelerate
pip install pillow  # 图像处理
pip install librosa  # 音频处理
```

### 项目能帮你完成的事情总结

✅ **文字处理**: 完全支持对话、问答、生成
✅ **语音处理**: 语音识别、语音理解
✅ **图片处理**: 图像理解、视觉问答、OCR
✅ **视频处理**: 视频理解、帧级分析
✅ **文档处理**: 文档解析、表格提取、科学公式识别
✅ **多模态融合**: 统一模型处理多种输入
✅ **模型部署**: 提供生产环境部署方案
✅ **性能优化**: 量化、分布式推理支持

Transformers 是构建多模态 AI 会话工具的理想框架，提供了从数据处理到模型推理的完整解决方案。

## 利用 Transformers 项目能做什么

### 文本处理任务
- **文本分类** - 情感分析、主题分类、垃圾邮件检测等
- **文本生成** - 创意写作、代码生成、对话系统
- **问答系统** - 阅读理解、知识问答、文档问答
- **文本摘要** - 文章摘要、会议记录总结
- **机器翻译** - 多语言翻译、语言转换
- **命名实体识别** - 提取人名、地名、组织名等
- **文本相似度** - 语义匹配、重复检测

### 图像处理任务
- **图像分类** - 物体识别、场景分类
- **目标检测** - 定位和识别图像中的多个物体
- **图像分割** - 语义分割、实例分割
- **图像描述生成** - 为图像生成文字描述
- **零样本图像分类** - 无需训练即可分类新类别

### 音频处理任务
- **语音识别** - 语音转文字
- **音频分类** - 音乐分类、环境音识别
- **语音合成** - 文字转语音
- **零样本音频分类** - 无需训练即可分类新音频类别

### 多模态任务
- **图文匹配** - 图像和文本的语义匹配
- **视觉问答** - 基于图像回答问题
- **图像到文本生成** - 图像描述、OCR

### 高级应用
- **模型微调** - 在自有数据上训练模型
- **模型量化** - 压缩模型大小，提升推理速度
- **分布式训练** - 在多GPU/多节点上训练大模型
- **模型部署** - 将模型部署到生产环境
- **自定义模型开发** - 基于现有架构创建新模型

### 快速开始示例
```python
from transformers import pipeline

# 文本分类
classifier = pipeline("sentiment-analysis")
result = classifier("I love this product!")

# 文本生成
generator = pipeline("text-generation", model="gpt2")
result = generator("Once upon a time")

# 问答
qa = pipeline("question-answering")
result = qa(question="What is AI?", context="AI is artificial intelligence")

# 图像分类
image_classifier = pipeline("image-classification")
result = image_classifier("path/to/image.jpg")
```

## Transformers 项目核心函数和类

### 模型核心类
- **PreTrainedModel** (`src/transformers/modeling_utils.py`) - 所有模型的基类，提供模型加载、保存、前向传播等核心功能
- **PreTrainedConfig** (`src/transformers/configuration_utils.py`) - 所有配置类的基类，管理模型超参数和配置

### 训练核心类
- **Trainer** (`src/transformers/trainer.py`) - 主要训练类，简化模型训练和微调流程
- **TrainingArguments** (`src/transformers/training_args.py`) - 训练参数配置类

### 分词器核心类
- **PreTrainedTokenizerBase** (`src/transformers/tokenization_utils_base.py`) - 分词器基类，提供文本编码/解码功能
- **PreTrainedTokenizer** (`src/transformers/tokenization_python.py`) - Python 实现的分词器基类

### Pipeline 核心函数
- **pipeline()** (`src/transformers/pipelines/__init__.py`) - 主要管道接口函数，用于快速推理
- **Pipeline** (`src/transformers/pipelines/base.py`) - 所有管道的基类

### 文本生成核心
- **GenerationMixin** (`src/transformers/generation/utils.py`) - 文本生成方法混入类
- **GenerationConfig** (`src/transformers/generation/configuration_utils.py`) - 文本生成配置类

### Auto 自动加载类
- **AutoModel** - 自动加载模型
- **AutoConfig** - 自动加载配置
- **AutoTokenizer** - 自动加载分词器
- **AutoProcessor** - 自动加载处理器（多模态）

### 数据处理核心
- **DataCollator** 系列类 (`src/transformers/data/data_collator.py`) - 训练数据批处理
- **ProcessorMixin** (`src/transformers/processing_utils.py`) - 多模态处理器基类

### 特征提取核心
- **FeatureExtractionMixin** (`src/transformers/feature_extraction_utils.py`) - 特征提取器基类
- **BaseImageProcessor** (`src/transformers/image_processing_utils.py`) - 图像处理器基类

## 下一步

- 查看 [README.md](./README.md) 了解项目更多信息
- 查看 [examples](./examples) 目录获取示例代码
- 访问 https://huggingface.co/docs/transformers/ 查看完整文档
