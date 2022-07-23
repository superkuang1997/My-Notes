# 综述

Geatpy 由两部分组成：

- 内核层：由 C++编写，负责具体计算
- 框架层：包括四大核心类





# 数据结构

## 种群染色体 Chrom

种群染色体是一个 numpy 的 array 类型的二维矩阵，一般用 Chrom 命名，每一行对应一个个体的一条染色体。

<img src="http://store.secretcamp.cn/uPic/image-20210408165131475202104081651311617871891qJHqnLqJHqnL.png" alt="image-20210408165131475" style="zoom:50%;" />



## 种群表现型 Phen

种群表现型的数据结构是 numpy 的 array 类型。

它是种群染色体矩阵 Chrom 经过解码后得到的基因表现型矩阵，每一行对应一个个体，每一列对应一个决策变量。

<img src="http://store.secretcamp.cn/uPic/image-202104081645256182021040816452516178715254YqeQy4YqeQy.png" alt="image-20210408164525618" style="zoom:50%;" />

Phen 的值与采用的解码方式有关，在不需要解码的 "实值编码" 种群中，这种种群的染色体的每一位就对应着决策变量的实际值，即这种编码方式下 Phen 等价 Chrom 。



## 目标函数 ObjV

目标函数的数据存储结构是 numpy 的 array 。

ObjV 的每一行对应每一个个体，因此它拥有与 Chrom 相同的行数。

每一列对应一个目标函数，对于单目标函数，ObjV 会只有 1 列；而对于多目标函数，ObjV 会有多列。

