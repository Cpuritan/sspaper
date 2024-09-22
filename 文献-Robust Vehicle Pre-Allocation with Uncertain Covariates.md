### Background

针对新加坡一家出租车运营商，如何分配闲置车辆以满足未来需求的不确定性？在新加坡这样的城市环境中，出租车运营商需要有效地管理其车辆资源，以应对高峰时段的需求波动和空间分布不均的问题。出租车时空分布不均，在市中心等繁忙区域常出现供大于求，而城市周边区域则会面临供应不足的现象。与此同时，出租车需求还受到其他不确定因素的影响，例如天气等。论文旨在通过分布鲁棒优化方法，结合协变量信息（如天气）来构建一个能够处理需求不确定性的车辆预分配模型，从而提高运营商的运营效率和盈利能力。

## Characters

$S_i$ 供应节点 $i$ 的闲置车辆数量，$i \in [N]$
$\tilde{z} = (\tilde{z_{j}})$ 需求向量，$j \in [M]$
$\tilde{v} \in \mathbb{R^I}$ 与需求相关的不确定协变量向量
$r_j$ 分配车辆到需求节点 $j$ 成功拉客时运营商的收益
$w_{ij}$ 分配车辆从供应节点 $i$ 到需求节点 $j$  的成本
$x_{ij}$ 分配车辆从供应节点 $i$ 到需求节点 $j$  的数量
需求和协变量联合分布$(\tilde{z},\tilde{v}) \in \mathbb{R^M} \times \mathbb{R^I}$
总成本$\sum\limits_{j\in[M]} \sum\limits_{i\in[N]} w_{ij} x_{ij}$ ；总收益$\sum\limits_{j\in[M]} r_{j} (\tilde{z_{j}}  \wedge \sum\limits_{i\in[N]} x_{ij})$ 

## SO Model
如果对于运营商而言 $(\tilde{z},\tilde{v})$ 的**联合分布信息是明确已知**的，即$\mathbb{Q} \in \mathcal{P_{0}(\mathbb{R^{M}} \times \mathbb{R^{I}}})$ ，则车辆分配问题可以指定为以下随机运输问题：
$$
\begin{align}
&\mathop{max}\limits_{x_{ij} \geq 0} -\sum\limits_{j\in[M]} \sum\limits_{i\in[N]} w_{ij} x_{ij} + \mathbb{E}_\mathbb{Q}\left[\sum\limits_{j\in[M]} r_{j} \left(\tilde{z_{j}}  \wedge \sum\limits_{i\in[N]} x_{ij}\right)\right] \tag{1}\\
&s.t.\sum\limits_{j\in[M]}x_{ij}\leq S_{i} \quad,\forall i\in [N]
\end{align}
$$
令$\mathbb{P} = \Pi_{\tilde{z}} \mathbb{Q}$ 表示在 $\tilde{z}$ 下 $\mathbb{Q}$ 的边缘分布，且$(1)$中的目标函数独立于不确定协变量向量$\tilde{v}$，则可以等价如下：
$$
\begin{align}
&\mathop{max}\limits_{x_{ij} \geq 0} -\sum\limits_{j\in[M]} \sum\limits_{i\in[N]} w_{ij} x_{ij} + \mathbb{E}_\mathbb{P}\left[\sum\limits_{j\in[M]} r_{j} \left(\tilde{z_{j}}  \wedge \sum\limits_{i\in[N]} x_{ij}\right)\right] \tag{2}\\
&s.t.\sum\limits_{j\in[M]}x_{ij}\leq S_{i} \quad,\forall i\in [N]
\end{align}
$$
但以上模型在边际分布$\mathbb{P}$未知时将不再成立。随机运输问题是基于$\mathbb{Q}$或$\mathbb{P}$完全已知的假设来求解问题$(1)$。然而，在实践中，此类信息需要从历史数据中估计。一种常用的方法是样本平均近似（SAA）。

