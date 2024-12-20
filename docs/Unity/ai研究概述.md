---
title: ai研究概述
date: 2024-05-03 19:27:36
categories: 技术
tags: 
  - unity
  - AI
hidden: true
---

## 参考资料：

[AI反拆：只狼狮子猿 | 设计者笔记 (jskyzero.com)](https://design.jskyzero.com/2023/10/10/sekiro_lion_tamarin_AI/)

[如何设计有“对话感”的BOSS？《只狼》Ai完全拆解。 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/614731111)

[HTN框架介绍、开发心得-CSDN博客](https://blog.csdn.net/jk_chen_acmer/article/details/118894596)

[游戏AI研究（一）：感知AI - GameRes游资网](https://www.gameres.com/830335.html)

[FSM（状态机）、HFSM（分层状态机）、BT（行为树）的区别_对战状态机-CSDN博客](https://blog.csdn.net/qq_39885372/article/details/103950973)

[行为树入门教程 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/463182588)

[游戏开发技术杂谈14：理解行为树 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/675093070)

https://robohub.org/introduction-to-behavior-trees/

https://zhuanlan.zhihu.com/p/463182588

[行为树的理论与实践入门 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/611393851)

[GOAP思路解析与代码框架 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/575072589)

[<游戏开发>怪物AI实现方案-GOAP - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/372718085)

[目标导向的AI系统（GOAP）技术分享 | 登峰造极者，殊途亦同归。 (lfzxb.top)](https://www.lfzxb.top/goal-oriented-action-planning-tech-share/)

[AkiKurisu/AkiGOAP: Modular and high-performed Unity Goal Oriented Action Planner with well designed visual graph editor (github.com)](https://github.com/AkiKurisu/AkiGOAP)

[游戏AI行为决策方法——HTN 教程（附代码与项目） - 技术专栏 - Unity官方开发者社区](https://developer.unity.cn/projects/657fcd52edbc2a002654a6b4)

[Ai Plan (11) Task Network | Yimi的小天地 (yimicgh.top)](https://yimicgh.top/学习笔记/AI-Plan-11-Task-Networks/)

[AI算法--通过实例探讨分层任务网络规划(HTN) - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/597082052)

[HTN简略笔记 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/607593872)

<!--more-->

## 概要：

### 游戏AI是什么？

在以前游戏AI基本上都是程序员预先定义编写好可能发生的行为，而不具有机器学习那种自我学习的特性。这是因为游戏AI主要职责是模拟出智能行为，而并非学习（例如：街道上会避让车的人群，天空中乱飞的小鸟，来回巡逻的守卫，兵线上的小兵...）。而游戏AI往往使用的是以行为树为代表的人工设计方法，而很少应用到机器学习技术。可以说游戏AI是智能的假象。

在游戏中，AI（人工智能）是指模拟和实现游戏角色的智能行为的技术。虽然在过去，游戏AI主要依赖程序员预定义可能发生的行为，但随着技术的发展，现代游戏AI已经涵盖了更多复杂的技术和方法。以下是一些常见的游戏AI架构：

1. **行为树（Behavior Trees）**：行为树是一种流行的游戏AI设计方法，它将角色的行为表示为树结构。游戏开发者可以通过安排节点和连接来控制角色的决策和行为。
2. **状态机（Finite State Machines）**：状态机是一种基本的游戏AI架构，角色可以处于不同的状态，并根据条件或事件转换状态。
3. **路径规划（Pathfinding Algorithms）**：路径规划是指让角色在游戏中找到最佳路径的技术。常见的算法包括A*算法和Dijkstra算法。
4. **博弈树（Minimax Algorithm）**：在许多游戏中，特别是棋类游戏，博弈树是一种用于决策制定的重要技术，通过搜索可能的走法并选择最有利的策略。
5. **机器学习（Machine Learning）**：现代游戏开始采用机器学习技术来改进游戏AI的表现。深度强化学习等技术被应用于让AI角色更加智能和自适应。

## 状态机

状态机模式是一个游戏常用的经典设计模式，常被用作管理一种物体的各种状态（例如管理人物的行走，站立，跳跃等状态）

游戏 AI 选择状态机模式的原因有几个重要的考虑：

1. **简单性和可维护性**：状态机模式非常直观和易于理解。通过将行为划分为不同的状态，并定义状态之间的转换规则，使得代码更易维护和扩展。开发人员可以直观地查看状态之间的转换关系，从而更容易调试和修改行为。
2. **灵活性**：状态机模式提供了对游戏 AI 行为进行动态管理和调整的灵活性。通过修改状态之间的转换规则，可以改变 AI 的行为方式，使得游戏 AI 可以根据不同的情况作出不同的反应。
3. **逻辑分离**：使用状态机模式可以将游戏 AI 的逻辑和状态信息进行分离。这样可以使得 AI 的行为更具可读性，并允许开发人员单独处理状态转换逻辑和状态具体行为逻辑，提高了代码的组织和可维护性。
4. **适用性广泛**：状态机模式适用于各种不同类型的游戏 AI，无论是简单的角色移动AI还是复杂的决策系统。状态机模式是一种通用且有效的设计模式，适用于各种游戏 AI 场景。

### 为什么需要状态机？

假如我们正在开发一款动作游戏，当前的任务是实现根据输入来控制主角的行为——当按下B键时，他应该跳跃。

直观的代码：

```C++
   if (input == PRESS_B) {
        if (!m_isJumping) {
            m_isJumping = true;
            Jump();//跳跃的代码
        }
    }
```

后来我们需要添加更多行为了，所有行为如下：

```C++
 if (input == PRESS_B) {
        //如果在站立时且没在跳跃，则跳跃
        if (!m_isJumping && m_isStanding) {
            m_isJumping = true;
            player.jump();//跳跃的代码
        }
    }
    else if (input == PRESS_DOWN) {
        //如果在跳跃时且没在俯冲，则俯冲
        if (m_isJumping && !m_isDiving) {
            m_isDiving
            player.dive();//俯冲的代码
        }
        //如果没在跳跃
        else if (!m_isJumping) {
            //如果站立时，则蹲下
            if (m_isStanding) {
                m_isStanding = false;
                player.sneak();//蹲下的代码
            }
            //如果蹲下时，则站立
            else {
                m_isStanding = true;
                player.stand();//站立的代码
            }
        }
    }
```

可以看到一堆if-else语句非常复杂，要是添加更多行为，其逻辑结构更加难以维护，而且主角的代码又得重新编译（耦合性大）

一个可行的办法是将这些 **状态&状态切换&状态对应的行为** 封装成类，

![img](https://typorapicturefivuvuv.oss-cn-shanghai.aliyuncs.com/picgo/1409576-20181226091322393-1146430295.png)

代码实现1：

```C++
//状态接口类
class State {
public:
    //处理输入，然后根据输入转换相应的状态
    virtual void handleInput(Player& player,const Input& input) = 0;
};
//站立状态
class StandState : public State {
public:
    void handleInput(Player& player, const Input& input) override{
        if (input == PRESS_B) {
            player.jump();//角色跳跃的代码
            player.setState(JumpState());
        }
        else if (input == PRESS_DOWN) {
            player.sneak();//角色蹲下的代码
            player.setState(SneakState());
        }
    }
};
//跳跃状态
class JumpState : public State {
public:
    void handleInput(Player& player, const Input& input) override {
        if (input == PRESS_DOWN) {
            player.dive();//角色俯冲的代码
            player.setState(DiveState());
        }
    }
};
//下蹲状态
class SneakState : public State {
public:
    void handleInput(Player& player, const Input& input) override {
        if (input == PRESS_DOWN) {
            player.stand();//角色站立的代码
            player.setState(StandState());
        }
    }
};
//俯冲状态
class DiveState : public State {
public:
    void handleInput(Player& player, const Input& input) override {
    }
};
```

代码实现2：

~~~C#
 public enum GameState
{
    //游戏开始
    GameStart,
    Preparation,
    //玩家回合
    PlayerRoundStartResolution,
    PlayerAction,
    PlayerRoundEndResolution,
    //敌人回合
    EnemyRoundStartResolution,
    EnemyAction,
    EnemyRoundEndResolution,
    //战斗结束
    PlayerLose,
    PlayerLoseResolution,
    PlayerWin,
    Reward
}

public interface IState
{
    public void OnEnter();

    public void OnUpdate();

    public void OnExit();
}
~~~

unity的动画状态机就是一个很好的例子

![img](https://typorapicturefivuvuv.oss-cn-shanghai.aliyuncs.com/picgo/1409576-20190207223240946-913199845.jpg)

## 行为树

### 为什么需要行为树？

1. **层次化结构**：行为树以树形结构组织 AI 的行为，具有层次化的特点，从顶部的根节点开始，逐级展开到叶子节点。这种结构使得行为树更容易理解和管理，能够清晰地表达 AI 的行为逻辑。

2. **灵活性**：行为树提供了更灵活的行为组合方式，通过组合不同类型的节点（例如序列节点、选择节点、并行节点等），可以实现丰富多样的行为表达。这种灵活性使得行为树适用于各种复杂的 AI 行为设计需求。

3. **可扩展性**：行为树易于扩展和调整。开发人员可以轻松地添加、修改或删除行为树的节点，而不会影响到其他部分的逻辑。这种特性使得行为树在面对复杂的 AI 行为需求时更加适用。

   ![Unity战斗框架 优化Animator动画状态机排线_战斗动画状态机-CSDN博客](https://typorapicturefivuvuv.oss-cn-shanghai.aliyuncs.com/picgo/20190723100031475.png)

   

相较于状态机，行为树在复杂的 AI 行为设计上具有更多的优势，尤其适用于需要灵活、层次化、可扩展和容错的 AI 行为模型。

行为树是当前最主流的游戏开发中的AI实现方案，和状态机这样概念结构比较简单的模型不同，行为树自带了很多专属于行为树的概念，比如组合、装饰器、任务等。

### 行为树的推导方法论

[游戏开发技术杂谈14：理解行为树 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/675093070)

简而言之，在游戏开发中使用行为树其实就是通过提供一个合理的逻辑表达方式来帮助策划，使得策划可以完全独立于程序进行配置

### 行为树的概念（behavior tree）

1. **行为树是树：**执行时从根结点开始按照指定的顺序遍历，直到到达终结状态
2. **叶子结点都是可执行的行为:**叶子结点会进行具体的操作，可以是一个简单的检测操作，也可以是一个更复杂的操作，结点会返回状态信息(成功，失败，运行中)。
3. **内部结点控制树的遍历：**内部结点会根据孩子结点返回的状态信息，按照特定的规则确定下一个执行的结点。

![img](https://typorapicturefivuvuv.oss-cn-shanghai.aliyuncs.com/picgo/image.png)

1. tick:行为树从根节点开始按照预定义的顺序遍历各个节点并执行他们的行为的过程称为一次tick，会返回**成功(success)**，**失败(failure)**，**运行中(running)**的状态信息给它的父结点。
2. **行为结点(execution node)**：行为树的叶子结点，可以是动作结点(action node)或条件结点(condition node)。对于条件结点(condition node)会在一次tick后立马返回**成功或**失败的状态信息。对于动作结点(action node)则可以跨越多个tick执行，直到到达它的终结状态。一般来说，条件结点用于简单的判断(比如钳子是否打开?)，动作结点用于表示复杂的行为(比如打开房门)。
3. **控制结点(****control node****)：**控制结点是行为树的内部结点，它们定义了遍历其孩子结点的方式。**控制结点的孩子可以是行为结点，也可以是控制结点**。**顺序(Sequence)**，**备选(Fallback)**，**并行(Parallel)这3种类型的控制结点可以有任意数量的孩子结点****，它们的区别在于对其孩子结点的处理方式。而****装饰(Decorator)结点只能有一个孩子结点**，用来对孩子结点的行为进行自定义修改。

### 不同类型的控制结点：

#### 顺序节点

按顺序执行孩子结点直到其中一个孩子结点返回失败状态或所有孩子结点返回成功状态。

![](https://typorapicturefivuvuv.oss-cn-shanghai.aliyuncs.com/picgo/image.png)

#### 选择节点

按顺序执行孩子结点直到其中一个孩子结点返回成功状态或所有孩子结点返回失败状态。一般用来实现角色的选择行为。

![](https://typorapicturefivuvuv.oss-cn-shanghai.aliyuncs.com/picgo/bt_fallback_node.png)

#### 并行节点

“并行执行”所有孩子结点。直到至少M个孩子(M的值在1到N之间)结点返回成功状态或所有孩子结点返回失败状态。

![](https://typorapicturefivuvuv.oss-cn-shanghai.aliyuncs.com/picgo/1.png)

#### 装饰结点

装饰结点：以自定义的方式修改孩子结点的行为。比如Invert类型的装饰结点，可以反转其孩子结点返回的状态信息。为了方便他人理解，应该尽可能使用比较常见的装饰结点。

![](https://typorapicturefivuvuv.oss-cn-shanghai.aliyuncs.com/picgo/2.png)

### 行为树举例说明

有一个角色的行为树，

当它周围没有敌人时，保持巡逻状态；

当它周围有敌人时，前往敌人的位置；

当它靠近敌人，达到攻击的范围时，执行攻击操作；

当敌人被消灭时，周围没有敌人了，回到之前的巡逻状态。

![img](https://typorapicturefivuvuv.oss-cn-shanghai.aliyuncs.com/picgo/v2-a800c1c10fe5e8f13db4ad82f6d126f4_720w.webp)

**行为节点（Action）**

![img](https://typorapicturefivuvuv.oss-cn-shanghai.aliyuncs.com/picgo/v2-541de51410a1c5b8983a2eef32dbedf7_r.jpg)

没有子节点，用以执行具体行为的节点。

**条件节点（Conditional）**

![img](https://typorapicturefivuvuv.oss-cn-shanghai.aliyuncs.com/picgo/v2-4e0c131189696544271fda087c56ea2b_720w.webp)

没有子节点，用以判定条件是否成立的节点。

### 在unity中使用行为树插件：

这边进行样例演示

## GOAP(Goal Orentated Action planning):

### 概述

目标导向型行动计划（简称GOAP）是一种能够轻松呈现给你的代理选择的AI系统，也是帮助你可以无需维持一个庞大且复杂的有限状态机而做出明智的决策的机器。GOAP的一个Action只有在其所有前提条件都得到满足的情况下才能执行，并且每个动作都会以某种方式改变世界的状态。

也就是， 让AI创造他们自己的方法去解决问题，我们提供给它一系列可能的动作作为对这个世界的描述，和每个动作使用的先决条件，和行动带来的影响。AI拥有一个初始状态和他需要达到的目标。有一组目标，AI可以通过优先级或当前状态选择一个。计划系统决定一个动作序列来满足当 前目标，计划出一个像路径一样的能最简单达到目标状态的动作序列。

### GOAP使用场景

所有需要用到复杂AI的地方都可以使用，为什么非要是复杂AI呢，因为简单AI用FSM或行为树足以干净利落的解决问题，就没必要舍近求远了，那么这个复杂和简单用什么来界定呢，我在这里说一下我的标准，如果一个FSM的连线或者切换关系已经让人感觉一团糟，一个行为树的树状图非常庞大（100起），这个时候，你就可以考虑使用GOAP来化解难题了。

### GOAP中的一些概念

WorldState,Condition,Sensor,Action,A*

Action：它由前提条件（PreCondition）和效果（Effect）组成，一般都是键值对，要执行一个Action，需要先检查前提条件是否满足，然后再把效果内容加入到Memory中（可选）

Goal：AI的目标，也是包含一个或多个键值对，会以此为基准使用A*算法展开路径搜索。例如：

Memory：AI的记忆，更新维护AI所认知的世界状态，偏静态。例如：{‘hasMoney’: true}

Sensor：感知者，其本质和Memory一样，但是偏动态，之所以需要他是因为我们很多时候需要做一下逻辑处理才会把世界状态写入Memory，例如，AI在血量小于30%时会寻找掩护物，要处理这个过程，我们需要一个Sensor来订阅AI的血量改变状态，每次改变就判断是否小于30%，如果是，就把Memory中”isInDanger”设置为true。

### 具体示例：

```c#
//Action：{名称：建造Axe}，{先决条件：hasWood = true，hasSteel = true}，{效果：hasAxe = true，hasWood = false，hasSteel = false}，{权重：10}
ReGoapTestsHelper.GetCustomAction(gameObject, "CreateAxe",
    new Dictionary<string, object> {{"hasWood", true}, {"hasSteel", true}},
    new Dictionary<string, object> {{"hasAxe", true}, {"hasWood", false}, {"hasSteel", false}}, 10);
//Action：{名称：砍树}，{先决条件：无}，{效果：hasRawWood = true}，{权重：2}
ReGoapTestsHelper.GetCustomAction(gameObject, "ChopTree",
    new Dictionary<string, object> { },
    new Dictionary<string, object> {{"hasRawWood", true}}, 2);
//Action：{名称：加工原木}，{先决条件：hasRawWood = true}，{效果：hasRawWood = false，hasWood = true}，{权重：5}
ReGoapTestsHelper.GetCustomAction(gameObject, "WorksWood",
    new Dictionary<string, object> {{"hasRawWood", true}},
    new Dictionary<string, object> {{"hasWood", true}, {"hasRawWood", false}}, 5);
//Action：{名称：拥有矿山}，{先决条件：无}，{效果：hasOre = true}，{权重：10}
ReGoapTestsHelper.GetCustomAction(gameObject, "MineOre",
    new Dictionary<string, object> { },
    new Dictionary<string, object> {{"hasOre", true}}, 10);
//Action：{名称：加工矿石}，{先决条件：hasOre = true}，{效果：hasOre = false，hasSteel = ture}，{权重：10}
ReGoapTestsHelper.GetCustomAction(gameObject, "SmeltOre",
    new Dictionary<string, object> {{"hasOre", true}},
    new Dictionary<string, object> {{"hasSteel", true}, {"hasOre", false}}, 10);
//Goal：{名称：拥有Axe}，{目标状态：hasAxe = true}，{优先级：1}
var hasAxeGoal = ReGoapTestsHelper.GetCustomGoal(gameObject, "HasAxeGoal",
    new Dictionary<string, object> {{"hasAxe", true}}, 1);
//Goal：{名称：拥有Axe和矿山}，{目标状态：hasAxe = true，hasOre = true，isGreedy = true}，{优先级：2}
var greedyHasAxeAndOreGoal = ReGoapTestsHelper.GetCustomGoal(gameObject, "GreedyHasAxeAndOreGoal",
    new Dictionary<string, object> {{"hasAxe", true}, {"hasOre", true}, {"isGreedy", true}},
    2);
```

![image-20210916152957981](https://myfirstblog.oss-cn-hangzhou.aliyuncs.com/typoraImages_3/20210916152958.png!webp)

那么，我们现在开始进行规划，现在有两个目标，而GoalZ的优先级大于GoalY，所以会先把GoalZ纳入规划目标，但是我们找不到“贪婪的”这一状态，所以这个目标规划失败了，然后去尝试规划GoalY，它的规划结果如下

即：ActionA->ActionC->ActionB->ActionD->ActionE

这似乎与我们所想的ActionA->ActionB->ActionC->ActionD->ActionE结果不同，这是因为ReGoap中使用的A*是从终点->起点，而不是起点->终点的，这就会导致它只可以保证综合结果的Cost一定是所有规划里最小的，但在单次规划中并不会确定性采用权值较小的Action为下一结点，这样做是有考虑的，因为如果是从起点到终点进行的规划，就会多出非常多的可能性，更多的可能性意味着更多的计算，这对于要求低延迟的游戏造成的后果是不可接受的。

### 与行为树和状态机的对比

**缺点**

- GOAP学习成本偏高
- GOAP默认不保证Action顺序，如果我们想要强制指定顺序，需要额外做一些操作，但是一般而言我们的AI也不会去在意这种无关紧要的顺序，因为重要的顺序我们已经规范好了。
- GOAP毕竟使用了运行时规划，会对游戏性能产生一定的影响，但是这种目标导向的A*规划在AI复杂的情况下，性能并不一定比FSM/行为树弱。

**优点**

- 解耦目标和行为，设计时可以更加专注的设计AI的行为不用过多考虑目标，新增，减少Action非常方便，不会像FSM/行为树那样具有那么强的入侵性和破坏性，提高了开发效率
- 动态规划的能力，这会使我们的AI看起来更加“智能”
- 配置方便，易于理解，完全可以让策划使用EXCEL接管AI开发工作

## HTN（Hierarchical Task Networks）：

### 学习资料：

[(3 封私信 / 45 条消息) 学习HTN规划技术要了解哪些东西? - 知乎 (zhihu.com)](https://www.zhihu.com/question/323258599/answer/1941657770)

### 概念介绍：

在AI开发中，一个普遍需要解决的问题是行为选择。这个问题有很多解决方案，比如FSM有限状态机、行为树、基于效用的选择、神经网络和规划器。这篇文章通过利用在开发过程中可能遇到的现实世界中的项目例子来探究一个类型规划器的细微差别，这类规划器称为分层任务网络(HTN)规划器。

HTN这样的规划器架构，是将一个问题作为输入，并提供一系列解决问题的步骤。在HTN术语中，这一系列步骤称为一个计划(plan)。HTN相对于其他规划器的独特之处在于，它允许我们把问题表现为一个非常高级（抽象）的计划(plan)，通过它的规划过程，递归地把这个任务分解成更小的任务。当这个过程完成时，剩下生成的是一组表示计划(plan)的原子任务。

### 关键词：

自前往后，任务，分解任务，运行时规划

#### 大致架构

![img](https://typorapicturefivuvuv.oss-cn-shanghai.aliyuncs.com/picgo/ef0a710c-364b-4997-98b0-7ddacdca53d3_image.png)

### 详细概念：

#### 世界状态（The World State）

世界状态（World State）是指在任何给定时间点，描绘环境和代理（通常是游戏中的角色或者是一个自主的软件代理）属性的一组变量或属性值。这些变量或属性值描述了环境和代理当前的情况，允许规划系统根据这些信息做出决策。

#### 传感器（Sensor）

传感器(sensor)可以管理不同的世界状态(world state)属性。一些不同传感器的例子包括视觉、听觉、范围和生命值传感器。这些传感器的工作原理与任何其他人工智能系统相同，只是增加了一个步骤，即把它们的信息编码成我们的HTN能够理解的世界状态(world state)。

#### 原子任务（Primitive Tasks)

原子任务是指不能再被分解为更小任务的基础任务。它们是HTN执行过程中的最基本单位，直接映射到实际动作或操作上。原子任务通常与特定的动作相对应，这些动作在世界状态上具有直接影响，改变环境或者代理的状态。

假设有一个探索游戏，其中的HTN规划目标是让角色寻找并收集游戏世界中的特定资源。这个过程可能包括复合任务，如“探索区域”和“收集资源”。其中，“探索区域”可能进一步分解为“移动到区域A”和“搜索区域A”，最终，“移动到区域A”这种动作就是一个原子任务，因为它直接对应到游戏世界中的一个不可再分的动作。

#### 复合任务（Composite Tasks）

复合任务是指可以被进一步分解为更多子任务（复合任务或原子任务）的任务。这种任务的分解是基于任务的层次结构进行的，其目的是通过逐层分解来简化问题解决过程，直至所有的任务被分解为可直接执行的原子任务。

以一个简化的机器人清理房间的任务为例，顶层的复合任务可能是“清理房间”。这个任务可以进一步分解为“清理地板”和“整理桌子”两个复合任务。继续分解，“清理地板”可能变为“扫地”和“拖地”两个原子任务，而“整理桌子”可能进一步分解为“拾起物品”和“擦拭桌面”等原子任务。

#### 方法（method）

"方法"用于描述如何将复合任务分解成更具体的子任务序列。这些子任务可以是进一步的复合任务，需要继续分解，或者是原子任务，直接对应于可执行的动作。方法基本上定义了从高层任务到更低层、更具体任务之间的分解规则和逻辑。

#### 三者关系

复合任务实际上只是一组方法(method)的容器，这些方法(method)每一个都是用来表示完成某个高级任务的不同方式

![img](https://typorapicturefivuvuv.oss-cn-shanghai.aliyuncs.com/picgo/db2db7e0-546b-4eb6-ac80-d365d11dd2f8_image.png)

#### 计划（Plan）

计划（plan）是指从初始状态到达目标状态的一系列可执行动作的组合。这些动作是通过对初步任务（可能是一个或多个复合任务）的逐步分解和具体化得到的。最终，这些动作被组织成一个顺序或有时是并行的执行序列，旨在完成给定的目标或任务。

假设有一个机器人在仓库环境中的任务是从仓库的一端将一箱货物移动到另一端，并且途中需要避开障碍物。

1. **高层任务**：将货物从A点移动到B点。

2. 复合任务分解

   ：这个高层任务可以分解为几个子任务：

   - 定位货物。
   - 搬起货物。
   - 规划到达B点的路径，考虑避开障碍物。
   - 沿规划路径移动货物。
   - 在B点放下货物。

#### 规划器（Planner）

（Planner）是一个核心组件，负责执行整个规划流程，即从初始状态开始，通过逐步分解任务并应用相应的方法，最终生成一个实现目标的计划。

伪代码实现：

~~~C#
    //规划（核心）
    public void Plan()
    {
        //先复制一份世界状态
        var worldState = HTNWorld.CopyWorldState();
        //将存储列表清空，避免上次计划结果的影响
        FinalTasks.Clear();
        //将根任务压进栈中，准备分解
        taskOfProcess.Push(rootTask);
        //只要栈还没空，就继续分解
        while(taskOfProcess.Count > 0)
        {
            //拿出栈顶的元素
            var task = taskOfProcess.Pop();
            //如果这个元素是复合任务
            if(task is CompoundTask cTask)
            {
                //判断是否可以执行
                if(cTask.MetCondition(worldState))
                {
                    /*如果可以执行，就肯定有可用的方法，
                    就将该方法的子任务都压入栈中，以便继续分解*/
                    var subTask = cTask.ValidMethod.SubTask;
                    foreach(var t in subTask)
                    {
                        taskOfProcess.Push(t);
                    }
                    /*通过上面的步骤我们知道，能被压进栈中的只有
                    复合任务和原子任务，方法本身并不会入栈*/
                }
            }
            else //否则，这个元素就是原子任务
            {
                //将该元素转为原子任务，因为原本是IBaseTask类型
                var pTask = task as PrimitiveTask;
                //再将该原子任务加入存放分解完成的任务列表
                FinalTasks.Push(pTask);
            }
        }
~~~

#### 执行器：

规划器的要点在于对「复合任务」的分解，这里提一下，一个HTN会保证有一个复合任务做为根任务，就和行为树的根节点一样。分解也是由此开始：

~~~C#
//用于描述运行结果的枚举（如果有看上一篇行为树的话
public enum EStatus
{
    Failure, Success, Running, 
}
public class HTNPlanRunner
{
    //当前运行状态
    private EStatus curState;
    //直接将规划器包含进来，方便重新规划
    private readonly HTNPlanner planner;
    //当前执行的原子任务
    private PrimitiveTask curTask;
    //标记「原子任务列表是否还有元素、能够继续」
    private bool canContinue;

    public HTNPlanRunner(HTNPlanner planner)
    {
        this.planner = planner;
        curState = EStatus.Failure;
    }

    public void RunPlan()
    {
        //如果当前运行状态是失败（一开始默认失败）
        if(curState == EStatus.Failure)
        {
            //就规划一次
            planner.Plan();
        }
        //如果当前运行状态是成功，就表示当前任务完成了
        if(curState == EStatus.Success)
        {
            //让当前原子任务造成影响
            curTask.Effect();
        }
        /*如果当前状态不是「正在执行」，就取出新一个原子任务作为当前任务
        无论失败还是成功，都要这么做。因为如果是失败，肯定在代码运行到这
        之前，已经进行了一次规划，理应获取新规划出的任务来运行；如果是因
        为成功，那也要取出新任务来运行*/
        if(curState != EStatus.Running)
        {
            //用TryPop的返回结果判断规划器的FinalTasks是否为空
            canContinue = planner.FinalTasks.TryPop(out curTask);
        }
        /*如果canContinue为false，那curTask会为null也视作失败（其实应该是「全部
        完成」，但全部完成和失败是一样的，都要重新规划）。所以只有当canContinue && curTask.MetCondition()都满足时，才读取当前原子任务的运行状态，否则就失败。*/
        curState = canContinue && curTask.MetCondition() ? curTask.Operator() : EStatus.Failure;
    }
}
~~~

## 只狼的游戏AI----以弦一郎为例（行为树与HTN的结合，变种的HTN或者行为树）

#### 写在开头：

**动作游戏是贩卖关于强大角色或能力的幻想**。

#### AI结构：

FromSoftWare的ai配置是用lua进行配置

*Lua 是一种轻量小巧的脚本语言，用标准C语言编写并以源代码形式开放， 其设计目的是为了嵌入应用程序中，从而为应用程序提供灵活的扩展和定制功能。*



![img](https://typorapicturefivuvuv.oss-cn-shanghai.aliyuncs.com/picgo/b301c86dd22698c44a8deee8d327ef0a-910-775.png)

![img](https://typorapicturefivuvuv.oss-cn-shanghai.aliyuncs.com/picgo/v2-d7c4bba0d6c91ccc34418a46ccb09579_r.jpg)

- **Goal.Activate主动计划——自由态时调用** 根据与玩家距离、方位、状态，从众多任务里随机选择一个执行。任务的随机是有权重的，按照设定的权重分配

  ![img](https://typorapicturefivuvuv.oss-cn-shanghai.aliyuncs.com/picgo/v2-1a700dcc21f30e50aebd5dc026bc3b7e_r.jpg)

- **Goal.Interrupt变招计划——不只是读指令** 由于玩家角色的特定行为，中断原有计划转为执行其他动作的计划。

  ![img](https://typorapicturefivuvuv.oss-cn-shanghai.aliyuncs.com/picgo/v2-a89d37d7d98d4513371099c8176dffe9_1440w.webp)

- **Goal.Kengeki交锋计划** 根据玩家格挡或自身格挡的结果判断，从众多任务里随机选择一个执行。

![img](https://typorapicturefivuvuv.oss-cn-shanghai.aliyuncs.com/picgo/v2-7d8f1b034d1e02bce986187dd8e9ae22_r.jpg)