![image-20210408164754474](http://store.secretcamp.cn/uPic/image-20210408164754474202104081647541617871674sM7SmssM7Sms.png)



## 个体适应度 FitnV

个体适应度用列向量表示，它是 numpy 的 array 类型。

每一行对应种群矩阵的每一个个体。因此它拥有与 Chrom 相同的行数。

<img src="http://store.secretcamp.cn/uPic/image-20210408165217569202104081652171617871937sQQAO7sQQAO7.png" alt="image-20210408165217569" style="zoom:50%;" />



## 违反约束程度 CV

矩阵CV（Constraint Violation Value） 来存储种群个体违反各个约束条件的程度。

每一行对应种群的每一个个体，因 此它拥有与 Chrom 相同的行数；

每一列对应一个约束条件，因此若有一个约束条件，那么 CV 矩阵就会只有一列，如有多个约束条件，CV 矩阵就会有多列。

CV 矩阵的某个元素若小于或等于 0，则表示该元素对应的个体满足对应的约束条件。若大于 0，则表示违反约束条件

Geatpy 提供两种处理约束条件的方法

- 罚函数法
- 可行性法则

在使用可行性法则处理约束条件时，需要用到 CV 矩阵。

<img src="http://store.secretcamp.cn/uPic/image-20210408165526941202104081655271617872127ZtvHJxZtvHJx.png" alt="image-20210408165526941" style="zoom:50%;" />





## 译码矩阵

译码矩阵 （区域描述器） 来描述种群染色体的特征

在使用面向对象编程时，译码矩阵可以和 Encoding 配合使用



### FieldD

`Encoding = "BG"` 表示编码方式是二进制或格雷码，此时使用 8 行 n 列的矩阵 FieldD 来作为译码矩阵。

- lens：染色体的每个子染色体的长度（此处存疑）
- lb：决策变量的上界
- ub：决策变量的下界
- codes：表示使用二进制编码或格雷编码 
  - codes[i] = 0 表示第i个变量使用的是标准二进制编码
  - codes[i] = 1 表示第i个变量使用格雷编码
- scales：每个子串用的是算术刻度还是对数刻度
  - scales[i] = 0 为算术刻度
  - scales[i] = 1 为对数刻度
- lbin：变量是否包含范围的边界（上边界）
- ubin：变量是否包含范围的边界（下边界）
- varTypes：决策变量的类型
  - 0 表示离散变量
  - 1 表示连续变量

<img src="http://store.secretcamp.cn/uPic/image-202104081659093522021040816590916178723495XjWtG5XjWtG.png" alt="image-20210408165909352" style="zoom:50%;" />

以下是一个描述三个变量的译码矩阵



### FieldDR

对于实值编码（RI、P编码）的种群，使用 3 行 n 列的矩阵来作为译码矩阵，n 是染色体所表达的控制变量个数

<img src="http://store.secretcamp.cn/uPic/image-20210408170547129202104081705471617872747h44JIsh44JIs.png" alt="image-20210408170547129" style="zoom:50%;" />

对于排列编码（P编码），由于编码方式是离散的，要求 FieldDR 的第一行所有元素都相等，第二行所有元素也都相等，且第三行元素均为 1 。

<img src="http://store.secretcamp.cn/uPic/image-20210408171032865202104081710331617873033vGZw2CvGZw2C.png" alt="image-20210408171032865" style="zoom:50%;" />

上图若是作为排列编码种群的译码矩阵，则表示种群染色体是由集合 {2, 3, 4, 5, 6, 7, 8, 9, 10} 中任意抽取 7 个数出来的全排列。

图中有两个个体。

<img src="http://store.secretcamp.cn/uPic/image-20210408171606798202104081716061617873366q9ofMxq9ofMx.png" alt="image-20210408171606798" style="zoom:50%;" />



## 进化追踪器 pop_trace

进化追踪器用来记录种群在进化的过程中各代的最优个体，尤其是采用无精英保留机制时，进化追踪器帮助我们记录种群在进化的历史中产生过的最优个体。待进化完成后，再从进化追踪器中挑选出历史最优的个体。

<img src="http://store.secretcamp.cn/uPic/image-20210408171815282202104081718151617873495TZtXDXTZtXDX.png" alt="image-20210408171815282" style="zoom: 43%;" />









# 核心类

进化算法模板是 Geatpy 进化算法框架的核心。进化算法的核心流程是部体现在进化算法模板类中。



## Algorithm

`Algorithm` 是算法类的顶级父类，各模板类则继承了 `SoraAlgorithm` 或者 `MoraAlgorithm`

<img src="http://store.secretcamp.cn/uPic/image-20210410171459318202104101714591618046099EfApk4EfApk4.png" alt="image-20210410171459318" style="zoom: 50%;" />



```python
属性:
    name            : str      - 算法名称（可以自由设置名称）。
    
    problem         : class <Problem> - 问题类的对象。

    population      : class <Population> - 种群对象。
    
    MAXGEN          : int      - 最大进化代数。
    
    currentGen      : int      - 当前进化的代数。
    
    MAXTIME         : float    - 时间限制（单位：秒）。
    
    timeSlot        : float    - 时间戳（单位：秒）。
    
    passTime        : float    - 已用时间（单位：秒）。
    
    MAXEVALS        : int      - 最大评价次数。
    
    evalsNum        : int      - 当前评价次数。
    
    MAXSIZE         : int      - 最优个体的最大数目。
    
    logTras         : int      - Tras即周期的意思，该参数用于设置在进化过程中每多少代记录一次日志信息。
                                 设置为0表示不记录日志信息。
                                 注：此时假如设置了“每10代记录一次日志”而导致最后一代没有被记录，
                                     则会补充记录最后一代的信息，除非找不到可行解。

    log             : Dict     - 日志记录。其中包含2个基本的键：'gen'和'eval'，其他键的定义由该算法类的子类实现。
                                 'gen'的键值为一个list列表，用于存储日志记录中的每一条记录对应第几代种群。
                                 'eval'的键值为一个list列表，用于存储进化算法的评价次数。
                                 注：若设置了logTras为0，则不会记录日志，此时log会被设置为None。
    
    verbose         : bool     - 表示是否在输入输出流中打印输出日志信息。

```



```python
函数:
    __init__()       : 构造函数，定义一些属性，并初始化一些静态参数。

    initialization() : 在进化前对算法模板的一些动态参数进行初始化操作，具体功能需要在继承类中实现。
    
    run()            : 执行函数，具体功能需要在继承类中实现。

    logging()        : 用于在进化过程中记录日志，具体功能需要在继承类中实现。

    stat()           : 用于分析当代种群的信息，具体功能需要在继承类中实现。

    terminated()     : 计算是否需要终止进化，具体功能需要在继承类中实现。

    finishing ()     : 进化完成后调用的函数，具体功能需要在继承类中实现。

    check()          : 用于检查种群对象的ObjV和CV的数据是否有误。

    call_aimFunc()   : 用于调用问题类中的aimFunc()进行计算ObjV和CV(若有约束)。

    display()        : 用于在进化过程中进行一些输出，需要依赖属性verbose和log属性。
```



### SoeaAlgorithm

单目标进化优化算法模板的父类，所有单目标优化算法模板均继承自该父类。

对比于父类该类新增的变量和函数：

```
		trappedValue    : int  - 进化算法陷入停滞的判断阈值。

    maxTrappedCount : int  - “进化停滞”计数器最大上限值。

    drawing         : int  - 绘图方式的参数，
                             0表示不绘图；
                             1表示绘制进化过程中种群的平均及最优目标函数值变化图；
                             2表示实时绘制目标空间过程动画；
                             3表示实时绘制决策空间动态图。
```



### MoeaAlgorithm

多目标进化优化算法模板的父类，所有多目标优化算法模板均继承自该父类。

对比于父类该类新增的变量和函数:

```
    drawing        : int - 绘图方式的参数，
                           0表示不绘图；
                           1表示绘制最终结果图；
                           2表示实时绘制目标空间动态图；
                           3表示实时绘制决策空间动态图。

    draw()         : 绘图函数。
```



## Problem

`Problem` 类定义了与问题相关的一些信息 



### 属性

    name      : str   - 问题名称（可以自由设置名称）。
    
    M         : int   - 目标维数，即有多少个优化目标。
    
    maxormins : array - 目标函数最小最大化标记的行向量，1表示最小化，-1表示最大化，例如：
                        array([1,1,-1,-1])，表示前2个目标是最小化，后2个目标是最大化。
    
    Dim       : int   - 决策变量维数，即有多少个决策变量。
    
    varTypes  : array - 连续或离散标记，是Numpy array类型的行向量，
                        0表示对应的决策变量是连续的；1表示对应的变量是离散的。
    
    ranges    : array - 决策变量范围矩阵，第一行对应决策变量的下界，第二行对应决策变量的上界。
    
    borders   : array - 决策变量范围的边界矩阵，第一行对应决策变量的下边界，第二行对应决策变量的上边界，
                        0表示范围中不含边界，1表示范围包含边界。
    
    ReferObjV : array - 存储着目标函数参考值的矩阵，每一行对应一组目标函数参考值，每一列对应一个目标函数。



### 方法

```
aimFunc(pop) : 目标函数，需要在继承类即自定义的问题类中实现，或是传入已实现的函数。
               其中pop为Population类的对象，代表一个种群，
               pop对象的Phen属性（即种群染色体的表现型）等价于种群所有个体的决策变量组成的矩阵，
               该函数根据该Phen计算得到种群所有个体的目标函数值组成的矩阵，并将其赋值给pop对象的ObjV属性。
               若有约束条件，则在计算违反约束程度矩阵CV后赋值给pop对象的CV属性（详见Geatpy数据结构）。
               该函数不返回任何的返回值，求得的目标函数值保存在种群对象的ObjV属性中。
               例如：population为一个种群对象，则调用aimFunc(population)即可完成目标函数值的计算，
               此时可通过population.ObjV得到求得的目标函数值，population.CV得到违反约束程度矩阵。

calReferObjV()   : 计算目标函数参考值，需要在继承类中实现，或是传入已实现的函数。
									 常常用待优化问题的理论全局最优解作为该参考值，用于后续进行评价进化结果的好坏。
									 如果并不知道理论全局最优解是什么，可以不设置calReferObjV()函数

getReferObjV()   : 获取目标函数参考值。
```



## Population

种群类（Population） 是一个存储着与种群个体相关信息的类



### 属性

```
sizes    : int   - 种群规模，即种群的个体数目。

ChromNum : int   - 染色体的数目，即每个个体有多少条染色体。

Encoding : str   - 染色体编码方式，
                   'BG':二进制/格雷编码；
                   'RI':实整数编码，即实数和整数的混合编码；
                   'P':排列编码。
                   相关概念：术语“实值编码”包含实整数编码和排列编码，
                   它们共同的特点是染色体不需要解码即可直接表示对应的决策变量。
                   "实整数"指的是种群染色体既包含实数的小数，也包含实数的整数。
                   特殊用法：
                   设置Encoding=None，此时种群类的Field,Chrom成员属性将被设置为None，
                   种群将不携带与染色体直接相关的信息，可以减少不必要的数据存储，
                   这种用法可以在只想统计非染色体直接相关的信息时使用，
                   尤其可以在多种群进化优化过程中对个体进行统一的适应度评价时使用。

Field    : array - 译码矩阵，可以是FieldD或FieldDR（详见Geatpy数据结构）。

Chrom    : array - 种群染色体矩阵，每一行对应一个个体的一条染色体。

Lind     : int   - 种群染色体长度。

ObjV     : array - 种群目标函数值矩阵，每一行对应一个个体的目标函数值，每一列对应一个目标。

FitnV    : array - 种群个体适应度列向量，每个元素对应一个个体的适应度，最小适应度为0。

CV       : array - CV(Constraint Violation Value)是用来定量描述违反约束条件程度的矩阵，每行对应一个个体，每列对应一个约束。
                   注意：当没有设置约束条件时，CV设置为None。

Phen     : array - 种群表现型矩阵（即种群各染色体解码后所代表的决策变量所组成的矩阵）。
```



### 方法

```
initChrom(NIND=None)：void：初始化种群染色体矩阵

decoding()：Phen（表现型类）：种群染色体解码

copy()：return Population：种群的复制

shuffle()：打乱种群个体的个体顺序

save(dirName='Result'))：void：该函数将在字符串 dirName 所指向的文件夹下保存种群的信息
```



## PsyPopulation

多种群类（PsyPopulation）



# 进化算法模板

以 soea 开头的是 “单目标” 进化算法模板

以 moea 开头的是 “多目标” 进化算法模板

带 psy 字样的是 “多染色体” 进化算法模板

带 multi 字样的是 “多种群” 进化算法模板

带 DE 字样的是 “差分” 进化模板





## soea_SGA_templet

soea_SGA_templet : class - Simple GA templet（最简单、最经典的遗传算法模板）

本模板实现的是最经典的单目标遗传算法。算法流程如下：

1) 根据编码规则初始化N个个体的种群。