若有$T$个需求和协变量的历史数据样本$\mathcal{T} = \{(\hat{z}_{1},\hat{v}_{1}),(\hat{z}_{2},\hat{v}_{2}),...,(\hat{z}_{T},\hat{v}_{T})\}$，采用 SAA 方法使用经验分布来近似分布Q，其中每个样本$(\hat{z}_{t},\hat{v}_{t})$具有相等的概率$\frac{1}{T}$则问题$(1)$可**近似**如下：
$$
\begin{align}
& \Pi^{\mathrm{SAA}}=\max _{x_{i j} \geq 0}-\sum_{j \in[M]} \sum_{i \in[N]} w_{i j} x_{i j}+\frac{1}{T} \sum_{t \in[T]} \sum_{j \in[M]} r_j\left(\hat{z}_{j t} \wedge \sum_{i \in[N]} x_{i j}\right) \tag{3}\\
& \text { s.t. } \quad \sum_{j \in[M]} x_{i j} \leq S_i, \forall i \in[N] \text {, }
\end{align}
$$
其中$\hat{z}_{j t}$表示需求变量$\hat{z}_{t}$的第$j$个分量，上述 SAA 近似公式可以重构成一个线性规划问题来解决，线性规划模型如下：
$$
\begin{align}
\Pi^{\mathrm{SAA}} & =\max _{x_{i j} \geq 0, y_{j t}}\left\{-\sum_{j \in[M]} \sum_{i \in[N]} w_{i j} x_{i j}+\frac{1}{T} \sum_{j \in[M]} \sum_{t \in[T]} r_j y_{j t}\right\}\\
\text { s.t. } & \sum_{j \in[M]} x_{i j} \leq S_i, \forall i \in[N] \tag{3*}\\
& y_{j t} \leq \hat{z}_{j t}, \forall j \in[M], t \in[T] \\
& y_{j t} \leq \sum_{i \in[N]} x_{i j}, \forall j \in[M], t \in[T]
\end{align}
$$
因此，从操作者的角度来看，真实分布是不确定的，仅属于一组共享一致的部分分布信息的分布。为了解决我们问题中的分布不确定性，我们采用分布鲁棒优化框架，并将协变量信息纳入创新的场景不确定性集

## DRO Model

不同于随即优化模型，分布鲁棒框架假设真实分布$(\tilde{z},\tilde{v}): \mathbb{Q}\in\mathcal{P}_{0}(\mathbb{R}^{M}\times\mathbb{R}^{I})$在一个不确定集合$\mathbb{F}\subseteq\mathcal{P}_{0}(\mathbb{R}^{M}\times\mathbb{R}^{I})$ ，这个不确定集合$\mathbb{F}$是由数据的部分分布信息估计得到的。使用$\Pi_{\tilde{z}}\mathbb{F}$表示集合 $\mathbb{F}$ 中所有分布的$\tilde{z}$的边缘分布。在DRO框架下，运营商意图最大化 $\mathbb{F}$ 中所有可能分布下最坏情况 的期望利润，如下：
$$
\begin{align}
\max\limits_{x_{i j}\ge0} & -\sum\limits_{j\in[M]}\sum\limits_{i\in[N]}w_{i j}x_{i j}+\inf\limits_{\mathbb{Q\in F}}\mathbb{E_{Q}}\left[\sum\limits_{j\in [M]}r_{j}(\tilde{z}_{j}\wedge\sum\limits_{i\in[N]}x_{i j}) \right]\tag{4}\\
\text{s.t.}&\sum\limits_{j\in[M]}x_{i j} \leq S_{i}\text{ , }\forall i\in [N]
\end{align}
$$
方程$(4)$的解及其样本外性能都取决于模糊集F。

### 4.1 含协变量的场景不确定性集合

由于模糊集$\mathbb{F}$对于模型的解有决定性作用，引入一种情景化的模糊集，该模糊集结合了不确定需求和不确定协变量的分布信息。后续说明协变量信息的价值以及如何基于历史数据估计情景化的模糊集。

为了利用协变量信息，本文将 $\tilde{v}$ 所有可能的实现划分为$L$种情景：$\Omega_{l}, l\in[L]$其中 $\Omega_{l}\bigcap\Omega_{k}=\emptyset$对于所有的 $l, k \in [L]$ 且$l\neq k$，并且$\bigcup_{l\in[L]}\Omega_{l}=\Omega$。令$p_{l}$ 表示第$l$种情景发生的概率。结合所有情景与需求的边际矩信息，我们构建了以下基于情景的模糊集：

