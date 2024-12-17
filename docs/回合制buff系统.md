
# 在unity中的一个比较完善的回合制buff系统框架（暂时已废弃）

## 前言：

​	在前两个月开发的的cusga，笔者仿照猴叔的可扩展俯视角射击[用Unity制作一个极具扩展性的顶视角射击游戏战斗系统 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/416805924)

完成了游戏中的buff系统的构建，经过了一些小小的魔改，在配置上和扩展上达到了可以接受的程度，但是还是饱受另一位配置的程序的诟病，于是有了这篇文章，基于之前的框架，进行一些重构，来达到配置更简单，更容易扩展的目的。

## 原本的buff系统：

### 原本buff系统组成：

(本buff系统基本上来自于猴叔的架构，所以看过猴叔的可以简单地跳过)

#### BuffData

原本的buff系统大体由BuffData,BuffInfo,BuffHandler组成，其中buffData是buff的基础配置，是静态的配置，由一些buff的信息和回调点的delegate以及各个delegate的参数组成（关于回调点的概念，请参考上面的知乎文章，另外，在旧的buff系统中，其实回调点参数都没有使用），buffdata的具体定义如下：

```C#
public class BuffData
{

    /// <summary>
    /// buff的唯一id
    /// </summary>
    public string id;
    /// <summary>
    /// buff的名字
    /// </summary>
    public string buffName;
    /// <summary>
    /// 存储buff的icon图标的路径，resoueces读取，后面没用上
    /// </summary>
    public string buffIcon;
    /// <summary>
    /// 用于方便检索的tag，例如：火焰，冰冻，中毒等
    /// </summary>
    public string[] tags;
    /// <summary>
    /// buff的最高层数
    /// </summary>
    public int maxStack;
    //当buff的层数发生变化时，buff的更新策略
    public BuffUpdateEnum buffUpdateEnum;

    public BuffRemoveStackUpdateEnum removeStackUpdateEnum;

    //buff的时间信息
    public int duringCount;
    /// <summary>
    /// 是否是永久的buff
    /// </summary>
    public bool isPermanent;
    /// <summary>
    /// buff会给角色添加的属性，暂定两种，一种加算，一种乘算
    /// </summary>
    public ChaProperty[] propMod;
    /// <summary>
    /// buff对于角色状态的修改
    /// </summary>
    public ChaControlState stateMod;
    /// <summary>
    /// buff在创建的时候的事件
    /// </summary>
    public OnBuffCreate onCreate;
    public object[] onCreateParams;
    /// <summary>
    /// buff在移除的时候的事件
    /// </summary>
    public OnBuffRemove onRemove;
    public object[] onRemoveParams;
    /// <summary>
    /// 在回合开始的时候的触发的事件
    /// </summary>
    public OnRoundStart onRoundStart;
    public object[] onRoundStartParams;
    /// <summary>
    /// 在回合结束的时候触发的事件
    /// </summary>
    public OnRoundEnd onRoundEnd;
    public object[] onRoundEndParams;
    /// <summary>
    /// 在伤害流程中，持有这个buff的角色作为攻击者时触发的事件
    /// </summary>
    public BuffOnHit onHit;
    public object[] onHitParams;
    /// <summary>
    /// 在伤害流程中，持有这个buff的角色作为被攻击者时触发的事件
    /// </summary>
    public BuffOnBeHurt onBeHurt;
    public object[] onBeHurtParams;
    /// <summary>
    /// 在开局玩家操作阶段roll骰子时触发的事件
    /// </summary>
    public BuffOnRoll onRoll;
    public object[] onRollParams;
    /// <summary>
    /// 在伤害流程中，如果击杀目标，会触发的事件
    /// </summary>
    public BuffOnkill onKill;
    public object[] onKillParams;

    public BuffOnBeKilled onBeKilled;
    public object[] onBeKilledParams;

    public BuffOnCast OnCast;
    public object[] onCastParams;

    public OnAddBuff onAddBuff;
    public object[] onAddBuffParams;

    public OnGetFinalDamage onGetFinalDamage;
    public object[] onGetFinalDamageParams;
    public BuffData(
        string id, string name, string icon, string[] tags, int maxStack, int duringCount, bool isPermanent,
        BuffUpdateEnum buffUpdateEnum, BuffRemoveStackUpdateEnum removeStackUpdateEnum,
        string onCreate, object[] onCreateParams,
        string onRemove, object[] onRemoveParams,
        string onRoundStart, object[] onRoundStartParams,
        string onRoundEnd, object[] onRoundEndParams,
        string onHit, object[] onHitParams,
        string onBeHurt, object[] onBeHurtParams,
        string onRoll, object[] onRollParams,
        string onKill, object[] onKillParams,
        string onBeKilled, object[] onBeKilledParams,
        string onCast, object[] onCastParams,
        string onAddBuff, object[] onAddBuffParams,
        string onGetFinalDamage, object[] onGetFinalDamageParams,
        ChaControlState stateMod, ChaProperty[] propMod = null
        )
    {
        this.id = id;
        this.buffName = name;
        this.buffIcon = icon;
        this.tags = tags;
        this.maxStack = maxStack;
        this.duringCount = duringCount;
        this.isPermanent = isPermanent;
        this.propMod = new ChaProperty[2]
            {ChaProperty.zero,
             ChaProperty.zero
        };

        if (propMod != null)
        {
            for (int i = 0; i < Mathf.Min(propMod.Length, 2); i++)
            {
                this.propMod[i] = propMod[i];
            }
        }
        this.stateMod = stateMod;

        this.onCreate = (onCreate == "" )? null : DesignerScripts.BuffEvents.onCreateFunc[onCreate];
        this.onCreateParams = onCreateParams;
        this.onRemove = (onRemove == "") ? null : DesignerScripts.BuffEvents.onRemoveFunc[onRemove];
        this.onRemoveParams = onRemoveParams;
        this.onRoundStart = (onRoundStart == "") ? null : DesignerScripts.BuffEvents.onRoundStartFunc[onRoundStart];
        this.onRoundStartParams = onRoundStartParams;
        this.onRoundEnd = (onRoundEnd == "") ? null : DesignerScripts.BuffEvents.onRoundEndFunc[onRoundEnd];
        this.onRoundEndParams = onRoundEndParams;
        this.onHit = (onHit == "") ? null : DesignerScripts.BuffEvents.onBuffHitFunc[onHit];
        this.onHitParams = onHitParams;
        this.onBeHurt = (onBeHurt == "") ? null : DesignerScripts.BuffEvents.onBeHurtFunc[onBeHurt];
        this.onBeHurtParams = onBeHurtParams;
        this.onRoll = (onRoll == "") ? null : DesignerScripts.BuffEvents.onRollFunc[onRoll];
        this.onRollParams = onRollParams;
        this.onKill = (onKill == "") ? null : DesignerScripts.BuffEvents.onKillFunc[onKill];
        this.onKillParams = onKillParams;
        this.onBeKilled = (onBeKilled == "") ? null : DesignerScripts.BuffEvents.onBeKillFunc[onBeKilled];
        this.onBeKilledParams = onBeKilledParams;
        this.OnCast = (onCast == "") ? null : DesignerScripts.BuffEvents.onCastFunc[onCast];
        this.onCastParams = onCastParams;
        this.onAddBuff = (onAddBuff == "") ? null : DesignerScripts.BuffEvents.onAddFunc[onAddBuff];
        this.onAddBuffParams = onAddBuffParams;
        this.onGetFinalDamage = (onGetFinalDamage == "") ? null : DesignerScripts.BuffEvents.onGetFinalDamageFunc[onGetFinalDamage];
        this.onGetFinalDamageParams = onGetFinalDamageParams;
    }


}
/// <summary>
/// buff的创建，移除，回合开始，回合结束，被攻击，攻击，被击杀，击杀等事件
/// </summary>
/// <param name="buff"></param>
public delegate void OnBuffCreate(BuffInfo buff);
public delegate void OnBuffRemove(BuffInfo buff);
public delegate void OnRoundStart(BuffInfo buff);
public delegate void OnRoundEnd(BuffInfo buff);
public delegate void BuffOnHit(BuffInfo buff, DamageInfo damageInfo, GameObject target);
public delegate void BuffOnBeHurt(BuffInfo buff, DamageInfo damageInfo, GameObject attacker);
public delegate void BuffOnRoll(BuffInfo buffInfo);
public delegate void BuffOnkill(BuffInfo buffInfo, DamageInfo damageInfo, GameObject target);
public delegate void BuffOnBeKilled(BuffInfo buffInfo, DamageInfo damageInfo, GameObject attacker);
public delegate SingleDiceObj BuffOnCast(BuffInfo buffInfo, SingleDiceObj singleDiceObj);
public delegate void OnAddBuff(BuffInfo buffInfo);
public delegate void OnGetFinalDamage(BuffInfo buffInfo,DamageInfo damageInfo);
```