2) 若满足停止条件则停止，否则继续执行。

3) 对当前种群进行统计分析，比如记录其最优个体、平均适应度等等。

4) 独立地从当前种群中选取N个母体。

5) 独立地对这N个母体进行交叉操作。

6) 独立地对这N个交叉后的个体进行变异，得到下一代种群。

7) 回到第2步。



```python
import geatpy as ea
from sys import path as paths
from os import path

paths.append(path.split(path.split(path.realpath(__file__))[0])[0])


class soea_SGA_templet(ea.SoeaAlgorithm):

    def __init__(self, problem, population):
      # 先调用父类构造方法
        ea.SoeaAlgorithm.__init__(self, problem, population)  
        if population.ChromNum != 1:
            raise RuntimeError('传入的种群对象必须是单染色体的种群类型。')
        self.name = 'SGA'
        self.selFunc = 'rws'  # 轮盘赌选择算子
        if population.Encoding == 'P':
            self.recOper = ea.Xovpmx(XOVR=0.7)  # 生成部分匹配交叉算子对象
            self.mutOper = ea.Mutinv(Pm=0.01)  # 生成逆转变异算子对象
        else:
            self.recOper = ea.Xovdp(XOVR=0.7)  # 生成两点交叉算子对象
            if population.Encoding == 'BG':
                # 生成二进制变异算子对象，Pm设置为None时，具体数值取变异算子中Pm的默认值
                self.mutOper = ea.Mutbin(Pm=None)  
            elif population.Encoding == 'RI':
              	# 生成breeder GA变异算子对象
                self.mutOper = ea.Mutbga(Pm=1 / self.problem.Dim, MutShrink=0.5, Gradient=20)  
            else:
                raise RuntimeError('编码方式必须为''BG''、''RI''或''P''.')

    def run(self, prophetPop=None):  # prophetPop为先知种群（即包含先验知识的种群）
        # ==========================初始化配置===========================
        population = self.population
        NIND = population.sizes
        self.initialization()  # 初始化算法模板的一些动态参数
        # ===========================准备进化============================
        population.initChrom(NIND)  # 初始化种群染色体矩阵
        self.call_aimFunc(population)  # 计算种群的目标函数值
        # 插入先验知识
        # 注意：这里不会对先知种群prophetPop的合法性进行检查
        # 故应确保 prophetPop 是一个种群类且拥有合法的 Chrom、ObjV、Phen 等属性
        if prophetPop is not None:
            population = (prophetPop + population)[:NIND]  # 插入先知种群
        # 计算适应度
        population.FitnV = ea.scaling(population.ObjV, population.CV, self.problem.maxormins)  
        # ===========================开始进化============================
        while self.terminated(population) == False:
            # 选择
            population = population[ea.selecting(self.selFunc, population.FitnV, NIND)]
            # 进行进化操作
            # 重组
            population.Chrom = self.recOper.do(population.Chrom) 
            # 变异
            population.Chrom = self.mutOper.do(population.Encoding, population.Chrom, population.Field)  
            # 计算目标函数值
            self.call_aimFunc(population)  
            # 计算适应度
            population.FitnV = ea.scaling(population.ObjV, population.CV, self.problem.maxormins)  
        return self.finishing(population)  # 调用finishing完成后续工作并返回结果

```