$$
\begin{align}
&\mathbb{F}=\left\{\mathbb{Q}\subseteq\mathcal{P}_{0}(\mathbb{R}^{M}\times\mathbb{R}^{I})\begin{array}{|l}
(\tilde{\mathbf{z}}, \tilde{\mathbf{v}}) \sim \mathbb{Q} & \\
\mathbb{E}_{\mathbb{Q}}\left(\tilde{\mathbf{z}} \mid \tilde{\mathbf{v}} \in \Omega_l\right)=\boldsymbol{\mu}_l, & \forall l \in[L] \\
\mathbb{E}_{\mathbb{Q}}\left(\left(\tilde{z_{j l}}-\mu_{j l}\right)^2 \mid \tilde{\mathbf{v}} \in \Omega_l\right) \leq \sigma_{j l}^2, & \forall l \in[L], j \in[M] \\
\mathbb{Q}\left(\tilde{\mathbf{v}} \in \Omega_l\right)=p_l, & \forall l \in[L] \\
\mathbb{Q}\left(\tilde{\mathbf{z}} \in \mathcal{Z}_l \mid \tilde{\mathbf{v}} \in \Omega_l\right)=1, & \forall l \in[L]
\end{array}\right\}
\end{align}
$$

 其中$(\tilde{\mathbf{z}}, \tilde{\mathbf{v}}) \sim \mathbb{Q}$表示联合需求和协变量服从于真实分布；$\mathbb{E}_{\mathbb{Q}}\left(\tilde{\mathbf{z}} \mid \tilde{\mathbf{v}} \in \Omega_l\right)=\boldsymbol{\mu}_l$表示情景$l$下 $\tilde{v}$ 属于该情景时需求的**均值**；$\mathbb{E}_{\mathbb{Q}}\left(\left(\tilde{z_{j l}}-\mu_{j l}\right)^2 \mid \tilde{\mathbf{v}} \in \Omega_l\right) \leq \sigma_{j l}^2$表示每个情景$l$下$\tilde{v}$属于该情景时需求的**方差**的上限；$\mathbb{Q}\left(\tilde{\mathbf{v}} \in \Omega_l\right)=p_l$指定了每个情景$l$的概率；$\mathcal{Z}_l=\left[\underline{\mathbf{z}}_l, \overline{\mathbf{z}}_l\right]$明确每个情景中需求可取的最大最小值。

### 4.2 协变量的价值

**举例** ：考虑当 $\tilde{z}$ 和 $\tilde{v}$ 都是一维的情况，记作 $(\tilde{z}, \tilde{v})$。假设潜在的真实模型如下：$\tilde{v}$ 服从伯努利分布，其中 $\mathbb{Q}(\tilde{v} = 1) = 1/2$。在给定 $\tilde{v}$ 的条件下，我们有 $\mathbb{Q}(\tilde{z} = z_1|\tilde{v} = 1) = 1$，$\mathbb{Q}(\tilde{z} = z_0|\tilde{v} = 0) = 1$，其中 $z_0 \leq z_1$。可以将 $\tilde{v} = 1$ 解释为天气下雨的事件。相应地，$\mathbb{Q}(\tilde{z} = z_1|\tilde{v} = 1) = 1$ 表明在下雨天一定将看到高需求 $z_1$。进一步假设，我们已知 $\tilde{v}$ 的分布，但无法知晓 $\tilde{z}$ 的分布，仅在给定 $\tilde{v}$ 的实现后，才知道 $\tilde{z}$ 的确切均值和方差。**不利用协变量信息**，我们可以用 $\tilde{z}$ 的均值和方差来表示不确定性集$\mathbb{\hat{F}}$为：
$$
\hat{\mathbb{F}}=\left\{\begin{array}{l|l}
\mathbb{Q} \in \mathcal{P}_0(\mathbb{R} \times \mathbb{R}) & \begin{array}{l}
(\tilde{z}, \tilde{v}) \sim \mathbb{Q} \\
\mathbb{E}_{\mathbb{Q}}(\tilde{z})=\frac{z_1+z_0}{2} \\
\mathbb{E}_{\mathbb{Q}}\left(\tilde{z}-\frac{z_1+z_0}{2}\right)^2 \leq\left(\frac{z_1-z_0}{2}\right)^2 \\
\mathbb{Q}(\tilde{z} \in \mathbb{R})=1
\end{array}
\end{array}\right\}
$$
当考虑协变量$\tilde{v}$ 则可以写出如下模糊集：
$$
\overline{\mathbb{F}}=\left\{\begin{array}{l|l}
\mathbb{Q} \in \mathcal{P}_0(\mathbb{R} \times \mathbb{R}) & \begin{array}{l}
(\tilde{z}, \tilde{v}) \sim \mathbb{Q} \\
\mathbb{E}_{\mathbb{Q}}(\tilde{z} \mid \tilde{v}=i)=z_i, \text { for } i=0,1 \\
\mathbb{E}_{\mathbb{Q}}\left(\left(\tilde{z}-z_i\right)^2 \mid \tilde{v}=i\right) \leq 0, \text { for } i=0,1 \\
\mathbb{Q}(\tilde{v}=i)=\frac{1}{2}, \text { for } i=0,1 \\
\mathbb{Q}(\tilde{z} \in \mathbb{R} \mid \tilde{v}=i)=1, \text { for } i=0,1
\end{array}
\end{array}\right\}
$$
在$\overline{\mathbb{F}}$中第一和第二个约束可以得出需求$\tilde{v}$的均值和方差。$\overline{\mathbb{F}}$中仅包含一个真实分布，即 $\mathbb{Q}(\tilde{z} = z_1|\tilde{v} = 1) = 1/2 ，\mathbb{Q}(\tilde{z} = z_1|\tilde{v} = 1) = 1/2$ 。上述例子表明，协变量可以帮助我们获得一个较为不保守的模糊集合。
Proposition 1可以证明$\Pi^{SDR}\geq \Pi^{MMM}$  