可以看到基本上和猴叔的buffData定义保持一致，只不过做出了一些基于回合制的特别改动，例如回调点的改动以及一些参数描述上的的改动（待补充）

#### BuffInfo

接下来是buffInfo,buff,BuffInfo是运行时实时进行刷新的buff实例，需要注意的是，这边的buff信息的架构并没有采用猴叔的三层架构，而是只采用了两层，所以代码会有些不同，完整代码如下

```c#
using System;
using System.Collections.Generic;
using UnityEngine;
[Serializable]
public class BuffInfo
{
    /// <summary>
    /// buff数据的模板
    /// </summary>
    public BuffData buffData;
    /// <summary>
    /// buff是否是永久的
    /// </summary>
    public bool isPermanent;
    /// <summary>
    /// buff的创建者
    /// </summary>
    public GameObject creator = null;
    /// <summary>
    /// buff的目标
    /// </summary>
    public GameObject target = null;
    /// <summary>
    /// 回合计数器
    /// </summary>
    public int roundCount;
    /// <summary>
    /// 执行次数
    /// </summary>
    public int ticked = 0;
    /// <summary>
    /// 当前buff的层数
    /// </summary>
    public int curStack;

    public Dictionary<string, object> buffParam = new Dictionary<string, object>();


    public BuffInfo(
        BuffData buffData, GameObject creator, GameObject target, int stack = 1, bool isPermanent = false,
        Dictionary<string, object> buffParam = null
        )
    {
        this.buffData = buffData;
        this.creator = creator;
        this.target = target;
        this.curStack = stack;
        this.isPermanent = buffData.isPermanent;
        //this.isPermanent = buffData.isPermanent;
        this.roundCount = buffData.duringCount;
        if (buffParam != null)
        {
            foreach (var item in buffParam)
            {
                this.buffParam.Add(item.Key, item.Value);
            }
        }
    }

    public BuffInfo(
        BuffData buffData, int stack = 1, bool isPermanent = false,
        Dictionary<string, object> buffParam = null
        )
    {
        this.buffData = buffData;
        this.curStack = stack;
        this.isPermanent = isPermanent;
        this.roundCount = buffData.duringCount;
        if (buffParam != null)
        {
            foreach (var item in buffParam)
            {
                this.buffParam.Add(item.Key, item.Value);
            }
        }
    }
    //深拷贝
    public BuffInfo(BuffInfo buffInfo)
    {
        this.buffData =buffInfo.buffData;
        this.creator = buffInfo.creator;
        this.target = buffInfo.target;
        this.curStack = buffInfo.curStack;
        this.isPermanent = buffInfo.isPermanent;
        this.buffParam = buffInfo.buffParam;
        this.roundCount=buffInfo.roundCount;
    }
}

public enum BuffRemoveStackUpdateEnum
{
    Reduce,
    Clear,
    
}

public enum BuffUpdateEnum
{
    Add,
    Replace,
    Keep
}


```

