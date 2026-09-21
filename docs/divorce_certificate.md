# 💍 民政部统一版式离婚证书文本结构与 OCR 识别规范

---

## 📖 1. 证件基本信息与排版背景 (Document Background)

**离婚证（Divorce Certificate）**是中国境内婚姻登记机关依法颁发给解除婚姻关系当事人的法定凭证。

目前中国国内现行的离婚证采用**中华人民共和国民政部统一监制**的版式。其最大的视觉特征是采用**紫红色烫金封面**（与之相对的结婚证为正红色），内页采用双页对称排版，底纹包含精细的防伪花纹及民政部水印。

### 🔍 基础元数据信息 (Metadata Profiles)
*   **监制机构：** 中华人民共和国民政部 (Ministry of Civil Affairs of the PRC)
*   **现行版本：** 自2004年起启用的全国统一双页版式（紫红色）
*   **技术识别难点：** 烫金字体的反光干扰消除、双页对称排版的文本行切分（Text Line Segmentation）。

---

## 📸 2. 脱敏数据参考与版式标记 (De-identified Layout ROI)

> ⚠️ **数据安全与开源合规声明：** 
> 本页面展示的视觉结构与文本字段已进行 **100% 深度脱敏**。所有关键隐私字段（如双方当事人姓名、身份证件号、离婚证字号、登记机关红章等）均已使用高斯模糊、马赛克覆盖或完全虚拟的测试数据（例如：“张三”、“李四”、“J110105-2026-000000”）代替，绝不包含任何真实自然人的隐私隐私。图片已加盖防伪水印，严禁用于任何实体伪造。

<img width="1057" height="1521" alt="离婚证样本（民政局离婚证官方制作图片）" src="https://github.com/user-attachments/assets/81d7a5ec-a51a-44f0-9b2d-43aa715856f1" />


```text
+-----------------------------------------------------------------------+

|  [左页：持证人信息页]                  | [右页：登记信息页]                    |
|                                       |                                       |
|  离婚证字号: J110105-2026-XXXXXX      | 登记日期: 2026年01月01日               |
|                                       |                                       |
|  持证人: 张三                         | 本证件由民政部监制。                  |
|                                       |                                       |
|  姓  名: 张三                         | 姓  名: 李四                          |
|  性  别: 男                           | 性  别: 女                            |
|  国  籍: 中国                         | 国  籍: 中国                          |
|  出生日期: 1990年01月01日             | 出生日期: 1992年01月01日              |
|  身份证件号: 11010519900101XXXX       | 身份证件号: 11010519920101XXXX        |
|                                       |                                       |
|                                       | 婚姻登记机关: [ 某某区民政局印章 ]      |
+-----------------------------------------------------------------------+
```
*(注：由于扁平化调整，该页面引用的脱敏图片建议统一存放在 `docs/images/divorce_certificate.png`)*

---

## 🗂️ 3. 核心字段元数据解构与 JSON 标签定义 (Field Schemas)

为了方便文档 AI 工程师高效建立实体识别（NER）和表单结构化模型，我们将离婚证解构为以下标准字段标签：

| 序号 | 页面印刷字段 | 推荐标签名 (JSON Key) | 数据类型 (Type) | 结构特征与提取规范 (Extraction Rules) |
| :--- | :--- | :--- | :--- | :--- |
| 1 | **离婚证字号** | `divorce_certificate_no` | String | 具有特定的编码规则，如“J+六位行政区划码+年份+六位流水号”。 |
| 2 | **持证人** | `certificate_holder` | String | 本本证书持有人的姓名（男方或女方）。 |
| 3 | **双方姓名** | `party_a_name` / `party_b_name` | String | 分别提取左页和右页的当事人姓名，用于关联分析。 |
| 4 | **出生日期** | `party_a_dob` / `party_b_dob` | Date | 标准中文格式“XXXX年XX月XX日”，需转化为标准化时间戳。 |
| 5 | **身份证件号** | `party_a_id` / `party_b_id` | String (18位) | 中国公民统一为18位身份证号，OCR提取时需进行校验码校验。 |
| 6 | **登记日期** | `registration_date` | Date (YYYY-MM-DD) | 正式解除婚姻关系的法定生效日期。 |
| 7 | **婚姻登记机关** | `registration_authority` | String | 签发该证件的民政局或婚姻登记处全称。 |

---

## 🌐 4. 婚姻登记涉外翻译高频核心术语表 (Bilingual Glossary)

本术语表严格对接国际公证处、移民局（如 USCIS）及涉外政务标准翻译规范：

*   **离婚证：** Divorce Certificate
*   **中华人民共和国民政部监制：** Supervised by Ministry of Civil Affairs of the People's Republic of China
*   **离婚证字号：** Divorce Certificate Number
*   **持证人：** Certificate Holder
*   **婚姻登记机关：** Marriage Registration Authority
*   **登记日期：** Date of Registration
*   **身份证件号：** Identity Document Number / ID Number
*   **出生日期：** Date of Birth

---

## 🛠️ 5. 计算机视觉 OCR 开发难点与鲁棒性优化 (CV/OCR Technical Insights)

1.  **暗红色纸张与低对比度文本 (Low Contrast Text Detection)：**
    *   *难点：* 离婚证内页背景为粉红/暗红色花纹，且字体为黑色印刷体。在手机拍照光线不足时，文本与背景的对比度极低，导致 OCR 漏检文字行。
    *   *解决方案：* 使用直方图均衡化（Histogram Equalization）或自适应阈值化（Adaptive Thresholding）增强局部图像对比度，或者在深度学习阶段使用多尺度特征融合（FPN）来捕获弱特征文本。
2.  **双页对称布局的文本行错误串行 (Cross-page Text Merging)：**
    *   *难点：* 由于左右两页是对称的表格，普通的 OCR 引擎（如未经过版式分析）很容易把左页的“姓名”和右页的“姓名”连在一起横向读错，导致数据串行。
    *   *解决方案：* 在文本识别前，必须引入基于语义分割（Semantic Segmentation）的版式分析模型（如 **LayoutParser**），优先将页面从中间中缝处切分为“左ROI”和“右ROI”两个独立的文本区域再分别识别。
3.  **封面烫金字体反光干扰 (Golden Foil Reflection)：**
    *   *难点：* 离婚证封面“离婚证”三个字为烫金工艺，手机拍照极易反光成一片白色（白斩），导致字符残缺。
    *   *解决方案：* 引入图像去反光算法（Image De-reflection），或在标注数据时增加翻拍、反光等数据增强（Data Augmentation）样本以提升模型的泛化能力。