### 4.3 模糊集 $\overline{\mathbb{F}}$ 的估计

对于给定的场景数 $L$ 而言，估算模糊集 $\overline{\mathbb{F}}$ 的关键在于使得该场景下 $\tilde{z}$ 具有最小方差。为了实现该目的，本章节使用一种直观且广泛使用的非参数方法，回归树。考虑本文探讨的问题中需求向量 $\tilde{z}$ 是独立的$M$维向量，因此采用多变量回归树**MRT**。回归树迭代的将样本空间一分为二，每一个叶结点代表一个子集， 每一个子集 $l$ 可以表示为$\mathcal{K_{l}}=\{t|\hat{v}_{t}\in \Omega_{l}\}$。**MRT**将分区的不纯度定义为$E^{MRT}=\sum\limits_{l\in[L]}\sum\limits_{t\in\mathcal{K}_{l}}\|\hat{z}_{t}-\mu_{l}\|_{2}^{2}$ 其中$\mu_{l}=\frac{1}{|\mathcal{K}_{l}|}\sum\limits_{t\in\mathcal{K}_{l} }\hat{z}_{t}$  则**MRT**通过选择不同的叶结点最小化$E^{MRT}$：
$$
\begin{align}
\min_{Tree \ with \ L \ Leaf \ Nodes}\sum\limits_{l\in[L]}\sum\limits_{t\in\mathcal{K}_{l}}(\hat{z}_{t}-\mu_{l})^{2}\tag{5}
\end{align}
$$
通过**MRT**，可以通过叶节点的大小估计场景 $l$ 的概率$p_{l}$如下：
$$
\hat{p}_l=\frac{|\mathcal{K}_l|}{T}
$$
进而场景 $l$ 下需求的均值$\mu_{l}$和方差$\sigma_{j l}^{2}$可以估计如下：
$$
\hat{\mu}_{l}=\frac{1}{|\mathcal{K}_{l}|} \sum\limits_{t\in\mathcal{K}_l}\hat{z}_{t}\quad\quad\sigma_{j l}^{2}=\sum\limits_{t\in\mathcal{K}_l}(\hat{z}_{jt}-\hat{\mu}_{jl})^{2} 
$$
上述的估计仍满足上述Proposition 1
虽然回归树并非是唯一划分场景的方法，还存在其他的无监督聚类方法，但是我们发现其他的无监督聚类方法并未利用协变量。我们在以下例子中展示了在某些情况下，利用协变量信息，**回归树**可以提供比仅基于因变量进行聚类的无监督聚类方法**更准确**的情景分类。

例 2 考虑一个一维的情况，有$(\tilde{z} , \tilde{v})$服从分布$\mathcal{Q}(\tilde{v}=1)=1/2$$\mathcal{Q}(\tilde{v}=2)=1/2$对于需求$\tilde{z}$有如下表所示的概率：
$$
\begin{array}{c|c|c} 
\hline
\tilde{z} & \tilde{v} & \mathbb{Q}(\tilde{z} \mid \tilde{v}) \\
\hline \hline
1 & & 2 / 3 \\
3 & 1 & 1 / 3 \\
\hline
2 & & 1 / 3 \\
4 & 2 & 2 / 3 \\
\hline

