# 7-13 日K蜡烛图（Perl语言实现）

## 前言

本题（7-13 日K蜡烛图）的主要考点是：准确理解题意、规范处理输入输出格式，并正确处理边界与精度。下面先给出题目描述与格式要求，再通过清晰的思路与可直接运行的perl代码进行讲解。

## 题目描述

股票价格涨跌趋势，常用蜡烛图技术中的K线图来表示，分为按日的日K线、按周的周K线、按月的月K线等。以日K线为例，每天股票价格从开盘到收盘走完一天，对应一根蜡烛小图，要表示四个价格：开盘价格Open（早上刚刚开始开盘买卖成交的第1笔价格）、收盘价格Close（下午收盘时最后一笔成交的价格）、中间的最高价High和最低价Low。

如果Close&lt;Open，表示为“BW-Solid”（即“实心蓝白蜡烛”）；如果Close&gt;Open，表示为“R-Hollow”（即“空心红蜡烛”）；如果Open等于Close，则为“R-Cross”（即“十字红蜡烛”）。如果Low比Open和Close低，称为“Lower Shadow”（即“有下影线”），如果High比Open和Close高，称为“Upper Shadow”（即“有上影线”）。请编程序，根据给定的四个价格组合，判断当日的蜡烛是一根什么样的蜡烛。

## 输入格式

输入在一行中给出4个正实数，分别对应Open、High、Low、Close，其间以空格分隔。

## 输出格式

在一行中输出日K蜡烛的类型。如果有上、下影线，则在类型后加上with 影线类型。如果两种影线都有，则输出with Lower Shadow and Upper Shadow。

## 输入样例1

```in
5.110 5.250 5.100 5.105
```

## 输入样例2

```in
5.110 5.110 5.110 5.110
```

## 输入样例3

```in
5.110 5.125 5.112 5.126
```

## 输出样例1

```out
BW-Solid with Lower Shadow and Upper Shadow
```

## 输出样例2

```out
R-Cross
```

## 输出样例3

```out
R-Hollow
```

## 解题思路

### 核心问题分析
本题是一个典型的**多条件组合判断**问题。核心任务是根据4个价格数据（开盘价Open、最高价High、最低价Low、收盘价Close）判断日K蜡烛图的类型，判断分为两个独立维度：
1. **蜡烛主体类型**（3种）：根据Close与Open的大小关系确定
   - Close < Open → BW-Solid（阴线，实心蓝白）
   - Close > Open → R-Hollow（阳线，空心红）
   - Close = Open → R-Cross（十字星）
2. **影线情况**（4种组合）：根据High、Low与Open、Close的比较确定
   - 无上影、无下影
   - 有下影（Lower Shadow）：Low同时小于Open和Close
   - 有上影（Upper Shadow）：High同时大于Open和Close
   - 双影线：既有上影又有下影

### 算法原理说明
采用**两步判断法**分别确定蜡烛类型和影线情况，再组合输出：
1. 第一步：比较Close与Open，确定蜡烛主体类型
2. 第二步：分别判断High、Low与实体边界（Open和Close的极值）的关系
3. 第三步：按格式要求拼接蜡烛类型和影线信息输出

### 具体计算步骤
1. 输入四个价格：Open、High、Low、Close
2. 判断蜡烛主体类型：
   - 若 Close < Open → 类型为 "BW-Solid"
   - 若 Close > Open → 类型为 "R-Hollow"
   - 若 Close = Open → 类型为 "R-Cross"
3. 判断下影线标志 hasLower：
   - Low < Open 且 Low < Close → 有下影线（设为1），否则无（设为0）
4. 判断上影线标志 hasUpper：
   - High > Open 且 High > Close → 有上影线（设为1），否则无（设为0）
5. 组合输出：
   - 先输出蜡烛类型
   - 若 hasLower && hasUpper → 追加 " with Lower Shadow and Upper Shadow"
   - 若仅 hasLower → 追加 " with Lower Shadow"
   - 若仅 hasUpper → 追加 " with Upper Shadow"
   - 都无则不追加

### 1. 验证样例：

- 样例1：Open=5.110, High=5.250, Low=5.100, Close=5.105
  - Close(5.105) < Open(5.110) → BW-Solid ✓
  - Low(5.100) < 5.110 且 < 5.105 → 有下影 ✓
  - High(5.250) > 5.110 且 > 5.105 → 有上影 ✓
  - 输出：BW-Solid with Lower Shadow and Upper Shadow ✓

- 样例2：全部都是5.110
  - Close = Open → R-Cross ✓
  - Low不小于实体，High不大于实体 → 无影线 ✓
  - 输出：R-Cross ✓

