搜索算法，贪心算法，动态规划

# 思想

![动图](pic/Dijkstra算法动图.gif)

# 复杂度

![Dijkstra算法复杂度](pic/Dijkstra算法复杂度.png)

# 伪代码

$$
\boxed{\large\begin{align*} &\large{\boldsymbol{\rm{Algorithm:Dijkstra}}}\\ &\\ &\boldsymbol{\mathrm{Input:}}\mathrm{Directed\,\, graph\,\,}G=(V,E,W)\,\,\mathrm{with\,\, weight}\\ &\\ &\boldsymbol{\mathrm{Output:}}\mathrm{All\,\, the\,\,shortest\,\,paths\,\, from\,\, the\,\, source\,\,vertex\,\,}s\mathrm{\,\,to\,\,every\,\,vertex\,\,}v_i\in V\setminus\left\{s\right\}\\ &\\ &1:S\leftarrow \left \{ s \right \}\\ &2:\mathrm{dist}[s,s]\leftarrow 0\\ &3:\boldsymbol{\mathrm{for}}\,\, v_i\in V-\left \{ s \right \}\,\,\boldsymbol{\mathrm{do}}\\ &4:\quad\,\, \mathrm{dist}[s,v_i]\leftarrow w(s,v_i)\\ &\quad \,\,\,\quad (\mathrm{when\,\,}v_i\,\,\mathrm{not\,\,found},\mathrm{dist}[s,v_i]\leftarrow \infty)\\ &5:\boldsymbol{\mathrm{while}}\,\,V-S\ne\varnothing \,\,\boldsymbol{\mathrm{do}}\\ &6:\quad\,\,\,\, \mathrm{find\,\,}\min_{v_j\in V}\,\mathrm{dist}[s,v_i]\,\,\mathrm{from\,\, the\,\, set\,\,}V-S\\ &7:\quad\,\,\,\, S\leftarrow S\cup \left\{v_j\right\}\\ &8:\,\,\,\,\quad \boldsymbol{\mathrm{for\,\,}}v_i\in V-S\,\,\boldsymbol{\mathrm{do}}\\ &9:\,\,\,\,\,\,\,\quad\quad \boldsymbol{\mathrm{if\,\,}}\mathrm{dist}[s,v_j]+w_{j,i}<\mathrm{dist}[s,v_i]\,\,\boldsymbol{\mathrm{then}}\\ &10:\,\,\,\,\,\quad\quad\quad \mathrm{dist}[s,v_i]\leftarrow \mathrm{dist}[s,v_j]+w_{j,i} \end{align*}}\tag{10}
$$



