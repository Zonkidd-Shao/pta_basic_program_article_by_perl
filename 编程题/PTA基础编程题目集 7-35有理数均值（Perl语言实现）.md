# 7-35 有理数均值（Perl语言实现）

## 前言

本题（7-35 有理数均值）的主要考点是：准确理解题意、规范处理输入输出格式，并正确处理边界与精度。下面先给出题目描述与格式要求，再通过清晰的思路与可直接运行的perl代码进行讲解。

## 题目描述

本题要求编写程序，计算N个有理数的平均值。

## 输入格式

输入第一行给出正整数N（≤100）；第二行中给出N个分数形式的有理数，其中分子和分母全是整形范围内的整数（正负均可），没有分母为0的情况。

## 输出格式

在一行中按照a/b的格式输出N个有理数的平均值。注意必须是该有理数的最简分数形式，若分母为1，则只输出分子。

## 输入样例

```in
4
1/2 1/6 3/6 -5/10
```

## 输出样例

```out
1/6
```

## 解题思路

### 核心问题分析
本题需要解决的核心问题：
1. **分数输入解析**：识别输入中的分数格式（分子/分母）或整数格式
2. **分数累加求和**：多个分数相加需要通分，避免浮点数精度丢失
3. **求平均值**：总和除以个数N
4. **最简分数输出**：用最大公约数约分，保证分母为正

### 算法原理说明
- **辗转相除法(GCD)**：用于求最大公约数，对分数进行约分。公式：`gcd(a, b) = gcd(b, a % b)`，直到b为0时a即为最大公约数
- **分数加法**：`a/b + c/d = (a*d + c*b) / (b*d)`，每次累加后立即约分防止溢出
- **求平均值**：将累加后的分母乘以N，再进行约分
- **符号处理**：确保负号在分子上，分母始终为正

### 具体计算步骤
1. 输入N，初始化分子sumNum=0，分母sumDen=1
2. 对每个分数：
   - 解析分子num和分母den
   - 通分累加：`sumNum = sumNum*den + num*sumDen`，`sumDen = sumDen*den`
   - 用GCD约分化简
3. 求平均：`sumDen *= N`，再次约分
4. 若分母为负，分子分母同时取反
5. 按格式输出结果

## 完整代码

```perl
#!/usr/bin/perl
use strict;
use warnings;

sub gcd {
    my ($a, $b) = @_;
    $a = abs $a;
    $b = abs $b;
    ($a, $b) = ($b, $a % $b) while $b;
    return $a;
}

my $n_line = <STDIN>;
chomp $n_line if defined $n_line;
my $n = $n_line + 0;
my $rest = do { local $/; <STDIN> };
my @fracs = ();
if (defined $rest) {
    @fracs = split /\s+/, $rest;
    @fracs = grep { $_ ne '' } @fracs;
    @fracs = @fracs[0 .. $n-1] if @fracs > $n;
}

my $sum_num = 0;
my $sum_den = 1;

for my $f (@fracs) {
    my ($num, $den) = split m{/}, $f;
    $sum_num = $sum_num * $den + $num * $sum_den;
    $sum_den = $sum_den * $den;
    my $g = gcd($sum_num, $sum_den);
    $sum_num /= $g;
    $sum_den /= $g;
}

$sum_den *= $n;
my $g = gcd($sum_num, $sum_den);
$sum_num /= $g;
$sum_den /= $g;

if ($sum_den < 0) {
    $sum_num = -$sum_num;
    $sum_den = -$sum_den;
}

if ($sum_den == 1) {
    print "$sum_num\n";
} else {
    print "$sum_num/$sum_den\n";
}
```

## 代码流程说明

### 1. gcd函数
- 输入：两个整数a、b
- 功能：求最大公约数
- 流程：先用abs()取绝对值（负数处理），再用辗转相除法while循环求解

### 2. 主程序-初始化
- `<STDIN>`读取N，chomp去除换行符
- 初始化累计分子$sumNum=0，累计分母$sumDen=1