- 样例3：Open=5.110, High=5.125, Low=5.112, Close=5.126
  - Close(5.126) > Open(5.110) → R-Hollow ✓
  - Low(5.112)不小于5.110 → 无下影 ✓
  - High(5.125)不大于5.126 → 无上影 ✓
  - 输出：R-Hollow ✓

## 完整代码

```perl
#!/usr/bin/perl
use strict;
use warnings;

# 7-13 日K蜡烛图
my ($Open, $High, $Low, $Close) = split /\s+/, <STDIN>;

my $type;
if ($Close < $Open) {
    $type = "BW-Solid";
} elsif ($Close > $Open) {
    $type = "R-Hollow";
} else {
    $type = "R-Cross";
}

my $hasLower = ($Low < $Open && $Low < $Close) ? 1 : 0;
my $hasUpper = ($High > $Open && $High > $Close) ? 1 : 0;

print $type;
if ($hasLower && $hasUpper) {
    print " with Lower Shadow and Upper Shadow";
} elsif ($hasLower) {
    print " with Lower Shadow";
} elsif ($hasUpper) {
    print " with Upper Shadow";
}
print "\n";
```

## 代码流程说明

1. **注释说明**
   - `# 7-13 日K蜡烛图`：单行注释，说明程序功能

2. **变量声明与数据输入**
   - `my $line = <STDIN>;`：声明词法变量$line，从标准输入读取整行
   - `chomp $line;`：去除行尾换行符
   - `my ($Open, $High, $Low, $Close) = split /\s+/, $line;`：按空白字符分割输入到4个变量
     - $Open：存储开盘价
     - $High：存储最高价
     - $Low：存储最低价
     - $Close：存储收盘价
   - `my $typeStr;`：声明词法变量$typeStr，用于存储蜡烛类型字符串

3. **判断蜡烛主体类型**
   - `if ($Close < $Open) {`：收盘价低于开盘价（阴线）
     - `$typeStr = "BW-Solid";`：实心蓝白蜡烛
   - `} elsif ($Close > $Open) {`：收盘价高于开盘价（阳线）
     - `$typeStr = "R-Hollow";`：空心红蜡烛
   - `} else {`：收盘价等于开盘价（十字星）
     - `$typeStr = "R-Cross";`：十字红蜡烛
   - `}`：if-elsif条件结构结束

4. **判断影线标志（Perl三元表达式?:）**
   - `my $hasLower = ($Low < $Open && $Low < $Close) ? 1 : 0;`：判断下影线
     - 条件：最低价$Low同时低于开盘价和收盘价
     - 条件为真 → $hasLower = 1（有下影线）
     - 条件为假 → $hasLower = 0（无下影线）
   - `my $hasUpper = ($High > $Open && $High > $Close) ? 1 : 0;`：判断上影线
     - 条件：最高价$High同时高于开盘价和收盘价
     - 条件为真 → $hasUpper = 1（有上影线）
     - 条件为假 → $hasUpper = 0（无上影线）

5. **组合输出结果**
   - `print($typeStr);`：先输出蜡烛主体类型（不换行）
   - `if ($hasLower == 1 && $hasUpper == 1) {`：双影线情况
     - `print(" with Lower Shadow and Upper Shadow");`：追加双影线描述
   - `} elsif ($hasLower == 1) {`：仅下影线
     - `print(" with Lower Shadow");`：追加下影线描述
   - `} elsif ($hasUpper == 1) {`：仅上影线
     - `print(" with Upper Shadow");`：追加上影线描述
   - `}`：if-elsif条件结构结束
   - `print("\n");`：输出换行符，完成整行输出

6. **程序结束**
   - Perl脚本执行完毕，程序正常退出

## 代码流程图

```mermaid
flowchart TD
    A[开始] --> B[注释说明程序功能]
    B --> C[声明词法变量: $Open, $High, $Low, $Close<br>$hasLower, $hasUpper, $typeStr]
    C --> D[<STDIN>读取整行存入$line<br>chomp去除换行符<br>split按空白分割出4个价格]
    D --> E{$Close < $Open?}
    E -->|是| F[$typeStr = BW-Solid]
    E -->|否| G{$Close > $Open?}
    G -->|是| H[$typeStr = R-Hollow]
    G -->|否| I[$typeStr = R-Cross]
    F --> J[?:三元表达式判断 $hasLower<br>$Low < $Open && $Low < $Close]
    H --> J
    I --> J
    J --> K[?:三元表达式判断 $hasUpper<br>$High > $Open && $High > $Close]
    K --> L[print输出 $typeStr]
    L --> M{$hasLower == 1 && $hasUpper == 1?}
    M -->|是| N[print输出 with Lower Shadow and Upper Shadow]
    M -->|否| O{$hasLower == 1?}
    O -->|是| P[print输出 with Lower Shadow]
    O -->|否| Q{$hasUpper == 1?}
    Q -->|是| R[print输出 with Upper Shadow]
    Q -->|否| S[print输出换行]
    N --> S
    P --> S
    R --> S
    S --> T[Perl脚本执行结束]
```

