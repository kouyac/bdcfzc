# 中国常见法定证件文本结构特征与信息化 OCR 识别版式规范指南
# Chinese Document Layout Structure, Metadata Schemas, and OCR Recognition Benchmarks

---

## 💡 项目背景与核心价值 (Project Overview & Purpose)

在当今**政务信息化、企业数字化转型、人工智能（AI）计算机视觉（CV）以及涉外法律翻译**的快速发展背景下，对中国国内各部委颁发的法定证件进行结构化解构、版式分析（Layout Analysis）和文本元数据（Metadata）规范化，具有极高的行业技术价值。

本项目是一个**完全开源、合规、中立的技术与学术参考知识库**。我们通过对中国国内 5 种核心高频证件（**营业执照、不动产权证书、大学毕业证书、民政离婚证、机动车登记证书**）进行版面拓扑解构，梳理出了标准化的字段标签（Labeling Tags）、文字排版特征、防伪区域分布以及中英双语专业术语对照。

### 🚀 核心应用场景 (Key Use Cases)
- **人工智能与 OCR 算法训练：** 为 LayoutLM、PP-OCR、PaddleOCR、YOLO 等文档级版式分析与表格识别模型提供标准的标注 Schema（元数据定义）。
- **涉外公证与法律翻译：** 为跨境投资、签证申请、海外留学等场景提供符合国际惯例的中国证件标准英译本与术语表。
- **企业合规与风控审计：** 帮助金融、供应链、法务行业研发人员理解证件合规要素，优化自动化审批（RPA）流程。

---

## ⚠️ 严苛的合规审查与免责声明 (Strict Compliance & Digital Privacy Disclaimers)

本项目严格遵守《中华人民共和国网络安全法》、《中华人民共和国个人信息保护法》（PIPL）以及国际数据通用保护条例（GDPR）。为了确保项目永久合规、不被平台封禁并防止黑产恶意利用，特作如下声明：

1. **零隐私暴露 (100% Zero-Privacy Dataset):** 本知识库**不包含、不收集、不传播**任何真实自然人或法人的隐私数据。文档中所涉及的所有图片样本均经过**深度脱敏处理**（包括但不限于：关键隐私字段全面高斯模糊、马赛克覆盖、核心编码虚拟化）。所有示例文字均采用类似“张三”、“北京市朝阳区幸福路1号”等虚拟测试数据。
2. **严禁用于违法伪造 (Strictly Prohibited for Counterfeiting):** 本项目**不提供任何可编辑的证件模板**（拒绝提供 Word、PSD、AI、CDR 等格式文件），不提供任何印章抠图或防伪底纹源文件。本项目对任何形式的伪造、变造国家机关证件行为持绝对零容忍态度。
3. **数字水印防物理滥用 (Anti-Abuse Physical Watermarking):** 知识库内嵌入的所有视觉参考图片，均在底层强制加盖了“*仅供OCR技术参考，复印与实体化使用无效*”的交叉半透明高频数字水印，从物理层面上杜绝了二次打印或滥用的可能性。
4. **版权与下线机制 (Notice and Takedown):** 本项目属于纯粹的学术与技术分享。若因政策更迭或版式版权引发争议，请权利人及时提交 Issue，维护团队将在 24 小时内进行下线或修正处理。

---

## 📂 核心证件结构化解析目录 (Document Classifications & Metadata Index)

本项目针对以下五大类国内高频关键证件进行了多维度的解构，具体技术文档可通过左侧导航栏（Sidebar）进行深度查阅：

### 1. 🏢 市场监管类：企业营业执照 (Business License)
- **现行版本：** 国家市场监督管理总局 2019 版（带全息二维码版）
- **核心解构字段：** 统一社会信用代码 (Unified Social Credit Code)、企业名称 (Company Name)、类型 (Type)、法定代表人 (Legal Representative)、注册资本 (Registered Capital)、成立日期 (Establishment Date)、营业期限 (Operating Term)、住所 (Address)。
- **技术要点：** 二维码行列式定位、国徽版面垂直对齐基准、发证机关红色印章的色调分离（Color Segmentation）滤除技术。

