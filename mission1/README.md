# 任务一：迭代法解稳态热传导 Ax=b

本项目是课程《数学思维实践》（CST4822A）CDIO 二级项目之任务一，用迭代法
求解稳态热传导问题，对比 **Jacobi**、**Gauss-Seidel** 与**直接解**三种方法。

物理场景：一块铁板（或一根铁棍），边界温度固定，求达到热平衡后内部各点的
稳态温度分布。数学上即求解线性方程组 **Ax = b**，其中 A 来自五点差分（二维）
或三点差分（一维）离散化。

---

## 一、依赖与运行

本项目用 [uv](https://docs.astral.sh/uv/) 管理依赖（仓库根的 `pyproject.toml`
与 `uv.lock`），依赖项为 `numpy` / `matplotlib` / `pillow`。

> 请使用 `uv run`，不要直接用裸 `python`——后者找不到 numpy。

```bash
# 1. 安装依赖（在仓库根目录执行）
uv sync

# 2. 运行任务一（生成全部图表 + 终端报告）
uv run python mission1/heat.py

# 3.（可选）跑单元测试
uv run python mission1/test_heat.py
```

---

## 二、代码结构

单文件函数化（`mission1/heat.py`），按「数学模型层 → 计算实现层 → 可视化验证层」
三层组织，对应报告的三个小节：

| 层 | 函数 |
|---|---|
| 数学模型层 | `build_1d`（铁棍 n 内部点，三对角）/ `build_2d`（铁板 n×n，五点差分） |
| 计算实现层 | `solve_direct`（numpy.linalg.solve，裁判）/ `jacobi` / `gauss_seidel`<br>`spectral_radius_jacobi`（=cos(π/(n+1))）/ `spectral_radius_gs`（=ρ_J²） |
| 可视化验证层 | 6 张静态图 `plot_*` + 3 张 GIF `make_iter_gif` / `make_direct_gif`<br>`main()` 端到端串联 |

---

## 三、输出清单（6 PNG + 3 GIF，共 9 个文件）

运行后写入 `mission1/output/`：

### 静态图（6 张）

| 文件 | 展示内容 |
|---|---|
| `fig1_rod.png` | **铁棍稳态温度**：n=5 内部点直接解 vs 解析解 `T=100·(1−k/6)` |
| `fig2_matrix.png` | **铁板 2×2 系数矩阵结构**：左图 spy 展示 4×4 稀疏矩阵；右图 4 个内部点稳态温度柱状图 |
| `fig3_heatmap.png` | **铁板 30×30 温度场热力图**：迭代解 vs 直接解对比（顶边红 100°C，其余三边蓝 0°C） |
| `fig4_error.png` | **迭代误差曲线（半对数）**：Jacobi 与 Gauss-Seidel 步差 ‖x_新−x_旧‖∞，GS 位于 Jacobi 之下 |
| `fig5_bars.png` | **三方法对比**：收敛轮数 / 收敛误差（symlog 轴，三方法柱子均可见）/ 耗时 |
| `fig6_diverge.png` | **不收敛反例**：A=[[1,2],[2,1]]（非对角占优）Jacobi 步差发散曲线 |

### 动画（3 张 GIF）

| 文件 | 展示内容 |
|---|---|
| `jacobi.gif` | Jacobi 迭代过程：热量从顶边逐渐下渗至稳态（每 50 轮一帧） |
| `gauss_seidel.gif` | Gauss-Seidel 迭代过程：收敛更快（帧数约为 Jacobi 一半） |
| `direct.gif` | 直接解：两帧（初值全 0 → 真解）一帧到位 |

---

## 四、可复现说明

- 停止条件：步差 `‖x_新−x_旧‖∞ < 1e-6` 或达 10000 轮上限
- 初值：`x0 = np.zeros(n)`
- 主算例：铁板 30×30，顶边 100°C，其余三边 0°C
- 预期：Jacobi ≈2381 轮 / Gauss-Seidel ≈1252 轮（GS 约为 Jacobi 的一半多）；
  ρ_J=cos(π/31)≈0.9949、ρ_GS≈0.9898；Jacobi 真误差≈1.9e-4、GS≈9.6e-5
- 随机性：无（确定性算法，多次运行结果完全一致）

---

## 五、AI 工具使用声明

本仓库代码由 **Claude Code**（Anthropic 的官方 CLI）协助生成，包括：

- 算法实现（Jacobi / Gauss-Seidel / 直接解）
- 可视化代码（6 张静态图 + 3 张 GIF）
- 项目骨架与文档结构

数学推导、物理建模、结果分析与学术结论由小组成员完成并负责。代码已通过测试
（`test_heat.py` 6/6 全绿）并经人工审查，可直接复现。

---

## 六、目录结构

```
mission1/
├── heat.py            # 主程序（单文件函数化）
├── test_heat.py       # 单元测试（6 个）
├── README.md          # 本文件
└── output/            # 9 个输出文件（6 PNG + 3 GIF）
```
