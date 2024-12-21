
# 假设检验和错误检测率推导

## 假设检验定义
**假设检验**是一种统计推断方法，用于通过样本数据验证关于总体的假设是否成立。  
它的目标是判断一个统计假设是否可以根据样本数据被接受或拒绝。

### 核心步骤
1. 提出假设：
    - 原假设 $H_0$：通常是无效假设。
    - 备择假设 $H_1$：表示有某种效应存在。

2. 选择统计量：
    - 基于样本数据构建的检验函数，用于衡量样本与原假设的偏离程度。

3. 决定显著性水平：
    - 通常取 $\alpha = 0.05$ 或 $0.01$，表示可接受的错误拒绝概率。

4. 计算统计量并判断显著性：
    - 根据计算出的统计量值和临界值比较，决定是否拒绝 $H_0$。

---

## 检测错误率推导

在隐蔽通信中，检测错误率 $\xi$ 是监听者（威利，Willie）对是否存在隐蔽通信的判断错误的概率。它由以下两部分组成：
$$\[
\xi = P_{\text{FA}} + P_{\text{MD}}
\]$$
- $P_{\text{FA}}$：虚警概率，即在 $H_0$ 假设下错误地判断为 $H_1$。
- $P_{\text{MD}}$：漏检概率，即在 $H_1$ 假设下错误地判断为 $H_0$。

### 1. 威利的信号模型
监听者接收到的信号可以建模为两种假设下的表现：
- 当 $H_0$ 为真（无隐蔽通信）：
$$\[
y_W[i] = P_R^J h_{RW} V[i] + n_W[i]
\]$$
- 当 $H_1$ 为真（有隐蔽通信）：
$$\[
y_W[i] = P_S h_{SW} X[i] + P_R^J h_{RW} V[i] + n_W[i]
\]
$$
其中：
- $P_S$：隐蔽信号的功率，$P_R^J$：中继发送的干扰信号功率。
- $h_{SW}, h_{RW}$：信道增益。
- $X[i], V[i]$：隐蔽信号与干扰信号。
- $n_W[i]$：噪声，服从 $n_W[i] \sim \mathcal{CN}(0, \sigma_W^2)$。

### 2. 检测规则与统计量
监听者使用统计量 $T$ 来判断是否存在隐蔽通信：
$$\[
T = \frac{1}{n} \sum_{i=1}^n |y_W[i]|^2
\]$$
检测规则：
- 如果 $T > \tau$，判断为 $H_1$（存在隐蔽通信）。
- 如果 $T \leq \tau$，判断为 $H_0$（无隐蔽通信）。

---

## 最优检测门限推导

### 1. 检测错误率的表达式
虚警概率和漏检概率分别为：
$$\[
P_{\text{FA}} = 1 - \Phi\left(\frac{\tau - \mu_0}{\sigma_0}\right)
\]$$


$$\[
P_{\text{MD}} = \Phi\left(\frac{\tau - \mu_1}{\sigma_1}\right)
\]$$
总检测错误率为：
$$\[
\xi(\tau) = P_{\text{FA}} + P_{\text{MD}} = 1 - \Phi\left(\frac{\tau - \mu_0}{\sigma_0}\right) + \Phi\left(\frac{\tau - \mu_1}{\sigma_1}\right)
\]$$

### 2. 最优门限的求解
为了最小化检测错误率，对 $\xi(\tau)$ 关于 $\tau$ 求导，并令其为 $0$：
$$\[
\frac{\partial \xi}{\partial \tau} = -\frac{\partial \Phi\left(\frac{\tau - \mu_0}{\sigma_0}\right)}{\partial \tau} + \frac{\partial \Phi\left(\frac{\tau - \mu_1}{\sigma_1}\right)}{\partial \tau} = 0
\]$$
得到：
$$\[
-\frac{1}{\sigma_0} f_Z\left(\frac{\tau - \mu_0}{\sigma_0}\right) + \frac{1}{\sigma_1} f_Z\left(\frac{\tau - \mu_1}{\sigma_1}\right) = 0
\]$$

整理后，最优检测门限为：
$$\[
\tau^* = \frac{\mu_0 \sigma_1^2 - \mu_1 \sigma_0^2 + \sigma_0 \sigma_1 \ln(\sigma_1 / \sigma_0)}{\sigma_1^2 - \sigma_0^2}
\]$$

---

## 最小检测错误率计算

将最优门限 $\tau^*$ 代入检测错误率公式：
$$\[
\xi^* = 1 - \Phi\left(\frac{\tau^* - \mu_0}{\sigma_0}\right) + \Phi\left(\frac{\tau^* - \mu_1}{\sigma_1}\right)
\]$$

### 示例
假设：
- $H_0$：$\mu_0 = 1$，$\sigma_0 = 0.5$
- $H_1$：$\mu_1 = 2$，$\sigma_1 = 0.6$

计算：
1. **最优门限 $\tau^*$**
$$\[
\tau^* = \frac{1 \cdot 0.6^2 - 2 \cdot 0.5^2 + 0.5 \cdot 0.6 \ln(0.6 / 0.5)}{0.6^2 - 0.5^2} \approx 1.45
\]$$

2. **最小错误检测率 $\xi^*$**
$$\[
\xi^* = 1 - \Phi\left(\frac{1.45 - 1}{0.5}\right) + \Phi\left(\frac{1.45 - 2}{0.6}\right)
\]$$
通过标准正态分布表，计算得：
$$\[
\xi^* \approx 0.36
\]$$

即最小错误检测率约为 $36\%$。

---

## 总结

1. 最优检测门限 $\tau^*$ 平衡了虚警概率和漏检概率，使检测错误率最小化。
2. 错误检测率的最小值取决于信号均值差异 $|\mu_1 - \mu_0|$ 和标准差 $\sigma_0, \sigma_1$。
3. 本文方法提供了隐蔽通信中优化检测性能的重要工具。