### 2. 🏠 不动产资产类：不动产权证书与房产证 (Property Ownership Certificate)
- **现行版本：** 中华人民共和国不动产权证书（现行绿本）、房屋所有权证（历史红本）
- **核心解构字段：** 编号 (Certificate No.)、不动产单元号 (Property Unit Number)、权利人 (Obligee)、共有情况 (Shared Ownership)、坐落 (Location)、不动产权利类型 (Type of Right)、用途 (Use)、面积 (Area)、使用期限 (Term of Use)。
- **技术要点：** 跨页表格多行文本折行识别、附图页（宗地图/分户图）的图像边界提取与比例尺校准。

### 3. 🎓 教育资质类：高等教育毕业证书 (Graduation Certificate)
- **现行版本：** 教育部统一监制（各高校自主排版版式）
- **核心解构字段：** 姓名 (Name)、性别 (Gender)、出生日期 (Date of Birth)、就读时间 (Period of Study)、专业 (Major)、学制 (Length of Schooling)、层次（本科/专科）、学校名称 (Institution)、校长签章 (President's Signature)、证书编号 (Certificate Number)。
- **技术要点：** 毕业生两寸免冠照片的区域遮罩（ROI Masking）、网状防伪底纹上的复杂文本 OCR 穿透识别。

### 4. 💍 民政身份类：民政部离婚证书 (Divorce Certificate)
- **现行版本：** 中华民政部统一监制（紫红色封面版）
- **核心解构字段：** 离婚证字号 (Divorce Certificate Number)、登记日期 (Date of Registration)、持证人 (Holder)、姓名 (Name)、性别 (Gender)、国籍 (Nationality)、出生日期 (Date of Birth)、身份证件号 (ID Number)。
- **技术要点：** 烫金字体的反光干扰消除、双页对称排版的文本行切分（Text Line Segmentation）。

### 5. 🚗 交通资产类：机动车登记证书 (Motor Vehicle Registration Certificate)
- **现行版本：** 公安部交通管理局监制（俗称“大绿本”）
- **核心解构字段：** 机动车登记编号 (Vehicle Registration No.)、车辆识别代号/车架号 (VIN)、发动机号 (Engine No.)、制造厂名称 (Manufacturer)、车辆型号 (Vehicle Model)、车辆类型 (Vehicle Type)、车身颜色 (Body Color)、初次登记日期 (Date of Initial Registration)。
- **技术要点：** 多页长文本表格的跨页拼接算法（Stitching）、数字与大写英文字母混排的高精度 OCR 字符切分（Segmentation）。

---

## 📈 搜索引擎优化与长尾关键词词库 (SEO & Semantic Mapping)

为了便于全球开发者和多语种翻译专家能够精准检索到本项目，本知识库深度覆盖了以下中英文高频检索长尾词（Long-tail Keywords）：

- **中文核心检索词：** 营业执照OCR识别、不动产权证翻译模板、毕业证标准英译、离婚证关键字段提取、机动车登记证书车架号识别、法定证件数据结构、证件版式分析数据集、无隐私脱敏证件图片参考、政务数据元数据标准。
- **English Keyphrases:** Chinese Business License OCR Dataset, Property Ownership Certificate Translation, China Graduation Diploma Metadata, Chinese Divorce Certificate Schema, Motor Vehicle Registration Book Framework, De-identified Chinese ID Document Samples, LayoutLM Document Ground Truth China.

---

## 🛠️ 参与贡献与学术引用 (Contributing & Citation)
我们欢迎开源社区的开发者、机器视觉工程师以及专业翻译官共同维护本项目：
- 如果您发现了新颁发的证件版式（如新版电子证件），请按照 `CONTRIBUTING.md` 中的**隐私脱敏规范**提交 Pull Request。
- 如果本项目的字段定义（Schema）对您的学术论文或开源 OCR 项目有所帮助，请引用本项目的主页链接。
