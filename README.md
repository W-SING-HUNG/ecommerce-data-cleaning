# Ecommerce Data Cleaning Project

## Introduction
本项目通过对英国一家在线零售公司的销售数据进行评估和清理，生成高质量数据集，为后续商业分析（如畅销产品挖掘）奠定基础。数据集记录了2010年12月1日至2011年12月9日的交易数据，涵盖礼品类商品的销售情况。本项目为自学数据分析的成果，展示了使用 Python 和 Pandas 进行数据清理的能力，适用于数据科学和商业分析领域。

**项目目标**：评估电商销售数据的整齐度和干净度，执行系统化的数据清理，生成规范、可靠的数据集。

**关键词**：数据清理、Pandas、电商销售、自学项目

## Data Source
- **数据集**：UCI Online Retail 数据集
- **来源**：[UCI Machine Learning Repository](https://archive.ics.uci.edu/ml/datasets/online+retail)
- **许可**：Creative Commons Attribution 4.0 International (CC BY 4.0)
- **描述**：数据集包含541,909条交易记录，涵盖8个字段：
  - `InvoiceNo`：交易编号（6位，以“C”开头表示取消）
  - `StockCode`：产品编号（5位）
  - `Description`：产品名称
  - `Quantity`：交易数量
  - `InvoiceDate`：交易日期和时间
  - `UnitPrice`：单价（英镑）
  - `CustomerID`：客户编号（5位）
  - `Country`：客户所在国家

## Analysis Objectives
本项目的核心目标是：
1. **评估数据质量**：检查数据结构（整齐度）和内容（缺失值、不一致值、无效值）。
2. **清理数据**：通过数据预处理，消除缺失值、不一致数据和无效数据，生成适合分析的数据集。
3. **验证清理效果**：通过关键指标对比，量化清理成果，确保数据质量。

通过此项目，展示自学掌握的数据清理技能，以及对数据质量的系统化理解，为商业决策提供可靠数据支持。

## Analysis Methods
本项目使用 Python 的 Pandas 库，执行以下数据清理流程：

1. **数据导入与初探**：
   - 使用 `pd.read_csv` 加载 `e_commerce.csv`。
   - 通过 `head()` 和 `info()` 查看数据结构和类型。

2. **数据整齐度评估**：
   - 验证数据符合“每行一个观察值、每列一个变量、每个单元格一个值”的标准，确认无结构性问题。

3. **数据干净度评估**：
   - **缺失值**：检查 `Description`（1,454条缺失）和 `CustomerID`（135,080条缺失），分析其影响。
   - **不一致数据**：检查 `Country` 字段中的值（如 "USA" vs. "United States"）。
   - **无效数据**：识别 `Quantity` < 0（10,624条，含取消订单）和 `UnitPrice` < 0（2条，坏账调整）。
   - **数据类型**：确认 `InvoiceDate`（应为 datetime）和 `CustomerID`（应为字符串）的类型问题。

4. **数据清理**：
   - 转换 `InvoiceDate` 为 datetime 类型。
   - 转换 `CustomerID` 为字符串并移除末尾“.0”。
   - 删除 `Description` 缺失的行（因其 `UnitPrice` 为 0，无分析价值）。
   - 统一 `Country` 值（"USA" → "United States"，"UK"/"U.K." → "United Kingdom"）。
   - 删除 `Quantity` < 0 的行（取消订单或无效交易）。
   - 删除 `UnitPrice` < 0 的行（坏账调整）。
   - 保存清理后的数据为 `e_commerce_cleaned.csv`。

5. **验证清理效果**：
   - 使用 `isnull().sum()` 和条件筛选（如 `len(df[df["Quantity"] < 0])`）验证缺失值、不一致值和无效值已消除。
   - 对比清洗前后的关键指标，量化清理效果。

**技术栈**：Python 3.12, Pandas

## Example Code
以下是数据清理的关键代码片段，展示清理流程：

```python
import pandas as pd

# 加载数据
original_data = pd.read_csv("e_commerce.csv")

# 转换数据类型
cleaned_data = original_data.copy()
cleaned_data["InvoiceDate"] = pd.to_datetime(cleaned_data["InvoiceDate"])  # 转换为 datetime
cleaned_data["CustomerID"] = cleaned_data["CustomerID"].astype(str).str.slice(0, -2)  # 转换为字符串并移除“.0”

# 删除缺失值
cleaned_data.dropna(subset=["Description"], inplace=True)  # 删除 Description 为空的行

# 统一 Country 值
cleaned_data["Country"] = cleaned_data["Country"].replace({
    "USA": "United States",
    "UK": "United Kingdom",
    "U.K.": "United Kingdom"
})

# 删除无效数据
cleaned_data = cleaned_data[cleaned_data["Quantity"] >= 0]  # 删除 Quantity < 0 的行
cleaned_data = cleaned_data[cleaned_data["UnitPrice"] >= 0]  # 删除 UnitPrice < 0 的行

# 保存清理后的数据
cleaned_data.to_csv("e_commerce_cleaned.csv", index=False)
```

**代码说明**：上述代码展示了从数据加载到清理的完整流程，包括类型转换、缺失值处理、不一致值统一和无效值删除。

## Project Outcomes
### Data Cleaning Results
- **初始数据规模**：541,909 行
- **清理后数据规模**： 530,691 行
- **清理效果**：
  - 删除了 1,454 条 `Description` 缺失的记录（均 `UnitPrice` = 0）。
  - 统一了 `Country` 值，消除了 "USA"、"UK"、"U.K." 等变体。
  - 删除了 10,624 条 `Quantity` < 0 的记录（取消订单或无效交易）。
  - 删除了 2 条 `UnitPrice` < 0 的记录（坏账调整）。
  - 修正了 `InvoiceDate` 和 `CustomerID` 的数据类型。
- **输出文件**：`e_commerce_cleaned.csv`，可用于后续分析。

### Key Metrics Comparison Before and After Cleaning
| 指标                     | 清洗前            | 清洗后            |
|--------------------------|-------------------|-------------------|
| 总行数                  | 541,909           | 530,691          |
| `Description` 缺失数量   | 1,454 (0.27%)     | 0 (0%)            |
| `CustomerID` 缺失数量    | 135,080 (24.93%)  | 135,080 (25.45%)  |
| `Quantity` < 0 数量      | 10,624 (1.96%)    | 0 (0%)            |
| `UnitPrice` < 0 数量     | 2 (0.0004%)       | 0 (0%)            |
| `Country` 不一致值       | 存在（如 USA, UK）| 统一（如 United States） |

**表格说明**：对比表量化了数据清理的效果，消除了缺失值和无效数据，统一了不一致值，确保数据质量。`CustomerID` 缺失保留，因其不影响产品分析。

### Key Insights
1. **数据质量问题**：
   - `Description` 缺失的记录均无效（`UnitPrice` = 0），可能是错误或测试数据。
   - `Quantity` < 0 的记录多为取消订单（以“C”开头）或无效交易，需剔除以确保销售分析准确性。
   - `Country` 字段的不一致性可能影响地理分析，统一后提高了数据可靠性。
2. **业务意义**：
   - 清理后的数据集为后续分析（如畅销产品、客户行为）提供了可靠基础。
   - 保留 `CustomerID` 缺失的记录，确保了数据完整性。
3. **自学成果**：
   - 展示了自学 Pandas 和数据清理的能力，掌握了缺失值处理、不一致数据统一和无效数据剔除。
   - 体现了系统化的数据分析流程，适合数据科学和商业分析学习。

## Usage Instructions
1. **获取数据**：
   - 从 [UCI Machine Learning Repository](https://archive.ics.uci.edu/ml/datasets/online+retail) 下载数据集（ZIP 文件）。
   - 解压 ZIP 文件，得到 `Online Retail.xlsx`。
   - 将 Excel 文件转换为 CSV 格式，命名为 `e_commerce.csv`。示例代码：
     ```python
     import pandas as pd
     # 读取 Excel 文件
     data = pd.read_excel("Online Retail.xlsx")
     # 保存为 CSV
     data.to_csv("e_commerce.csv", index=False)
     ```
     
2. **环境准备**：
   - 安装 Python 3.12 和 Pandas：`pip install pandas`
   - 安装 openpyxl（用于读取 Excel 文件）：`pip install openpyxl`
   
3. **运行项目**：
   - 确保 `e_commerce.csv` 已准备好（通过上述转换步骤）。
   - 打开 `Ecommerce_Data_Cleaning.ipynb`。
   - 执行笔记本代码，生成清理后的 `e_commerce_cleaned.csv`。

## Project Value
- **学术价值**：展示了数据清理的规范流程，符合数据科学领域的标准实践。
- **应用价值**：为电商数据分析（如畅销产品挖掘）提供了高质量数据基础。
- **个人能力**：体现了自学 Python 和 Pandas 的能力，以及对数据质量的系统化理解，适合数据科学、商业分析等港硕申请。

## License
- **代码**：MIT License
- **数据集**：Creative Commons Attribution 4.0 International (CC BY 4.0)，详情见数据来源。

## Contact Information
如有问题或建议，请通过 1822208056@QQ.COM 联系，或查看我的 [GitHub 主页](https://github.com/W-SING-HUNG)。