## soea_SEGA_templet

```python
# -*- coding: utf-8 -*-
import geatpy as ea  # 导入geatpy库
from sys import path as paths
from os import path

paths.append(path.split(path.split(path.realpath(__file__))[0])[0])


class soea_SEGA_templet(ea.SoeaAlgorithm):
    """
soea_SEGA_templet : class - Strengthen Elitist GA templet(增强精英保留的遗传算法模板)

算法描述:
    本模板实现的是增强精英保留的遗传算法。算法流程如下：
    1) 根据编码规则初始化N个个体的种群。
    2) 若满足停止条件则停止，否则继续执行。
    3) 对当前种群进行统计分析，比如记录其最优个体、平均适应度等等。
    4) 独立地从当前种群中选取N个母体。
    5) 独立地对这N个母体进行交叉操作。
    6) 独立地对这N个交叉后的个体进行变异。
    7) 将父代种群和交叉变异得到的种群进行合并，得到规模为2N的种群。
    8) 从合并的种群中根据选择算法选择出N个个体，得到新一代种群。
    9) 回到第2步。
    该算法宜设置较大的交叉和变异概率，否则生成的新一代种群中会有越来越多的重复个体。
    
"""

    def __init__(self, problem, population):
        ea.SoeaAlgorithm.__init__(self, problem, population)  # 先调用父类构造方法
        if population.ChromNum != 1:
            raise RuntimeError('传入的种群对象必须是单染色体的种群类型。')
        self.name = 'SEGA'
        self.selFunc = 'tour'  # 锦标赛选择算子
        if population.Encoding == 'P':
            self.recOper = ea.Xovpmx(XOVR=0.7)  # 生成部分匹配交叉算子对象
            self.mutOper = ea.Mutinv(Pm=0.5)  # 生成逆转变异算子对象
        else:
            self.recOper = ea.Xovdp(XOVR=0.7)  # 生成两点交叉算子对象
            if population.Encoding == 'BG':
                # 生成二进制变异算子对象，Pm设置为None时，具体数值取变异算子中Pm的默认值
                self.mutOper = ea.Mutbin(Pm=None)  
            elif population.Encoding == 'RI':
                # 生成breeder GA变异算子对象
                self.mutOper = ea.Mutbga(Pm=1 / self.problem.Dim, MutShrink=0.5, Gradient=20)  
            else:
                raise RuntimeError('编码方式必须为''BG''、''RI''或''P''.')

    def run(self, prophetPop=None):  # prophetPop为先知种群（即包含先验知识的种群）
        # ==========================初始化配置===========================
        population = self.population
        NIND = population.sizes
        self.initialization()  # 初始化算法模板的一些动态参数
        # ===========================准备进化============================
        # 初始化种群染色体矩阵
        population.initChrom(NIND)  
        # 计算种群的目标函数值
        self.call_aimFunc(population)  
        # 插入先验知识（注意：这里不会对先知种群prophetPop的合法性进行检查，故应确保prophetPop是一个种群类且拥有合法的Chrom、ObjV、Phen等属性）
        if prophetPop is not None:
            population = (prophetPop + population)[:NIND]  # 插入先知种群
        population.FitnV = ea.scaling(population.ObjV, population.CV, self.problem.maxormins)  # 计算适应度
        # ===========================开始进化============================
        while self.terminated(population) == False:
            # 选择
            offspring = population[ea.selecting(self.selFunc, population.FitnV, NIND)]
            # 进行进化操作
            # 重组
            offspring.Chrom = self.recOper.do(offspring.Chrom)  
            # 变异
            offspring.Chrom = self.mutOper.do(offspring.Encoding, offspring.Chrom, offspring.Field)  
            self.call_aimFunc(offspring)  # 计算目标函数值
            population = population + offspring  # 父子合并
            # 计算适应度
            population.FitnV = ea.scaling(population.ObjV, population.CV, self.problem.maxormins)  
            # 得到新一代种群
            # 采用基于适应度排序的直接复制选择生成新一代种群
            population = population[ea.selecting('dup', population.FitnV, NIND)]  
        return self.finishing(population)  # 调用finishing完成后续工作并返回结果

```



