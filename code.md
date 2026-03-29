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


## Evolutionary Algorithm

同样以N皇后问题为例
**需要定义一个评价函数（用于选择个体），和产生后代的机制。保持每轮种群数量不变，迭代一定次数，算出当前最优解，再产生新种群**
- 编码：表示皇后位置的一维数组天然可当作编码
- 交配：根据锦标赛选出parents，随机挑选parent1中某一段，按顺序拼接parent2中除了刚才那段中的其他数字即可作为交配
- 变异：产生的后代自己交换某两个位置（生成随机数小于设定的变异概率才变异）
- 选择：锦标赛选择法，种群中随机选5个候选者，其中适应度最好的那个被选为parent1，parent2同理。
- 新种群：加入当前最优个体，剩余个体通过交配和变异产生，直到种群数量达到设定值

```python
def order_crossover(parent1, parent2, n): #杂交

    idx1, idx2 = sorted(np.random.choice(range(n), 2, replace=False)) # 选择杂交中交换的区间
    
    child = np.full(n, -1) #初始化后代
    subset = parent1[idx1:idx2+1]
    child[idx1:idx2+1] = subset #将parent1的子序列复制到child中
    
    fill_values = [x for x in parent2 if x not in subset] #从parent2中选择不在子序列中的元素，按照顺序填充到child中
    
    current_fill = 0 
    for i in range(n):
        if child[i] == -1:
            child[i] = fill_values[current_fill]
            current_fill += 1
            
    return child

def swap_mutation(grid, n):  #变异
    i, j = np.random.choice(n, 2, replace=False)
    grid[i], grid[j] = grid[j], grid[i]
    return grid

def genetic_algorithm(n, pop_size=100, max_generations=1000, mutation_rate=0.1):
    # 以随机排列的形式初始化种群，每个个体都是一个长度为n的数组，表示每列皇后的位置
    population = [np.random.permutation(n) for _ in range(pop_size)] 
    history = []
    best_solution = None
    min_conflicts = float('inf') #最小冲突初始化为无穷大

    prob = NQueenProblem(None, n, None) #初始化一次问题实例，方便后面用他的value函数计算冲突数

    for generation in range(max_generations):  #迭代次数      
        scores = []
        for individual in population:
            prob.grid = individual #将个体编码传进实例参数，以正确计算value
            conflicts = prob.value()
            scores.append(conflicts)
        
        scores = np.array(scores)
        
        # Track best solution
        current_best_idx = np.argmin(scores)  #找出冲突最少的个体
        current_best_conflicts = scores[current_best_idx]
        history.append(current_best_conflicts)
        
        if current_best_conflicts < min_conflicts:  
            min_conflicts = current_best_conflicts
            best_solution = population[current_best_idx]
        
        if min_conflicts == 0: #找到完美解，提前终止
            print(f"Solution found at generation {generation}!")
            break
            
        # Selection & Reproduction
        new_population = []
        
        new_population.append(population[current_best_idx]) #先加入当前最优个体 
        
        # 当个体数量不够时，利用锦标赛法繁殖新个体进行补充
        while len(new_population) < pop_size:
            # 锦标赛挑选法
            # Select 2 parents
            # Parent 1
            # 种群中选5个候选者，其中适应度最好的那个被选为parent1
            candidates = np.random.choice(len(population), 5, replace=False)
            p1_idx = candidates[np.argmin(scores[candidates])]
            parent1 = population[p1_idx]
            
            # Parent 2
            candidates = np.random.choice(len(population), 5, replace=False)
            p2_idx = candidates[np.argmin(scores[candidates])]
            parent2 = population[p2_idx]
            
            # Crossover
            child = order_crossover(parent1, parent2, n)
            
            # Mutation
            if np.random.rand() < mutation_rate:
                child = swap_mutation(child, n)
                
            new_population.append(child)
            
        population = new_population

    return NQueenProblem(best_solution, n, None), history
```

## Backtracking Search (bts)
类似DFS，递归地构建解空间树。当构建到某一层（对某个变量赋值）时，如果发现当前解不满足限制条件，则回溯到上一层（取消对该变量的赋值），尝试其他分支。
若搜索树较大，可结合启发式方法来提高效率。每次赋值变量时，需记录剩余变量的合法值数量。
* MRV：每次选择可选合法值最少的变量进行赋值，优先处理最受限制的变量。
* LCV：对该变量赋值时选择对其他变量合法值影响最小的结果，减少冲突的产生。
* 前向检查：每次赋值后，检查剩余变量的合法值数量，如果某个变量没有合法值了（即无解），立即剪枝回溯。

