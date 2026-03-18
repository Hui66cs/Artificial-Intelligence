## A* Search

**需要启发条件，如每个点到终点的距离**

idea: 与dijkstra算法原理一样，区别在于构建最小堆时用的是f(n)=g(n)+h(n)，其中g(n)是起点到当前节点的实际距离，h(n)是当前节点到目标节点的直线距离。

```python
import heapq  #最小堆
  
def AStarSearch(Graph,start,end,distances):
    # Graph['S']:[('R', '80'), ('F', '99'),...] 为邻接节点和边权
    # distances['S'] = 366  #节点到目标节点的距离
    # start 和 end 是起点和终点的节点名

    g = {start: 0}  #起点g为0
    visited = set()  #标记访问过的节点

    def get_f(n):  #计算f(n) = g(n) + h(n)
        return g[n] + distances[n]

    min_heap = [] 
    parent = {}  #记录每个节点的父节点，便于回溯路径

    # 堆中存储元组：(f_score, node_name)，先按f排，再按节点字典序排
    heapq.heappush(min_heap, (get_f(start), start))
    
    while len(min_heap) > 0:
        
        curr_f, node = heapq.heappop(min_heap)  #取出最顶上的节点
  
        if node in visited :  #跳过discovered节点,不用再写懒删除，因为首先弹出的节点肯定是最小的，然后被标记为visited了
            continue
            
        if node == end: #检测到终点，结束搜索
            break

        visited.add(node)
        
        for neighbor, weight in Graph[node]: 
            new_g = g[node] + int(weight)   
            
            if neighbor not in g or new_g < g[neighbor]:
                g[neighbor] = new_g
                parent[neighbor] = node  
                f_val = get_f(neighbor)
                heapq.heappush(min_heap, (f_val, neighbor))

    # 回溯路径
    current = end
    route = []
    while current != start:
        route.append(current)
        current = parent[current]
    route.append(start)
    route.reverse()

    return route
```

## Simulated Annealing

**需要定义一个评价函数（用于决定是否接受新解），和一个邻域结构（生成新解的方式）**

以N皇后问题为例，评价函数可以是当前棋盘上相互攻击的皇后对数，邻域结构可以是交换两行或者随机打乱棋盘。

```python
# In init state of the n-queen problem,用一位数组而非二维数组表示位置。这样可以天然的避免行列冲突，只需考虑对角线冲突。
# if n = 8, grid = [0, 1, 2, 3, 4, 5, 6, 7],代表
# 1 0 0 0 0 0 0 0
# 0 1 0 0 0 0 0 0
# 0 0 1 0 0 0 0 0
# 0 0 0 1 0 0 0 0
# 0 0 0 0 1 0 0 0
# 0 0 0 0 0 1 0 0
# 0 0 0 0 0 0 1 0
# 0 0 0 0 0 0 0 1

def value(self):  # 冲突对数
    conflicts = 0
    for i in range(self.n-1):
        for j in range(i + 1, self.n):
            if abs(self.grid[i] - self.grid[j]) == abs(i - j):  # 对角线。行、列由定义自动满足
                conflicts += 1
    return conflicts 

def swap(grid, n):
    # swap two positions in the grid
    new_grid = grid.copy()  # 复制，避免修改原来的grid（按引用传递），保证不接受新解可以自动回退（由于没改变）
    i, j = np.random.choice(n, 2, replace=False)
    new_grid[i], new_grid[j] = new_grid[j], new_grid[i]  #交换
    return new_grid
```

idea: 每一步生成一个新解，如果新解更优则直接接受，否则根据温度、差值以一定概率接受（给机会跳出局部最优解）。温度逐渐降低，最终趋近于0时基本不接受更差的解了。温度可以类比成醉酒程度
1. 若求最大值（如函数最大值），则$$\Delta f = cur\_value - new\_value$$
2. 若求最小值（如N皇后最少冲突），则$$\Delta f = new\_value - cur\_value$$
   
$\Delta f<0$则直接接受新解，不接受则生成一个随机数，小于概率$P$接受则接受新解，概率计算公式如下：
$$P = \exp\left(\frac{-\Delta f}{T}\right)$$
这里保证了$\Delta f$为正数，否则P会大于1，导致不合理的接受概率。

```python
def simulated_annealing(initial:NQueenProblem, schedule, halt, log_interval=200):
    state = initial
    cur_value = state.value()
    t = 0           # time step
    T = schedule(t) # temperature
    f = [state.value()] # a recording of values
    while not halt(T):
        T = schedule(t)
        new_state = state.local_search()
        new_value = new_state.value()
        # 要求的是最小值,所以delta f = new_value - cur_value。
        # TODO: implement the replacement here
        if new_value < cur_value:
            state = new_state
            cur_value = new_value
    
        elif T > 0:
            p = np.exp((cur_value - new_value) / T)
            if np.random.rand() < p:
                state = new_state
                cur_value = new_value

        f.append(cur_value)
        
        # update time and temperature
        if t % log_interval == 0:
            print(f"step {t}: T={T}, current_value={cur_value}")
        if new_value == 0: # 找到最优解了，提前退出
            break
        t += 1
    print(f"step {t}: T={T}, current_value={cur_value}")
    return state, f

rng = np.random.default_rng(200)  #设置随机数种子
solution, record = simulated_annealing(
    initial=NQueenProblem(np.arange(12), 12, swap),
    schedule=lambda t: 0.999**t,  #降温机制为指数衰减，初始温度是0次方为1
    halt=lambda T: T<1e-7,  #停止温度
    log_interval=100   #打印日志间隔（与算法无关）
)
solution.print_grid()
solution.grid, solution.value()
```
**超参数配置建议**
- 初始温度：初始温度需要设置成能跳出局部最优解才行，即一开始能接受坏解的概率最好在50%~90%
- 降温机制：这里用的是几何降温中的指数衰减$0.999^t$，也可用线性降温$1 - 0.0001\times t$,快速降温$ \frac{1}{1 + t } $。初始温度也可以不是1。过快的降温可能导致过早收敛到局部最优解，过慢的降温则可能导致算法运行时间过长。
- 停止条件：一般设为一个较小的温度阈值（如1e-7），或找到最优解提前退出
- 扰动步长：这里用的是交换，小步长。如果用矩阵重排算大步长，不好收敛等


