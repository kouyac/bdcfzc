# 🎓 中国高等教育全日制毕业证书文本结构与 OCR 识别规范

---

## 📖 1. 证件基本信息与排版背景 (Document Background)

**高等教育毕业证书（Graduation Certificate / Diploma）**是中国境内高等院校依法颁发给完成学业毕业生的法定学历凭证。

中国高等教育毕业证的版式演变较为独特：自 200化 年代起，毕业证书实行**国家统一编号注册、各高校自主设计排版**的政策。这意味着虽然不同大学（如清华大学、北京大学或地方院校）的证书底纹、色调和校徽位置各有不同，但其包含的**法定元数据字段和核心文本结构是高度统一且标准化**的。

### 🔍 基础元数据信息 (Metadata Profiles)
*   **监制机构：** 中华人民共和国教育部 (Ministry of Education of the PRC)
*   **现行版本：** 各高校自主设计版（通常包含教育部电子注册备案的 18 位证书编号）
*   **核心开发技术：** 毕业生两寸免冠照片的区域遮罩（ROI Masking）、复杂彩色网状底纹下的文本穿透识别。

---

## 📸 2. 脱敏视觉样本与版面网格 (De-identified Layout ROI)

> ⚠️ **数据安全与开源合规声明：** 
> 本页面展示的视觉版式和数据结构已进行 **100% 隐私脱敏处理**。所有关键隐私字段（如持证人姓名、出生日期、学校名称、18位证书编号、校长印章等）均已使用高斯模糊、马赛克覆盖或完全虚拟的测试数据（例如：“张三”、“123456789012345678”）代替，绝不包含任何真实自然人的隐私隐私。图片已加盖“仅供测试”不可逆水印。

<img width="800" height="563" alt="大学毕业证样本（全日制大学毕业证官方制作图片）" src="https://github.com/user-attachments/assets/1ded6ee0-76c8-4c2d-9dca-d58046ca9472" />


```text
+-------------------------------------------------------+

|                                                       |
|                     [ 大学正规校徽 ]                   |
|                       毕 业 证 书                     |
|                                                       |
|  学生 张三 ，性别 男 ，一九九九年一月一日生。          |
|  于二零一八年九月至二零二二年六月在本校               |
|  计算机科学与技术 专业四年制本科学习，修完人才培养    |
|  方案规定的课程，成绩合格，准予毕业。                  |
|                                                       |
|       [ 2寸免冠照片 ]            学校名称: XXXX大学    |
|       [ Photo Mask ]            校(院)长: [ 签名章 ]  |
|                                                       |
|                                 证书编号: 10001XXXXXXXX|
|                                 发证日期: 2022年06月30日|
+-------------------------------------------------------+
```
*(注：在实际文档中，此处建议放置一张经过厚马赛克脱敏、并打上浅灰色贯穿水印的大学毕业证扫描件图片)*

---

## 🗂️ 3. 核心字段元数据解构与标注定义 (JSON Key Schemas)

为了让自然语言处理（NLP）和文档 AI（Document AI）工程师高效建立特征提取模型，我们将毕业证解构为以下标准字段标签：

| 序号 | 页面印刷字段 | 推荐标签名 (JSON Key) | 数据类型 (Type) | 结构特征与提取规范 (Extraction Rules) |
| :--- | :--- | :--- | :--- | :--- |
| 1 | **姓名** | `student_name` | String | 毕业学生法定全称，通常为 2-4 个汉字。 |
| 2 | **性别** | `student_gender` | String | “男” 或 “女”。 |
| 3 | **出生日期** | `date_of_birth` | Date (YYYY-MM-DD) | 证书中通常印刷为中文大写或阿拉伯数字的混合格式（如：一九九九年一月一日）。 |
| 4 | **就读时间段** | `study_period` | Object / Range | 包含入学年份和毕业年份（如“二零一八年九月至二零二二年六月”），需拆解为 `start_date` 和 `end_date`。 |
| 5 | **学校名称** | `institution_name` | String | 颁发证书的高校全称，往往与校章、校徽一致。 |
| 6 | **专业** | `academic_major` | String | 教育部标准专业目录名称（如“计算机科学与技术”、“工商管理”）。 |
| 7 | **学历层次与学制** | `degree_level_length` | String / Int | 明确是“四年制本科”、“三年制专科”等核心权重字段。 |
| 8 | **证书编号** | `certificate_number` | String (18位) | 18位学信网可查的唯一学历注册码，前5位通常为高校代码。 |

---

## 🌐 4. 教育资质涉外翻译高频核心术语表 (Bilingual Glossary)

本术语表完全符合中国涉外公证处、教育部留学服务中心（CSCSE）以及跨国企业 WES 学历认证的标准翻译术语：

*   **毕业证书：** Graduation Certificate
*   **普通高等学校：** Regular Higher Education Institution
*   **全日制高等教育：** Full-time Higher Education
*   **四年制本科：** Four-year Undergraduate Program
*   **修完人才培养方案规定的课程：** Completed all the courses required by the talent training program
*   **成绩合格，准予毕业：** Passed all examinations and is hereby permitted to graduate
*   **校长签章：** Signature of the President
*   **证书编号（学信网注册码）：** Certificate Number / Registration Number
*   **中华人民共和国教育部监制：** Supervised by Ministry of Education of the People's Republic of China

---

## 🛠️ 5. 计算机视觉 OCR 开发难点与鲁棒性优化 (CV/OCR Technical Insights)

1.  **高校自主排版导致的版式多样性 (Layout Diversity)：**
    *   *难点：* 不同学校的“证书编号”和“发证日期”可能出现在左下角、右下角甚至正下方，固定坐标（Anchor Boxes）的方法极易失效。
    *   *解决方案：* 抛弃基于绝对坐标的提取逻辑，全面采用基于预训练多模态模型（如 **LayoutLMv3** 或 **Donut**）的键值对（Key-Value）关联和命名实体识别（NER）。
2.  **两寸彩色照片对文本切分的干扰 (Photo Border Interference)：**
    *   *难点：* 毕业证照片周边往往会加盖学校的钢印或者红章，且照片的边缘极易被 OCR 文本行检测器错误误判为文字边框。
    *   *解决方案：* 引入目标检测网络（如 YOLOv8-obb），优先将照片区域（Photo ROI）作为非文本物体提取并实施特征遮罩（ROI Masking），从而净化文本检测环境。
3.  **复杂手写/艺术体校长签名章识别 (President Signature Parsing)：**
    *   *难点：* 许多大学校长的签章采用的是毛笔书法体或行草艺术字，通用 OCR 字典（Lexicon）极易将其识别为乱码。
    *   *解决方案：* 此类字段无需强行进行字符级识别（Character-level OCR），直接通过签名检测框提取图像特征，将其作为图像哈希（Image Hash）与发证机关进行多因子匹配关联。