```python
def bts(problem):
    action_stack = [] #目前已放入的所有皇后位置

    #栈顶元素（m，n）表示第m行最后尝试的是放在n列
    stack = [(0, -1)]  # 从第0行开始尝试

    while not problem.is_satisfy(action_stack):
            
        row, col = stack.pop()
        
        next_col = col + 1
        found_valid_pos = False
        
        while next_col < problem.N:

            current_pos = (row, next_col) 
            
            temp_state = action_stack + [current_pos] #加入进来测试
            
            if problem.is_valid(temp_state):
                found_valid_pos = True
                
                stack.append((row, next_col))
                
                # Update action_stack
                action_stack.append(current_pos)
                yield action_stack
                
                # 这一行皇后处理完成，尝试放下一个皇后
                if row + 1 < problem.N:
                    stack.append((row + 1, -1))
                break
            
            next_col += 1 #当前位置皇后invalid，尝试放在下一个位置
            
        if not found_valid_pos: # 这一行没有位置valid，回溯
            if action_stack:
                action_stack.pop()
                yield action_stack
```

加入启发式方法的bts：
```python
import copy

def improving_bts(problem):
    action_stack = []
    domains = {i: list(range(problem.N)) for i in range(problem.N)}  # 每行的可选列  
    untested_rows=[i for i in range(problem.N)] #未放置皇后的行
    stack = [([],domains,untested_rows)]  # 当前状态，目前合法的位置，未测试过的行

    while stack:
        
        cur_state, cur_domain, cur_untested_rows = stack.pop()

        while len(action_stack) > len(cur_state): # 说明回溯了，把action_stack弹出直到同个状态
            action_stack.pop()
            yield action_stack  
            
        # 当前快照有新进展，则将其加入 action_stack
        if len(cur_state) > len(action_stack):
            action_stack.append(cur_state[-1])
            yield action_stack

        if problem.is_satisfy(cur_state): #满足条件，退出
            break
        
        row = min(cur_untested_rows, key=lambda r: len(cur_domain[r])) #取出目前未测试行中合法位置最少者：MRV
            
        def count_conflicts(col):
            # 如果在这个(row, col)放皇后，会减少其余行多少个可选位置
            conflicts = 0
            for r in cur_untested_rows:
                if r != row:
                    for c in cur_domain[r]:
                        if not problem.is_valid(cur_state + [(row, col)] + [(r, c)]): 
                            conflicts += 1
            return conflicts


        # 遍历（行，可选列），选出其中对domains影响最小的列：LCV
        cols_to_try = sorted(cur_domain[row], key=count_conflicts, reverse=True)
        
        for col in cols_to_try:
            # 准备下一层的快照
            next_state = cur_state + [(row, col)]
            next_untested = [r for r in cur_untested_rows if r != row]
            next_domain = copy.deepcopy(cur_domain) 
            
            # 目前的state都是合法的，无需再次检查
            is_valid_branch = True
            for r in next_untested:
                # 过滤掉所有与当前(row, col)冲突的位置
                next_domain[r] = [
                    c for c in next_domain[r] 
                    if problem.is_valid(next_state + [(r, c)])
                ]
                # 再次剪枝:forward checking
                if not next_domain[r]:
                    is_valid_branch = False
                    break
                    
            if is_valid_branch:
                # 每个合法的col_to_try都压栈，用于后续回溯
                stack.append((next_state, next_domain, next_untested))
```

## Local Search 
先随机生成一组解（可以是不满足限制条件的）。然后挑选当前解中有冲突的一个变量(不总是选择冲突最多的，防止陷入局部最优)，改变它的取值使得冲突数减少最多。重复这个过程直到找到满足条件的解或者达到迭代次数上限。
**随机算法，可能无法找到可行解，但效率极高（适合搜索树巨大的情况）**
以八皇后问题为例，初始解可以是每行一个皇后，列随机分布。每一步选择一个有冲突的皇后，尝试将它移动到同一行的其他列，计算每个位置的冲突数，选择冲突数最少的位置移动过去。重复这个过程直到找到无冲突的解或者达到迭代次数上限。

```python
import random

def min_conflict(problem):
    action_stack = [(i, np.random.choice(problem.N)) for i in range(problem.N)]
    max_step = 1000
    cur_step = 0

    def is_conflict(r1, c1, r2, c2):
        return c1 == c2 or (r1 - c1 == r2 - c2) or (r1 + c1 == r2 + c2)
    
    while not problem.is_satisfy(action_stack) and cur_step < max_step:
        cur_step += 1
        conflicts = [] #记录当前有冲突的皇后位置
        for i1, c1 in action_stack:
            for i2, c2 in action_stack:
                if i1 != i2: #不和原来的自己比较
                    if is_conflict(i1, c1, i2, c2):
                        conflicts.append((i1, c1))
                        break
        if not conflicts: 
            break
        (x,y)=random.choice(conflicts)  #随机选择一个冲突的皇后

        #找出冲突最少的位置
        min_conflict_col = []  
        min_conflict_count = float('inf')
        for ny in range(problem.N):  
            if ny != y:
                conflict_count = 0
                for i, j in action_stack:
                    if i != x : #不和原来的自己比较
                        if is_conflict(x, ny, i, j):
                            conflict_count += 1
                if conflict_count < min_conflict_count:
                    min_conflict_col = [ny]
                    min_conflict_count = conflict_count
                elif conflict_count == min_conflict_count:
                    min_conflict_col.append(ny)
                    
        action_stack[x] = (x, random.choice(min_conflict_col))
        yield action_stack
```