\end{array}
$$
假设我们从这个分布中获得了6个样本 $(\tilde{z} , \tilde{v})$：(1, 1), (1, 1), (3, 1), (2, 2), (4, 2), 和 (4, 2)。对于 L = 2，使用回归树，{(1, 1), (1, 1), (3, 1)} 被识别为情景 1，概率为 0.5，而 {(2, 2), (4, 2), (4, 2)} 被识别为情景 2，概率也为 0.5。相比之下，使用忽略协变量信息的 K-means 聚类，只利用 $\tilde{z}$ 的数据点，即 1, 1, 2, 3, 4 , 4。因此，在 K-means 聚类中当 K = 2 时，{1, 1, 2} 被聚类为情景 1，概率为 0.5，而 {3, 4, 4} 被聚类为情景 2，概率也为 0.5。
回归树方法还具有良好的可解释性，展示了分区与协变量之间的联系。这种可解释性还赋予了场景化的模糊集在结合预测信息方面的灵活性，例如，协变量落入场景 $l$ 的概率。
```
from sklearn.tree import DecisionTreeRegressor
from sklearn.tree import plot_tree

regr = DecisionTreeRegressor(max_leaf_nodes=4,          # 最大叶结点数量
                             min_samples_leaf=3)        # 每个结点最小样本数
regr.fit(precip, demand)
mu, index, counts = np.unique(regr.predict(precip), 
                              axis=0,
                              return_inverse=True,
                              return_counts=True)       # conditional mean
w = counts/precip.shape[0]                              # 场景权重         
phi = np.array([demand.values[index==i].var(axis=0)
                for i in range(len(counts))])           # conditional variance
d_ub = np.array([demand.values[index==i].max(axis=0)
                for i in range(len(counts))])           # upper bound of each scenario
d_lb = np.array([demand.values[index==i].min(axis=0)
                for i in range(len(counts))])           # lower bound of each scenario

plt.figure(figsize=(20, 10))                            # 回归树可视化
plot_tree(regr, rounded=True, feature_names=demand.columns, fontsize=13)
plt.show()
```

```
# 可视化不同场景下不同地区的需求分布情况
fig = plt.figure(figsize=(10, 4))
gs = fig.add_gridspec(1, 4, hspace=0, wspace=0)
axes = gs.subplots(sharey='row')
for i in range(4):
    each_demand = demand.values[index==i]
    axes[i].boxplot(each_demand, 
                    vert=False, flierprops={'markersize': 4})
    axes[i].set_yticks(list(range(1, 11)), 
                       ['Region{0}'.format(i) for i in range(1, 11)], 
                       fontsize=11)
    axes[i].set_xlabel('Demand', fontsize=12)
    axes[i].set_title('Sample size: {0}'.format(each_demand.shape[0]), 
                      fontsize=13)
axes[3].set_ylim(10.5, 0.5)
plt.show()
```
基于由场景均值和方差定义的模糊集𝐹，分布鲁棒模型的实现如下
```
from rsome import dro                         # import the dro module
from rsome import square                      # import the element-wise square function
from rsome import E                           # import the notion of expectation
from rsome import eco_solver as eco           # import the ECOS interface

S = len(w)                                    # number of scenarios
model = dro.Model(S)                          # create a DRO model with S scenarios

d = model.rvar(J)                             # random demand as the variable d
u = model.rvar(J)                             # auxiliary random variable u
fset = model.ambiguity()                      # create an ambiguity set
for s in range(S):                            # for each scenario:
    fset[s].exptset(E(d) == mu[s],            # specify the expectation set of d and u
                    E(u) <= phi[s])
    fset[s].suppset(d >= d_lb[s],             # specify the support of d and u
                    d <= d_ub[s],
                    square(d - mu[s]) <= u)
pr = model.p                                  # an array of scenario probabilities
fset.probset(pr == w)                         # w as scenario weights

x = model.dvar((I, J))                        # here-and-now decision x
y = model.dvar(J)                             # wait-and-see decision y
y.adapt(d)                                    # y affinely adapts to d
y.adapt(u)                                    # y affinely adapts to u
for s in range(S):                            # for each scenario:
    y.adapt(s)                                # affine adaptation of y is different

model.minsup(((c-r)*x).sum() + E(r@y), fset)  # minimize the worst-case objective
model.st(y >= x.sum(axis=0) - d, y >= 0)      # robust constraints
model.st(x >= 0, x.sum(axis=0) <= q)          # deterministic constraints

model.solve(eco)                              # solve the model by ECOS
status = model.solution.status                # return the solution status
time = model.solution.time                    # return the solution time
objval = model.get()                          # get the optimal objective value
x_sol = x.get()                               # get the optimal solution
```