BuffInfo包含了一些Runtime比较重要的信息，以及运行时的额外参数，可以看到这边为了扩展性采用了string,object的字典,关于这边的构造函数，主要还是以需求为主，有需求再往上加。

### BuffHandler

最后是BuffHandler，Buffhandler负责管理具体游戏中角色的buff的添加和删除，以及供外部调用触发回调点函数，当然这边的回调点函数是不完全的，在有些情况下会出问题，这个在后续会讲到。

```c#
public class BuffHandler : MonoBehaviour
{
    [Description("Buff列表")]
    public List<BuffInfo> buffList = new List<BuffInfo>();
    //所有判断的时候如果buff需要移除，先加入这个列表，然后再进行移除
    private List<BuffInfo> removeList = new List<BuffInfo>();

    public void AddBuff(BuffInfo buffInfo, GameObject creator)
    {
        //添加buff时候的回调点触发
        buffInfo.creator = creator;
        buffInfo.target = this.gameObject;
        HalidomManager.Instance.OnAddBuff(buffInfo);
        BuffInfo findBuffInfo = buffList.Find(x => x.buffData.id == buffInfo.buffData.id);
        if (findBuffInfo != null)//获得相同的buff的情况
        {
            if (findBuffInfo.curStack < findBuffInfo.buffData.maxStack)
            {
                //findBuffInfo.curStack++;
                if (findBuffInfo.curStack + buffInfo.curStack > findBuffInfo.buffData.maxStack)
                {
                    findBuffInfo.curStack = buffInfo.buffData.maxStack;
                }
                else
                {
                    findBuffInfo.curStack += buffInfo.curStack;
                }
                
                switch (findBuffInfo.buffData.buffUpdateEnum)
                {
                    case BuffUpdateEnum.Add:
                        findBuffInfo.roundCount += findBuffInfo.buffData.duringCount;
                        break;
                    case BuffUpdateEnum.Replace:
                        findBuffInfo.roundCount = findBuffInfo.buffData.duringCount;
                        break;
                    case BuffUpdateEnum.Keep:
                        break;
                    default:
                        break;
                }
                var charac = (Character)findBuffInfo.target.GetComponent<ChaState>().side;
                var index = buffList.IndexOf(findBuffInfo);
                BuffUIManager.Instance.UpdateBuffDurationTime(charac, index, findBuffInfo.curStack);
                //TODO:提示buff层数增加,根据buff的类型进行不同的处理
                findBuffInfo.buffData.onCreate?.Invoke(findBuffInfo);
            }
            else
            {
                //TODO:提示buff层数已满
            }
        }
        else
        {
            
            buffInfo.buffData.onCreate?.Invoke(buffInfo);
            buffList.Add(buffInfo);
            //添加buffUI
            var charac = (Character)buffInfo.target.GetComponent<ChaState>().side;
            BuffUIManager.Instance.CreateBuffUIObject(charac, buffInfo.buffData.id, buffInfo.curStack);
        }
    }

    /// <summary>
    /// 移除buff
    /// </summary>
    /// <param name="buffInfo"></param>
    public void RemoveBuff(BuffInfo buffInfo)
    {
        switch (buffInfo.buffData.removeStackUpdateEnum)
        {
            case BuffRemoveStackUpdateEnum.Clear:
                buffInfo.buffData.onRemove?.Invoke(buffInfo);
                int index = buffList.IndexOf(buffInfo);
                var charac = (Character)buffInfo.target.GetComponent<ChaState>().side;
                BuffUIManager.Instance.RemoveBuffUIObject(charac, index);
                buffList.Remove(buffInfo);
                break;
            case BuffRemoveStackUpdateEnum.Reduce:
                buffInfo.curStack--;
                var charac1 = (Character)buffInfo.target.GetComponent<ChaState>().side;
                int index1 = buffList.IndexOf(buffInfo);
                BuffUIManager.Instance.UpdateBuffDurationTime(charac1, index1, buffInfo.curStack);
                buffInfo.buffData.onRemove?.Invoke(buffInfo);
                if (buffInfo.curStack <= 0)
                {
                    int i = buffList.IndexOf(buffInfo);
                    var c = (Character)buffInfo.target.GetComponent<ChaState>().side;
                    BuffUIManager.Instance.RemoveBuffUIObject(c, i);
                    buffList.Remove(buffInfo);
                }
                //TODO:关于buff层数减少的刷新
                break;
            default:
                break;
        }

    }
    /// <summary>
    /// 每个回合开始的时候，对buff的时间进行处理,side表示当前是哪一方的回合
    /// </summary>
    #region 回调点
    public void BuffRoundStartTick(int side)
    {
        if (side == 0)
        {
            for (int i = 0; i < buffList.Count; i++)
            {
                if (buffList[i].buffData.tags.Contains("PlayerOnRoundStart"))
                {
                    buffList[i].buffData.onRoundStart?.Invoke(buffList[i]);
                }
            }
        }
        else if (side == 1)
        {
            for (int i = 0; i < buffList.Count; i++)
            {
                if (buffList[i].buffData.tags.Contains("EnemyOnRoundStart"))
                {
                    buffList[i].buffData.onRoundStart?.Invoke(buffList[i]);
                }
            }
        }

    }
    //TODO:每个回合结束的时候，对buff的时间进行处理
    public void BuffRoundEndTick(int side)
    {
        List<BuffInfo> removeList = new List<BuffInfo>();
        //因为buffhandler既不知道是给谁挂载的，也不知道是什么回合，所以需要两个参数来判断
        //传入的是玩家并且当前是玩家的回合
        if (side == 0 && BattleManager.Instance.GetCurrentState() == BattleManager.Instance.GetStates()[GameState.PlayerRoundEndResolution])
        {
            
            //触发玩家身上的有OnMyTurnEnd的buff
            foreach (var buff in buffList)
            {
                if (buff.buffData.tags.Contains("OnMyTurnEnd"))
                {
                    buff.buffData.onRoundEnd?.Invoke(buff);
                }

            }

            for (int i = 0; i < buffList.Count; i++)
            {
                if (buffList[i].buffData.tags.Contains("OnMyTurnEnd"))
                {
                    if (buffList[i].isPermanent == false)//非永久buff
                    {
                        buffList[i].roundCount--;

                        buffList[i].roundCount = Mathf.Max(0, buffList[i].roundCount);//可能出现负数的情况
                        if (buffList[i].roundCount == 0)
                        {
                            removeList.Add(buffList[i]);
                        }
                    }
                }

            }
            for (int i = 0; i < removeList.Count; i++)
            {
                RemoveBuff(removeList[i]);
            }
        }
        //传入的是敌人 在玩家的回合结束
        else if (side == 1 && BattleManager.Instance.GetCurrentState() == BattleManager.Instance.GetStates()[GameState.PlayerRoundEndResolution])
        {
            Debug.Log("传入的是敌人 在玩家的回合结束");
            foreach (var buff in buffList)
            {
                if (buff.buffData.tags.Contains("OnOtherTurnEnd"))
                {
                    buff.buffData.onRoundEnd?.Invoke(buff);
                }

            }

            for (int i = 0; i < buffList.Count; i++)
            {
                if (buffList[i].buffData.tags.Contains("OnOtherTurnEnd"))
                {
                    if (buffList[i].isPermanent == false)//非永久buff
                    {
                        buffList[i].roundCount--;

                        buffList[i].roundCount = Mathf.Max(0, buffList[i].roundCount);//可能出现负数的情况
                        if (buffList[i].roundCount == 0)
                        {
                            removeList.Add(buffList[i]);
                        }
                    }
                }

            }
            for (int i = 0; i < removeList.Count; i++)
            {
                RemoveBuff(removeList[i]);
            }
        }
        //传入的是玩家 在敌人的回合结束
        else if (side == 0 && BattleManager.Instance.GetCurrentState() == BattleManager.Instance.GetStates()[GameState.EnemyRoundEndResolution])
        {
            Debug.Log("传入的是玩家 在敌人的回合结束");
            foreach (var buff in buffList)
            {
                if (buff.buffData.tags.Contains("OnOtherTurnEnd"))
                {
                    buff.buffData.onRoundEnd?.Invoke(buff);
                }

            }

            for (int i = 0; i < buffList.Count; i++)
            {
                if (buffList[i].buffData.tags.Contains("OnOtherTurnEnd"))
                {
                    if (buffList[i].isPermanent == false)//非永久buff
                    {
                        buffList[i].roundCount--;

                        buffList[i].roundCount = Mathf.Max(0, buffList[i].roundCount);//可能出现负数的情况
                        if (buffList[i].roundCount == 0)
                        {
                            removeList.Add(buffList[i]);
                        }
                    }
                }

            }
            for (int i = 0; i < removeList.Count; i++)
            {
                RemoveBuff(removeList[i]);
            }
        }

        //传入的是敌人 在敌人的回合结束
        if (side == 1 && BattleManager.Instance.GetCurrentState() == BattleManager.Instance.GetStates()[GameState.EnemyRoundEndResolution])
        {
            Debug.Log("传入的是敌人 在敌人的回合结束");
            //触发敌人身上的有OnMyTurnEnd的buff
            foreach (var buff in buffList)
            {
                if (buff.buffData.tags.Contains("OnMyTurnEnd"))
                {
                    buff.buffData.onRoundEnd?.Invoke(buff);
                }

            }

            for (int i = 0; i < buffList.Count; i++)
            {
                if (buffList[i].buffData.tags.Contains("OnMyTurnEnd"))
                {
                    if (buffList[i].isPermanent == false)//非永久buff
                    {
                        buffList[i].roundCount--;

                        buffList[i].roundCount = Mathf.Max(0, buffList[i].roundCount);//可能出现负数的情况
                        if (buffList[i].roundCount == 0)
                        {
                            removeList.Add(buffList[i]);
                        }
                    }
                }

            }
            for (int i = 0; i < removeList.Count; i++)
            {
                RemoveBuff(removeList[i]);
            }
        }
    }

    public void BuffOnReRoll()
    {
        for (int i = 0; i < buffList.Count; i++)
        {
            buffList[i].buffData.onRoll?.Invoke(buffList[i]);
        }
    }
    //待定
    public void BuffOnCast()
    {
        foreach (var buff in buffList)
        {

        }
    }
    #endregion
    public void RecheckBuff(ChaProperty[] buffProp, ref ChaControlState chaControlState)
    {

        foreach (var buff in buffList)
        {
            buffProp[0] += buff.buffData.propMod[0] * buff.curStack;
            buffProp[1] += buff.buffData.propMod[1];
            chaControlState += buff.buffData.stateMod;
        }
    }
}
```

