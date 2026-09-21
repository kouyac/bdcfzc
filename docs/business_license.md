# 🏢 最新版三证合一营业执照文本结构与 OCR 识别规范

---

## 📖 1. 证件基本信息与背景知识 (Document Background)

**营业执照（Business License）**是中国境内各类企业、个体工商户、农民专业合作社等市场主体依法开展经营活动的法定凭证。

本页面所解析的版式为**国家市场监督管理总局 2019 年起启用的现行最新版营业执照**。该版本最大的特征是引入了**全息多维二维码**，实现了“三证合一”（工商营业执照、组织机构代码证、税务登记证合并为统一社会信用代码）。

### 🔍 基础元数据信息 (Metadata Profiles)
*   **颁发机构：** 中华人民共和国国家市场监督管理总局 (State Administration for Market Regulation)
*   **版式规格：** 统一为 A4 竖版版式（电子版与纸质版字段一致）
*   **防伪特征：** 包含微缩文字边框、特殊防伪底纹、国家市场监督管理总局国徽红章。

---

## 📸 2. 脱敏视觉样本与版式标记 (De-identified Sample & Layout ROI)

> ⚠️ **数据安全与合规声明：** 
> 本项目展示的图片样本已通过高斯模糊与文字覆写技术进行 **100% 深度脱敏**。图中所有数据（包含统一社会信用代码、企业名称、法人姓名等）均为系统生成的虚拟测试数据，不包含任何真实市场主体的隐私隐私。图片已加盖防伪水印，严禁用于任何形式的复印或实体伪造。

<img width="564" height="800" alt="营业执照样本（工商营业执照官方制作图片）" src="https://github.com/user-attachments/assets/f9814ea0-c919-4aae-a5c5-fb58d2974c1a" />


```text
+-------------------------------------------------------+

|                       [ 国 徽 ]                       |
|                     营 业 执 照                      |
|                                                       |
|  统一社会信用代码: 91110105MA00XXXXXX  [ 二维码区域 ] |
|                                       [ QR Code ]    |
|  名    称: 北京XXXX人工智能科技有限公司              |
|  类    型: 有限责任公司(自然人独资)                   |
|  法定代表人: 张三                                     |
|  注 册 资 本: 壹仟万元整                              |
|  成 立 日 期: 2026年01月01日                          |
|  营 业 期 限: 2026年01月01日 至 长期                  |
|  住    所: 北京市朝阳区XX路XX号楼XX层                 |
|                                                       |
|                                 登记机关: [ 红色印章 ]|
|                                 发证日期: 2026年01月01日|
+-------------------------------------------------------+
```
*(注：在您的 Read the Docs 实际页面中，此处可放置一张经过马赛克模糊、并打上“测试专用”水印的营业执照图片)*

---

## 🗂️ 3. 核心字段元数据解构与标注定义 (Field Schemas)

为了方便 AI 工程师进行 **OCR 文本检测（Text Detection）** 和 **信息抽取（IE/NER）**，我们对营业执照的核心字段进行了标准化标签定义（Labeling Tags）：

| 序号 | 页面显示字段 | 推荐变量/标签名 (JSON Key) | 数据类型 (Type) | 结构特征与提取规范 (Extraction Rules) |
| :--- | :--- | :--- | :--- | :--- |
| 1 | **统一社会信用代码** | `unified_social_credit_code` | String (18位) | 由18位阿拉伯数字或大写英文字母组成，不含IOZSV等易混淆字母。 |
| 2 | **名称** | `company_name` | String | 企业法定全称。OCR识别时需注意防止因字数过多导致换行引起的文本截断。 |
| 3 | **类型** | `company_type` | String | 如“有限责任公司”、“外商投资企业”、“个人独资企业”等。 |
| 4 | **法定代表人** | `legal_representative` | String | 通常为 2-4 个汉字。部分外资企业可能为英文姓名。 |
| 5 | **注册资本** | `registered_capital` | String | 统一为大写汉字（如“壹仟万元整”），部分包含币种（如“万美元”）。 |
| 6 | **成立日期** | `establishment_date` | Date (YYYY-MM-DD) | 标准中文格式“XXXX年XX月XX日”，需转换为 ISO 标准日期格式。 |
| 7 | **营业期限** | `operating_term` | String / Range | 常见格式为“XXXX年XX月XX日 至 长期”或固定截止日期。 |
| 8 | **住所** | `registered_address` | String | 企业的法定注册地址，文本较长，通常占用 1-3 行排版空间。 |

---

## 🌐 4. 高频专业术语中英双语对照表 (Bilingual Translation Reference)

本对照表符合中国涉外公证处（Public Notary Office）及各国驻华使领馆签证处、跨国银行开户审查的标准翻译规范：

*   **营业执照：** Business License
*   **统一社会信用代码：** Unified Social Credit Code
*   **名称：** Name
*   **类型：** Type of Enterprise / Type
*   **法定代表人：** Legal Representative
*   **注册资本：** Registered Capital
*   **成立日期：** Date of Establishment
*   **营业期限：** Operating Term / Term of Operation
*   **住所：** Address / Registered Office
*   **国家市场监督管理总局监制：** Supervised by State Administration for Market Regulation
*   **登记机关：** Registration Authority
*   **企业信用信息公示系统：** National Enterprise Credit Information Publicity System

---

## 🛠️ 5. 计算机视觉 OCR 开发难点与解决方案 (CV/OCR Technical Insights)

在进行文档级智能化审批系统研发时，最新版营业执照通常存在以下图像处理难点：

1.  **红色公章覆盖文本 (Seal Occlusion)：**
    *   *难点：* 登记机关的红色印章往往会盖在“发证日期”或部分文字上方，导致字符粘连。
    *   *解决方案：* 在预处理阶段，利用 HSV 色彩空间进行通道过滤，分离出红色通道（Red Channel Mask），在不损伤底层黑色字迹的前提下滤除印章干扰。
2.  **复杂防伪底纹干扰 (Background Texture Noise)：**
    *   *难点：* 营业执照背景充满细腻的网状和曲线防伪底纹，低端扫描仪或手机拍照时容易产生摩尔纹（Moiré Pattern），严重影响二值化（Binarization）效果。
    *   *解决方案：* 引入基于深度学习的文档去噪网络（如 DocUNet 或 U-Net 变体）进行背景均一化处理。
3.  **多维二维码定位 (QR Code Localization)：**
    *   *难点：* 右侧的二维码包含企业的实时信用状态，是判定执照是否存续的关键。
    *   *解决方案：* 使用 OpenCV 的 `QRCodeDetector` 或 WeChat QR 识别引擎进行 ROI 区域快速锚定与解码。
