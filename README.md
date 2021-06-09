人工智能大作业五子棋弈棋系统**一、博弈树搜索**

博弈就是相互采取最优策略斗争的意思。所谓博弈树搜索就是基于目前的状态，系统根据博弈规则计算搜索，未来所有可能出现的状态，并且选择最优的状态。也就是说一个完整的博弈树会有一个起始节点，代表博弈中某一个情形，接着下一层的子节点是原来父节点博弈下一步的各种可能性，依照这规则扩展直到博弈结束。下图为一颗基于井字棋衍生出来的博弈树。

![img](file:///C:/Users/86151/AppData/Local/Temp/msohtmlclip1/01/clip_image002.png)

图 1井字棋博弈树

上图的起始节点为空棋盘，在此基础上下一层的子节点都是根据父节点博弈出来的所有可能的状态。可以看出随着层数的增加，子节点的数量增长速率及其可怕。而由于子节点的数量还与博弈决策的数量有关，也就是说在五子棋的情况下，子节点的数量将会增长的更快。所以由此我们需要一个极大极小值搜索，来知道根据目前状态，下一步如何得到最优的状态。

二、**极大极小值搜索**

对于一个固定的棋局，判断其对于落子方是占优势还是劣势，以及下一步如何走出最优解，就需要有相关的数值对棋局进行描述。所以我们对棋局需要由一个评估函数。而对于极其有利的棋局形势就需要有一个极高的估值，而对于不利的棋局形式则只需要一个比较小的估值。这样使搜索函数每次搜索最大的估值即可得到下一步的最优解。所以在极大极小值搜索这部分比较重要的的就是设计一个评估函数。

评估函数设计：对于五子棋的评估函数设计首先需要了解几种基本的棋型。

首先给出一些术语的介绍：

①成五：五颗同色棋子连在一起

![img](file:///C:/Users/86151/AppData/Local/Temp/msohtmlclip1/01/clip_image003.png)

图 2成五

 

②活四：有两个点可以形成五

![img](file:///C:/Users/86151/AppData/Local/Temp/msohtmlclip1/01/clip_image004.png)

图 3活四

③冲四：只有一个点能够形成五， 如下三种棋型称作冲四。

![img](file:///C:/Users/86151/AppData/Local/Temp/msohtmlclip1/01/clip_image005.png)           ![img](file:///C:/Users/86151/AppData/Local/Temp/msohtmlclip1/01/clip_image006.png)

图 4冲四3                                  图 5冲四2

![img](file:///C:/Users/86151/AppData/Local/Temp/msohtmlclip1/01/clip_image007.png)

图 6冲四3

④活三：可以形成活四的三个棋子，如下两种棋形都为活三。中间跳着一格的活三，也可以叫做跳活三，另一种叫做单活三。单活三比跳活三更难防守，也就是估值应该更大。

![img](file:///C:/Users/86151/AppData/Local/Temp/msohtmlclip1/01/clip_image008.png)        ![img](file:///C:/Users/86151/AppData/Local/Temp/msohtmlclip1/01/clip_image010.jpg)

图 7单活三                                  图 8跳活三

⑤眠三：只能够形成冲四的三。最基础的眠三有六种，此处只给出一种图。

![img](file:///C:/Users/86151/AppData/Local/Temp/msohtmlclip1/01/clip_image011.png)

图 9眠三

⑥活二：可以形成活三的两个棋子。只有两子相连即可称其为活二。

 

熟悉了以上基础棋形以后，根据每种棋形的对棋局的优劣程度给出以下的分数表。

表格 1基础棋形估值

| **术语** | **得分**   |
| -------- | ---------- |
| 成五     | 99999999分 |
| 活四     | 100000分   |
| 冲四     | 10000分    |
| 活三     | 5000分     |
| 眠三     | 500分      |
| 活二     | 50分       |

 

根据该表给出的得分，便可设计出评估函数。只需要棋局上所有匹配的棋形然后累加其得分，这个分数的统计便是评估函数。对与本系统中下一步该走哪只需要计算它走下一步的点以后计算局面的得分，根据博弈树搜索原理取分数最大值的那个点即可，这就是极大值搜索。而真正的对局中，只考虑下一个点的最优解是远远不够的，对方一定会在你得分最小的点下，这就是对方的最优解，这就是极小值搜索。考虑到计算机算力的情况下，通常使用计算三步后获得的最大分数。即搜索下一步该落在哪个点的情况下三步后得分最大。

三步搜索的过程即假设当前为A执棋并获得了最大值，然后B执棋选择了A的极小值,A在第三步时又获得了自己的最大值。将这三步看作一个整体，计算怎么获得三步后的最大值即是三步的极大值极小值搜索。

如下图：

![img](file:///C:/Users/86151/AppData/Local/Temp/msohtmlclip1/01/clip_image013.jpg)

图 10 三步情况下的极大值极小值搜索

 

根据以上的思路可以得到以下极大极小值搜索的伪代码[4]，这里仅是伪代码，适用于任何深度的极大极小值搜索。

**function** minimax(node, depth)

  **if** node is a terminal node or depth = 0

​    **return** the heuristic value of node

  **if** the adversary is to play at node

​    let α := +∞

​    foreach child of node

​      α := min(α, minimax(child, depth-1))

  **else** {we are to play at node}

​    let α := -∞

​    foreach child of node

​      α := max(α, minimax(child, depth-1))

**return** α

根据以上伪代码完成了极大极小值搜索的代码以后，一个弈棋系统的雏形已经完成了，剩下需要解决的问题是在真正追求必胜的情况下，五子棋必须做到搜索深度较大的极大极小值搜索。对于较大深度的搜索，家庭电脑很容易陷入死机，所以这里需要一个Alpha-Beta的剪枝搜索。

 

**三、****Alpha-Beta****剪枝**

   Alpha-beta的优点是减少搜索树的分枝，将搜索时间用在“更有希望”的子树上，继而提升搜索深度。把Alpha-Beta剪枝应用到极大极小搜索或者负极大值搜索中，就形成了Alpha-Beta搜索。假设在某种情况下节点搜索顺序达到最优化或近似最优化（将最佳选择排在各节点首位）的时候，则在相同时间内经过Alpha-Beta剪枝的搜索深度可达极小化极大算法的两倍多。在（平均或恒定）分枝因子为![img](file:///C:/Users/86151/AppData/Local/Temp/msohtmlclip1/01/clip_image015.png)，搜索深度为![img](file:///C:/Users/86151/AppData/Local/Temp/msohtmlclip1/01/clip_image017.png)层的情况下，要评估的最大叶节点数目为![img](file:///C:/Users/86151/AppData/Local/Temp/msohtmlclip1/01/clip_image019.png)——即和简单极小化极大搜索一样。则需要评估的最大叶节点数目按层数奇偶性，分别约为![img](file:///C:/Users/86151/AppData/Local/Temp/msohtmlclip1/01/clip_image021.png)和![img](file:///C:/Users/86151/AppData/Local/Temp/msohtmlclip1/01/clip_image023.png)（或![img](file:///C:/Users/86151/AppData/Local/Temp/msohtmlclip1/01/clip_image025.png))。其中层数为偶数时，搜索因子相当于减少了其平方根，等于能以同深度搜索两次。[5], ![img](file:///C:/Users/86151/AppData/Local/Temp/msohtmlclip1/01/clip_image027.png)代表对第一名玩家必须搜索所有的招法来找到最佳招式，但对于它们，只用将第二名玩家的最佳招法截断——Alpha-Beta确保无需考虑第二名玩家的其他招法。但因节点生成顺序随机，实际需要评估的节点平均约为![img](file:///C:/Users/86151/AppData/Local/Temp/msohtmlclip1/01/clip_image029.png)。[6]

Alpha-Beta算法一般使用两个值，alpha和beta，分别代表棋局中所能代表的双方玩家能放心的最大分和最小分。alpha和beta的初始值分别为正负无穷大，在棋局开始时双方都是以最低分开始游戏。在搜索过程中选择了某个节点的特定分支时，可能会出现当前棋局劣势玩家放心的最小分小于优势玩家放心的最大分(beta<=alpha）的情况。这种情况下，显然父节点不应该去选择这个节点，否则父节点分数会降低，因此该分支的其他节点没有必要继续探索。这就是Alpha-Beta的原理。

如图11为一段剪枝图示：设α和β双方玩家能放心的最大分和最小分，将其初始化为-∞ 和+∞。搜索到D的时候，局面得分是5，那么也就是说要搜索最大值，那么只可能会在（5，+∞） 之间， 同理，要搜索最小值，也只会在（-∞，5）之间。 继续搜索， 搜索到G时，F暂时等于6 ，F是要找最大值， 那么F不可能再小于6了， 而B此时要找最小值，B的已知最小值是在（-∞，5）之间的， F在不可能比6小的情况下，F分支的其他节点就没必要继续搜索了。这就是剪枝。

![img](file:///C:/Users/86151/AppData/Local/Temp/msohtmlclip1/01/clip_image031.jpg)

图 11搜索剪枝

下面为一有限可靠性版本的Alpha-Beta剪枝的伪代码[5]

```
function alphabeta(node, depth, α, β, maximizingPlayer) // node = 节点，depth = 深度，maximizingPlayer = 大分玩家
     if depth = 0 or node是终端節點
          return 節點的啟發值
      if maximizingPlayer
          v := -∞
          for 每个子節點
              v := max(v, alphabeta(child, depth - 1, α, β, FALSE)) // child = 子節點
              α := max(α, v)
              if β ≤ α
                  break // β裁剪
          return v
      else
          v := ∞
          for 每个子節點
              v := min(v, alphabeta(child, depth - 1, α, β, TRUE))
              β := min(β, v)
              if β ≤ α
                  break // α裁剪
          return v
初始調用：
alphabeta(origin, depth, -∞, +∞, TRUE) // origin = 初始節點
```

在这个有限可靠性的Alpha-Beta的伪代码中，v不能超出参数α和β构建的集合。

 

**四、负值极大法**

由于极小极大搜索的目的是站在自己的角度上获取最大值。为了让对方的值在自己这边有一个表示，则需要在对方的最大值上加一个负号，然后就变成了相对于自己的最大值。

以下为获取自己最大值的而确定下一步走法的函数伪代码：

```
function negamax(GameState S, depth, alpha, beta) {
    // 游戏是否结束 || 探索的递归深度是否到边界
    if ( gameover(S) || depth == 0 ) {
        return evaluation(S);
    }
    // 遍历每一个候选步
    foreach ( move in candidate list ) {
        S' = makemove(S);
        value = -negamax(S', depth - 1, -beta, -alpha);
        unmakemove(S') 
        if ( value > alpha ) {
            // alpha + beta剪枝点
            if ( value >= beta ) {
                return beta;
            }
            alpha = value;
        }
    }
    return alpha;
}
```

**五、具体实现**

由于篇幅有限，这里只放一部分实现的流程图展示。（完整代码见附件）

在熟悉了以上机器博弈系统的基本原理后，具体只要根据上述三个伪代码实现整个五子棋算法即可。

基于博弈树的五子棋走法生成部分算法流程图：

![img](file:///C:/Users/86151/AppData/Local/Temp/msohtmlclip1/01/clip_image033.png)

​									图 12五子棋走法生成算法流程图

根据上述流程图实现了源码以后借助了网上提供的开源模板实现了五子棋可视的可视化界面，由于时间仓促，在很多可以优化时间的地方都未做出优化，经测试最终源码在搜索深度大于3的情况下，很难实现与人实时对弈。

 

**六、系统分析：**

​    1、功能分析

​    （1）可以实现实时人机对弈，界面操作简单，根据鼠标定向选择落子位置。

（2）可以设置搜索深度，以调节五子棋系统的智能等级。

（3）在机器选择落子时在控制台打印出具体搜索次数以及经剪枝优化的搜索次数。

（4）在一方根据五子棋规则胜利时给出提示。

2、开发语言

本系统采用人工智能常用的Python语言完成，Python语言在对算法实现上有很大的优势，语法结构清晰。其在人工智能领域应用极多，所以在此选择Python语言实现。试最终源码在搜索深度大于3的情况下，很难实现与人实时对弈。