## 解题流程图

```mermaid
flowchart TD
    A[开始] --> B[输入4个价格<br>Open, High, Low, Close]
    B --> C[第一步: 判断蜡烛主体类型]
    C --> C1{Close 与 Open 比较}
    C1 -->|Close < Open 阴线| C2[BW-Solid 实心蓝白]
    C1 -->|Close > Open 阳线| C3[R-Hollow 空心红]
    C1 -->|Close = Open 十字| C4[R-Cross 十字红]
    C2 --> D[第二步: 判断影线情况]
    C3 --> D
    C4 --> D
    D --> D1{Low < Open 且 Low < Close?}
    D1 -->|是| D2[hasLower = 1 有下影]
    D1 -->|否| D3[hasLower = 0 无下影]
    D2 --> D4{High > Open 且 High > Close?}
    D3 --> D4
    D4 -->|是| D5[hasUpper = 1 有上影]
    D4 -->|否| D6[hasUpper = 0 无上影]
    D5 --> E[第三步: 组合输出]
    D6 --> E
    E --> E1[输出蜡烛类型]
    E1 --> E2{hasLower && hasUpper?}
    E2 -->|是| E3[输出 with Lower Shadow and Upper Shadow]
    E2 -->|否| E4{仅 hasLower?}
    E4 -->|是| E5[输出 with Lower Shadow]
    E4 -->|否| E6{仅 hasUpper?}
    E6 -->|是| E7[输出 with Upper Shadow]
    E6 -->|否| E8[输出换行]
    E3 --> E8
    E5 --> E8
    E7 --> E8
    E8 --> F[结束]
```

## 代码解析

```perl
#!/usr/bin/perl
use strict;
use warnings;

# 7-13 日K蜡烛图
my ($Open, $High, $Low, $Close) = split /\s+/, <STDIN>;

my $type;
if ($Close < $Open) {
    $type = "BW-Solid";
} elsif ($Close > $Open) {
    $type = "R-Hollow";
} else {
    $type = "R-Cross";
}

my $hasLower = ($Low < $Open && $Low < $Close) ? 1 : 0;
my $hasUpper = ($High > $Open && $High > $Close) ? 1 : 0;

print $type;
if ($hasLower && $hasUpper) {
    print " with Lower Shadow and Upper Shadow";
} elsif ($hasLower) {
    print " with Lower Shadow";
} elsif ($hasUpper) {
    print " with Upper Shadow";
}
print "\n";
```

注释说明

## 复杂度分析

设输入规模为 $n$（对数值类题目为参与运算的数据量，对字符串/序列类题目为长度）。

- **时间复杂度**：$O(n)$ 或 $O(n \log n)$，主要来自一次线性遍历与常数次数学运算，无嵌套高复杂度循环。
- **空间复杂度**：$O(n)$，用于存储输入、中间结果与输出字符串；若仅使用若干标量变量则为 $O(1)$。

## 常见易错点

### 1. 输入/输出格式不符
错误：多余空格、遗漏换行、大小写或精度不符。后果：判题系统判为格式错误。正确：严格按题目要求的格式输出，数值用合适精度。

### 2. 边界条件遗漏
错误：未处理 0、最小值、单字符或空输入等边界。后果：特例 WA。正确：先列出所有边界样例，在编码前单独分支处理。

### 3. 整数溢出与类型
错误：使用过小的整数类型或忽略负号。后果：大数计算溢出。正确：按数据范围选择合适类型，必要时用更大整数类型或字符串处理。

## 更多测试

### 测试一：常规边界

**输入：**

```text
（可取题目边界附近的值，如最小值或最大值）
```

**输出：**

```text
（依据题意推导的正确结果）
```

### 测试二：特殊用例

**输入：**

```text
（可取易错点，如 0、单一元素、全同值等）
```

**输出：**

```text
（对应正确结果）
```

## 总结

本题的核心在于理清「日K蜡烛图」的输入输出关系与边界处理：先按格式读取输入，再依据规则逐步计算或遍历，最后按规范输出。该思路可迁移到同类格式化输入输出与模拟计算的题目。