### 使用：

每一个收到buff影响的character身上都挂了一个buffHandler，其他的相关manager在合适的时候调用回调点触发函数，例如在受伤时的回调函数和攻击时的回调函数，就在damageManager中触发（关于damage的详细信息，见猴与花果山的文章）

### 引擎内配置：

![image-20240529225508974](https://typorapicturefivuvuv.oss-cn-shanghai.aliyuncs.com/picgo/image-20240529225508974.png)

这是一个最简单buffSO的截图示例，采取了SO配置整体信息+脚本内配置回调点具体函数，在运行的时候初始化为buffData，由于一些原因，损失了每个回调点的参数，也无法进行回调点的组合和公用，因此只能说是堪堪能用。

大体的工作流如下：

先在写回调点event的脚本里写相应的buff的回调点函数，再手动加进相应的字典里，字典的key是回调点函数效果的枚举，因此也需要对对应相关回调点的enum进行修改，再将buffName添加进buffName的enum（其实名字直接用string也可以，因为配置步骤过多，所以enum不容易错），之后再在需要用到buffData的地方，从buffData的字典里找出相应的buffData,根据具体的情况生成buffInfo，加到具体角色身上的buffHandler的bufflist里

## 新buff系统：

### 旧buff系统的问题：

从上述的描述中，其实已经可以看出，先前的buff系统在各个方面都存在不少的问题，但是这边还是要具体列举一下开发中遇到的问题。

1、配置上的问题，配置的步骤太过繁琐导致容易出错，随着业务需求的增加，会很繁琐，也就是程序化的程度不够，需要指定一套相对比较完善的配置流程

2、对于结构上的问题，每一个回调点：例如受击方扣除x点血量这种event，应该是可以公用的，因此需要决定参数开放在哪的问题，但是旧buff系统关于这一块没有很好地解决

3、与UI的交互的问题，应该统一规定好与UI交互的位置，在移除buff图标的时候，不需要手动地调用一些方法

### 新buff系统组成：

首先是配置，配置方面采用luban配表游戏内so中间层显示的模式，,大体的数据结构不变。

### 新buff系统系统拆解：

#### 层次结构：

主要分为luban层，SO层，和其他具体runtime时的数据层，配置的部分主要在luban层和SO层

#### luban层配置

在luban的配置层，主要完成的工作是进行buffData的配置,也就是需要在excel表格中进行buffData的配置，在excel中需要进行如下配置

| ##var  | id           | name       | buffIcon | tags                 | maxStack       | buffRemoveStackUpdateEnum | buffUpdateEnum | duringCount      | isPermanent | onCreateEvents         | onRemoveEvents       | onOtherRoundStartEvents | onThisRoundStartEvents | onOtherRoundEndEvents | onThisRoundEndEvents | onHitEvents          | onBeHurtEvents       | onKillEvents         | onBeKilledEvents     |
| ------ | ------------ | ---------- | -------- | -------------------- | -------------- | ------------------------- | -------------- | ---------------- | ----------- | ---------------------- | -------------------- | ----------------------- | ---------------------- | --------------------- | -------------------- | -------------------- | -------------------- | -------------------- | -------------------- |
| ##Type | string       | string     | string   | (list#sep=\|),string | int            | BuffRemoveStackUpdateEnum | BuffUpdateEnum | int              | bool        | (list#sep=\|),string   | (list#sep=\|),string | (list#sep=\|),string    | (list#sep=\|),string   | (list#sep=\|),string  | (list#sep=\|),string | (list#sep=\|),string | (list#sep=\|),string | (list#sep=\|),string | (list#sep=\|),string |
| ##     | buff的唯一id | buff的名字 | 路径     | buff的tag            | buff的上限层数 | buff移除的时候的模式      | buff添加的模式 | buff持续的回合数 | 是否永久    | 创建触发事件的类名列表 |                      |                         |                        |                       |                      |                      |                      |                      |                      |
|        | 1            | 测试buff   | ""       |                      | 5              | 0                         | 0              | 4                | TRUE        | HpEnhanceEvent         |                      |                         |                        |                       |                      |                      |                      |                      |                      |

可以看到基本上在excel里面的配置就是之前buffData的部分配置，值得一提的是，这边的回调点配置是一个string的list,这边记录的是event的名字，每个event都是一个类，这边在具体生成的时候会反射转换成类

#### unitySO层配置

在unitySO层的配置，主要时配置一些excel中配置不了的回调点event的参数，例如我有一个event指代的是扣血，不需要为了每个buff专门写每个的扣血event,因此需要开放出变量进行配置工作（下图是一个例子）

![image-20240612224648289](https://typorapicturefivuvuv.oss-cn-shanghai.aliyuncs.com/picgo/image-20240612224648289.png)

#### 具体工作流程：

1、先在excel表中配置buffData的一部分数据，通过luban的脚本生成json到unity的asset下（具体生成到什么文件夹看个人喜好）

2、在unity中通过buffEditor生成出buffDataSO以及相应的event

3、buffDataSO就是原本的buffData，需要再在实际运行的时候转成buffInfo

在引擎中生成出来的时候，会在指定的路径生成出相应buff的一整个文件夹，图示如下：

![image-20240612230816691](https://typorapicturefivuvuv.oss-cn-shanghai.aliyuncs.com/picgo/image-20240612230816691.png)