# core

"core" 文件夹里面全部为 Geatpy 工具箱的内核函数

"operators" 中存放着面向对象的重组和变异算子类，这些重组和变异算子类通过调用 "core" 文件夹下的重组和变异算子来执行相关的操作。

<img src="http://store.secretcamp.cn/uPic/image-20210410183601165202104101836011618050961c7HPlxc7HPlx.png" alt="image-20210410183601165" style="zoom:50%;" />



## 初始化种群染色体

- crtfld (生成译码矩阵，俗称“区域描述器”)

- crtbp (创建二进制种群染色体矩阵)

- crtip (创建元素是整数的种群染色体矩阵)

- crtpp (创建排列编码种群染色体矩阵)

- crtrp (创建元素是实数的种群染色体矩阵)



## 进化迭代

当完成了种群染色体的初始化后，就可以进行进化迭代了。这部分是在进化算法模 板里调用。迭代过程中包括：

- 调用 ranking 或 scaling 等计算种群适应度。

- 调用 selecting 进行选择操作 (也可以直接调用低级选择函数)。

- 调用 recombin 进行重组操作 (也可以直接调用低级重组函数)。

- 调用 mutate 进行重组操作 (也可以直接调用低级变异函数)。



## 适应度计算

- ranking (基于等级划分的适应度分配计算)

