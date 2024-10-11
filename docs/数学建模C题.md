### 1. **决策变量**：

$$
x_{i,j,t} = 
\begin{cases} 
1 & \text{如果地块 } i \text{ 在第 } t \text{ 季种植作物 } j, \\
0 & \text{否则}.
\end{cases}
$$

### 2. **目标函数：利润最大化**：
最大化总利润：
$$
\text{Maximize } Z = \sum_{i=1}^{N} \sum_{j=1}^{M} \sum_{t=1}^{2} \left( p_j \cdot r_j \cdot A_i \cdot x_{i,j,t} - c_{i,j} \cdot A_i \cdot x_{i,j,t} \right)
$$
- 

### 3. **约束条件**：

#### 3.1 地块类型约束：
根据不同类型的地块，施加不同的种植约束。

1. **平旱地、梯田、山坡只能种一季粮食作物**：
对这些地块类型 $T_i \in \{\text{平旱地, 梯田, 山坡}\}$，只能种一季粮食：
$$
\sum_{j \in S_{\text{grain}}} x_{i,j,1} = 1 \quad \forall i \text{ such that } T_i \in \{\text{平旱地, 梯田, 山坡}\}
$$
且不能种第二季：
$$
\sum_{j=1}^{M} x_{i,j,2} = 0 \quad \forall i \text{ such that } T_i \in \{\text{平旱地, 梯田, 山坡}\}
$$

2. **水浇地只能种一季水稻或两季蔬菜**：
对于水浇地 $T_i = \text{水浇地}$，可以种一季水稻或两季蔬菜：
$$
\sum_{j \in S_{\text{rice}}} x_{i,j,1} + \sum_{j \in S_{\text{vegetable1}}} x_{i,j,1} = 1 \quad \forall i \text{ such that } T_i = \text{水浇地}
$$
且第二季只能种特定蔬菜：
$$
\sum_{j \in S_{\text{vegetable2}}} x_{i,j,2} \leq 1 \quad \forall i \text{ such that } T_i = \text{水浇地}
$$

3. **普通大棚必须种一季蔬菜和一季食用菌**：
对于普通大棚 $T_i = \text{普通大棚}$，必须种一季蔬菜和一季食用菌：
$$
\sum_{j \in S_{\text{vegetable}}} x_{i,j,1} = 1 \quad \forall i \text{ such that } T_i = \text{普通大棚}
$$
$$
\sum_{j \in S_{\text{fungi}}} x_{i,j,2} = 1 \quad \forall i \text{ such that } T_i = \text{普通大棚}
$$

4. **智慧大棚必须种两季蔬菜**：
对于智慧大棚 $T_i = \text{智慧大棚}$，每年必须种两季蔬菜：
$$
\sum_{j \in S_{\text{vegetable}}} x_{i,j,1} = 1 \quad \forall i \text{ such that } T_i = \text{智慧大棚}
$$
$$
\sum_{j \in S_{\text{vegetable}}} x_{i,j,2} = 1 \quad \forall i \text{ such that } T_i = \text{智慧大棚}
$$

#### 3.2 豆类轮作约束：
每块地至少每三年种一次豆类作物。假设豆类作物集合为 $S_{\text{bean}}$：
$$
\sum_{j \in S_{\text{bean}}} \left( x_{i,j,1} + x_{i,j,2} \right) \geq 1 \quad \forall i
$$

#### 3.3 作物不分散约束（种植面积限制）：
每种作物 $j$ 的种植总面积不能小于最小阈值 $A_{\text{min},j}$：
$$
\sum_{i=1}^{N} \sum_{t=1}^{2} A_i \cdot x_{i,j,t} \geq A_{\text{min},j} \quad \forall j
$$

#### 3.4 非负和二进制变量约束：
决策变量 $x_{i,j,t}$ 为二进制变量，表示是否种植作物：
$$
x_{i,j,t} \in \{0, 1\} \quad \forall i, \forall j, \forall t
$$

### 完整的数学模型总结：

目标函数：
$$
\text{Maximize } Z = \sum_{i=1}^{N} \sum_{j=1}^{M} \sum_{t=1}^{2} \left( p_j \cdot r_j \cdot A_i \cdot x_{i,j,t} - c_{i,j} \cdot A_i \cdot x_{i,j,t} \right)
$$

约束条件：
1. **平旱地、梯田、山坡种植限制**：
$$
\sum_{j \in S_{\text{grain}}} x_{i,j,1} = 1 \quad \forall i \text{ such that } T_i \in \{\text{平旱地, 梯田, 山坡}\}
$$
$$
\sum_{j=1}^{M} x_{i,j,2} = 0 \quad \forall i \text{ such that } T_i \in \{\text{平旱地, 梯田, 山坡}\}
$$

2. **水浇地种植限制**：
$$
\sum_{j \in S_{\text{rice}}} x_{i,j,1} + \sum_{j \in S_{\text{vegetable1}}} x_{i,j,1} = 1
$$
$$
\sum_{j \in S_{\text{vegetable2}}} x_{i,j,2} \leq 1
$$

3. **普通大棚种植限制**：
$$
\sum_{j \in S_{\text{vegetable}}} x_{i,j,1} = 1
$$
$$
\sum_{j \in S_{\text{fungi}}} x_{i,j,2} = 1
$$

4. **智慧大棚种植限制**：
$$
\sum_{j \in S_{\text{vegetable}}} x_{i,j,1} = 1
$$
$$
\sum_{j \in S_{\text{vegetable}}} x_{i,j,2} = 1
$$

5. **豆类轮作约束**：
$$
\sum_{j \in S_{\text{bean}}} \left( x_{i,j,1} + x_{i,j,2} \right) \geq 1
$$

6. **种植面积限制**：
$$
\sum_{i=1}^{N} \sum_{t=1}^{2} A_i \cdot x_{i,j,t} \geq A_{\text{min},j}
$$

7. **二进制约束**：

$$
x_{i,j,t} \in \{0, 1\} \quad \forall i, \forall j, \forall tff
$$