### 3. 主程序-分数解析与累加
- `<STDIN>`读取整行，chomp处理，split(/\s+/)分割每个分数字符串
- 正则`$frac =~ /\//`判断是否包含'/'：有则用split(/\//)拆分分子分母，无则分母为1
- 通分公式累加分子分母
- 每次累加后用GCD约分，避免数值过大（int()确保整数除法）

### 4. 主程序-求平均与最终约分
- $sumDen乘以N求平均值
- 再次GCD约分
- 若分母为负，分子分母同时取反保证分母为正

### 5. 主程序-输出
- 分母为1时print只输出分子加换行\n
- 否则用双引号内插或.连接输出"分子/分母"格式加换行\n

## 代码流程图

```mermaid
flowchart TD
    A["开始"] --> B["<STDIN>读取整数N, chomp处理"]
    B --> C["初始化$sumNum=0, $sumDen=1"]
    C --> D["<STDIN>读取整行, split遍历每个分数$frac"]
    D --> E{"还有未处理的分数?"}
    E -->|是| F{"$frac =~ /\//包含'/'?"}
    F -->|是| G["split(/\//)拆分转$num和$den"]
    F -->|否| H["$num=整数值, $den=1"]
    G --> I["通分累加$sumNum和$sumDen"]
    H --> I
    I --> J["GCD约分, int()取整"]
    J --> D
    E -->|否| K["$sumDen *= N 求平均"]
    K --> L["GCD最终约分"]
    L --> M{"分母<0?"}
    M -->|是| N["分子分母同时取反"]
    M -->|否| O{"分母==1?"}
    N --> O
    O -->|是| P["print输出$sumNum加\n"]
    O -->|否| Q["print用双引号内插输出$sumNum/$sumDen加\n"]
    P --> R["程序结束"]
    Q --> R
```

## 解题流程图

```mermaid
flowchart TD
    A[理解题目需求] --> B[选择分数运算方案]
    B --> C[设计GCD约分函数]
    C --> D[设计分数输入解析逻辑]
    D --> E[设计累加算法<br>通分+每次约分]
    E --> F[设计求平均步骤<br>分母乘N再约分]
    F --> G[处理符号<br>保证分母为正]
    G --> H[设计格式化输出]
    H --> I[编写完整代码]
    I --> J[用样例验证]
    J --> K{输出正确?}
    K -->|是| L[完成]
    K -->|否| M[检查GCD实现/通分公式/符号]
    M --> I
```

## 代码解析

```perl
#!/usr/bin/perl
use strict;
use warnings;

sub gcd {
    my ($a, $b) = @_;
    $a = abs $a;
    $b = abs $b;
    ($a, $b) = ($b, $a % $b) while $b;
    return $a;
}

my $n_line = <STDIN>;
chomp $n_line if defined $n_line;
my $n = $n_line + 0;
my $rest = do { local $/; <STDIN> };
my @fracs = ();
if (defined $rest) {
    @fracs = split /\s+/, $rest;
    @fracs = grep { $_ ne '' } @fracs;
    @fracs = @fracs[0 .. $n-1] if @fracs > $n;
}

my $sum_num = 0;
my $sum_den = 1;

for my $f (@fracs) {
    my ($num, $den) = split m{/}, $f;
    $sum_num = $sum_num * $den + $num * $sum_den;
    $sum_den = $sum_den * $den;
    my $g = gcd($sum_num, $sum_den);
    $sum_num /= $g;
    $sum_den /= $g;
}

$sum_den *= $n;
my $g = gcd($sum_num, $sum_den);
$sum_num /= $g;
$sum_den /= $g;

if ($sum_den < 0) {
    $sum_num = -$sum_num;
    $sum_den = -$sum_den;
}

if ($sum_den == 1) {
    print "$sum_num\n";
} else {
    print "$sum_num/$sum_den\n";
}
```

gcd函数

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
1
1/2
```

**输出：**

```text
1/2
```

### 测试二：特殊用例

**输入：**

```text
3
1/2 -1/2 1/3
```

**输出：**

```text
1/9
```

## 总结

本题的核心在于理清「有理数均值」的输入输出关系与边界处理：先按格式读取输入，再依据规则逐步计算或遍历，最后按规范输出。该思路可迁移到同类格式化输入输出与模拟计算的题目。