- scaling (线性尺度变换适应度计算)

- indexing (指数尺度变换适应度计算)

- powing (幂尺度变换适应度计算) 对于多目标进化优化，由于各种多目标优化算法所采用的适应度计算方法门类有很多，因此此时的适应度计算交由继承了 `Algorithm` 的具体算法模板类中实现，详见相关源码。



## 选择

selecting 和 mselecting 是高级选择函数，它们调用下面的低级选择函数。其中 selecting 是用于单一种群的个体选择，而 mselecting 是用于多种群个体的选择。

- dup (Duplication，基于适应度排序的直接复制选择)

- ecs (Elite Copy Selection，精英复制选择)

- etour (精英保留锦标赛选择)

- otos (One-to-One Survivor Selection，一对一生存者选择)

- rcs (Random Compensation Selection，随机补偿选择)

- rps (Random Permutation Selection，随机排列选择)

- rws (Roulette Wheel Selection，轮盘赌选择)

- sus (Stochastic Universal Sampling，随机抽样选择)

- tour (Tournament，锦标赛选择)

- urs (Uncommitted Random Selection，无约束随机选择)

可在 Python 中通过 `import geatpy as ea` ，然后 `help(ea.模块名)` 查看各模块的用法。



## 重组

重组包括了交叉。recombin 是高级的重组函数，它调用下面的低级重组函数：

- recdis (离散重组)

- recint (中间重组)

- reclin (线性重组)

- recndx (正态分布交叉)

- recsbx (模拟二进制交叉)

- xovbd (二项式分布交叉)

- xovdp (两点交叉)

- xovexp (指数交叉)

- xovmp (多点交叉)

- xovox (顺序交叉)

- xovpmx (部分匹配交叉)

- xovsec (洗牌指数交叉)

- xovsh (洗牌交叉)

- xovsp (单点交叉)

- xovud (均匀分布交叉)

可在 Python 中通过 `import geatpy as ea` ，然后 `help(ea.模块名)` 查看各模块的用法。





## 突变

mutate 是高级的突变函数，它调用下面的低级突变函数：

mutbga (Mutation for Breeder Genetic Algorithm，Breeder GA 算法突变算子)

mutbin (Mutation for Binary Chromosomes，二进制变异算子)

mutde (Mutation for Differential Evolution，差分变异算子)

mutgau (Gaussian Mutation，高斯突变算子)

mutinv (Invertion Mutation，染色体片段逆转变异算子)

mutmove (Mutation by Moving，染色体片段移位变异算子)

mutpolyn (Polynomial Mutation，多项式变异)

mutpp (Mutation of Permutation Chromosomes，排列编码变异算子)

mutswap (Two Point Swapping Mutation，染色体两点互换变异算子)

mutuni (Uniform Mutation，均匀变异算子)

可在 Python 中通过 `import geatpy as ea` ，然后 `help(ea.模块名)` 查看各模块的用法。



## 染色体解码

对于二进制/格雷编码的染色体，要对其进行解码才能得到其表现型。如果想 从表现型得到二进制/格雷编码的染色体，则要调用编码函数进行编码。

bs2int (二进制/格雷码转整数)

bs2real (二进制/格雷码转实数)

bs2ri (二进制/格雷码转实整数)

ri2bs (实整数转二进制/格雷码)





## 可视化

- moeaplot (多目标优化目标空间绘图函数)

- soeaplot (单目标优化绘图函数)

- trcplot (进化记录器绘图函数)

- varplot (决策变量绘图函数)



## 多目标相关

- awGA (适应性权重法多目标聚合函数)

- rwGA (随机权重法多目标聚合函数)

- pbi (基于惩罚的边界交叉法多目标聚合函数)

- tcheby (切比雪夫法多目标聚合函数)

- ndsortDED (基于排除法的帕累托层级划分)

- ndsortESS (基于 ENS_SS 的快速非支配层级划分)

- ndsortTNS (基于 T_ENS 的快速非支配层级划分)

- crtidp (生成多目标空间的理想点)

- crtgp (创建在单位超空间中均匀的网格点集)

- crtup (创建在单位超平面内均匀分布的点集)

- crowdis (拥挤距离计算)

- refgselect (利用参考点引导的个体选择)

- refselect (基于参考点的“入龛”个体选择)



## 约束相关

- mergecv (合并违反约束条件程度矩阵)



## 多种群相关

- migrate (种群间个体迁移)

- mselecting (多种群个体选择)



## 指标评价

评价指标计算函数基本上集中在 indicator 模块中，主要有以下几个评价指标：

- indicator.GD (多目标优化世代距离的计算)

- indicator.IGD (多目标优化反转世代距离的计算)

- indicator.HV (多目标优化超体积指标的计算)

- indicator.Spacing (多目标优化分布性指标 Spacing 的计算)



## 数据可视化





# 算法执行细节

## 算法调用流程

1. 创建一个问题对象，定义问题

   ```python
   problem = MyProblem() 
   ```

   - 定义 `_init_#` 方法，即问题的基本参数 

   - 定义 `aimFunc#` 方法，即设置目标函数，需要将目标函数赋值到种群对象的 ObjV 属性。

     如果使用可行性法则约束，需要赋值到种群对象的 CV 属性。



2. 创建区域描述器，这一步是标准流程

   ```
   Field = ea.crtfld(Encoding, problem.varTypes, problem.ranges, problem.borders)
   ```

   

3. 创建种群对象，并传入相关参数，也是标准流程

   ```
   population = ea.Population(Encoding, Field, NIND)
   ```

   

4. 创建一个算法模板对象，其中定义了算法的具体执行流程

   可以直接对属性赋值，自定义算法的通用参数

   ```python
   myAlgorithm = ea.soea_SEGA_templet(problem, population)
   myAlgorithm.MAXGEN = 1000 # 最大遗传代数
   myAlgorithm.mutOper.F = 0.5 # 设置差分进化的变异缩放因子
   myAlgorithm.recOper.XOVR = 0.5 # 设置交叉概率
   myAlgorithm.drawing = 1 # 设置绘图方式
   ```

   

5. 调用算法模板对象的 `run#` 方法，开始进化过程

   ```python
   [BestIndi, population] = myAlgorithm.run()
   ```

   

6. 执行 `run#` 方法中定义的各种流程

   `run` 方法是算法模板子类独有的方法，主要作用是控制算法流程，例如参数初始化、计算目标函数、进化（选择、交叉、变异），循环 N 代后返回计算结果。

   

## 执行流程

1. 执行具体算法模板的 `run#` 方法，此时种群对象仅仅被创建，其中的个体还没有进行初始化

2. 调用 `population.initChrom#` 初始化染色体，主要操作是调用 `ea.crtpc#` 创建初始染色体型
3. 调用 `self.call_aimFunc#` 计算种群的目标函数值，主要操作是调用问题类的 `aimFunc#` 方法，在该方法中，取得整个种群的所有表现型，经过自定义计算后将计算结果赋值到种群的 ObjV 属性

4. 调用 `ea.scaling#` 计算适应度
5. 进化，`terminated#` 方法用于检查进化的状态
   - 变异
   - 重组
   - 计算目标函数值
   - 计算适应度
   - 选择



# 面向对象编程

## 问题描述

继承 `Problem` ，完成对问题的描述

`aimFunc() `为待优化的目标函数，需要一个 pop 参数，该参数是一个 `Population` 类的对象，代表着一个种群。

