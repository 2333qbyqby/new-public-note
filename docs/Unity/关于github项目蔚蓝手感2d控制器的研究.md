## 蔚蓝手感项目研究：

### 项目链接：

<https://github.com/david-reborn/2D-Platform-Controller>

<!--more-->

### 脚本分析：

#### common文件夹：

##### Calc脚本

+ 提供一个方法，这个方法接受三个浮点型参数：`val`（值）、prevVal（前一个值）和`interval`（间隔），用于确定当前值`val`相对于前一个值`prevVal`是否进入了新的`interval`区间。

```c#
  using System;
  using System.Collections.Generic;
  using System.Linq;
  using System.Text;
  using System.Threading.Tasks;
  
  namespace Myd.Common
  {
      public static class Calc
      {
          public static bool OnInterval(float val, float prevVal, float interval)
          {
              return (int)(prevVal / interval) != (int)(val / interval);
          }
      }
  }
```

   具体实现上，这个方法首先分别计算`prevVal`和`val`除以`interval`的结果的整数部分，然后比较这两个结果是否相等。如果不等，表示`val`已经“跨越”了一个或多个`interval`，相当于进入了新的区间，方法返回`true`；如果相等，表示`val`仍处于`prevVal`所在的`interval`区间内，方法返回`false`。

  ##### Ease脚本

+ 定义类Ease这个静态类，这个`Ease`类定义了许多常见的缓动函数，其中包括线性（`Linear`）、正弦（`SineIn, SineOut, SineInOut`）、二次（`QuadIn, QuadOut, QuadInOut`）、三次（`CubeIn, CubeOut, CubeInOut`），四次方（`QuintIn, QuintOut, QuintInOut`）、指数（`ExpoIn, ExpoOut, ExpoInOut`）等等。

  ```c#
  public delegate float Easer(float t);
  ```

  

+ 此外，这个类还定义了一个委托`Easer`，它接受一个`float`类型的参数，并返回一个`float`类型的值。所有的缓动函数都是这个类型的，接收一个在0至1之间的浮点数，表示开始和结束之间的一个插值，然后返回一个可能略大于或小于这个范围的新的浮点数。

剩下的common文件夹中的脚本在项目中没有引用，因此不再解释

---

#### Components文件夹

##### JumpCheck脚本

+ 专门控制土狼时间的类，来控制是否能够跳跃。

##### WallBoost

+ Wall Boost组件，当攀墙时跳跃，如果没有指定X轴方向，则系统自动产生一个推离墙面的力。且给予一定的持续时间。

#### Configs

##### PlayerParams

+ 存放Player的参数，是个SO，包含了重载常数的方法和回调的委托

---

#### Core文件夹

##### States

###### FiniteStateMachine

+ 定义了状态机的基础状态 抽象类BaseActionState，有限状态机FiniteStateMachine以及状态的枚举类EActionState.

+ ```c#
  public abstract class BaseActionState
      {
          protected EActionState state;
          protected PlayerController ctx;
  
          protected BaseActionState(EActionState state, PlayerController context)
          {
              this.state = state;
              this.ctx = context;
          }
  
          public EActionState State { get => state; }
  
          //每一帧都执行的逻辑
          public abstract EActionState Update(float deltaTime);
  
          public abstract IEnumerator Coroutine();
  
          public abstract void OnBegin();
  
          public abstract void OnEnd();
  
          public abstract bool IsCoroutine();
      }
  ```

  在这一段代码中，定义了各个状态的基类，主要关注几个方法的定义

+ 有限状态机类：

+  属性声明

  ```c#
  	    private S[] states;
  		private int currState = -1;
          private int prevState = -1;
          private Coroutine currentCoroutine;
  ```

+ 构造函数：

  ```c#
  public FiniteStateMachine(int size)
          {
              this.states = new S[size];
              this.currentCoroutine = new Coroutine(true);
          }
  ```

+ Update

  ```c#
  public void Update(float deltaTime)
          {
              State = (int)this.states[this.currState].Update(deltaTime);
              if (this.currentCoroutine.Active)
              {
                  this.currentCoroutine.Update(deltaTime);
              }
          }
  ```

  TODO:其他状态

##### 其他核心控件：

+ 一些解耦用的接口，相应的状态，管理状态的playerController,这边用partical class实现了对playcontroller中控制不同部分的部件的解耦

---

#### Effect文件夹

##### EffectComponent:

+ 挂载在playerRender的预制体上，控制人物的头巾效果

##### SceneEffectManager

+ 管理所有的特效，具体特效如下：

  ```c#
  [SerializeField]
          private ParticleSystem vfxMoveDust;
          [SerializeField]
          private ParticleSystem vfxJumpDust;
          [SerializeField]
          private ParticleSystem vfxLandDust;
          [SerializeField]
          private ParticleSystem vfxDashLine;
          [SerializeField]
          private RippleEffect vfxRippleEffect;
          [SerializeField]
          private GameObject vfxSpeedRing;
  ```

  在该脚本中，提供了为上层开启特效的函数api

##### TrailSnapShot

+ 断裂特效？

---

#### Level文件夹

该文件夹中的Gound和Level只提供了自己颜色的接口,也就是让玩家获取到地图的颜色

---

#### Postprocessing文件夹

##### PostEffectsController:

+ 管理对于摄像机的特殊效果，也就是冲刺时的空间扭曲

##### RippleEffect

+ 对于效果的具体实现（通过shader实现）

#### 其他重要脚本

##### Constant：

+ 一些游戏中的常数

##### GameInput:

+ 管理游戏的输入（包括一些预输入的功能）

#### Player：

+ 管理playercontroller和playerRenderer，并且允许内部进行交互

##### PlayerRender

+ 绘制玩家的形象，以下是其中的属性

```c#
[SerializeField]
        public SpriteRenderer spriteRenderer;

        [SerializeField]
        public ParticleSystem vfxDashFlux;
        [SerializeField]
        public ParticleSystem vfxWallSlide;

        [SerializeField]
        public TrailRenderer hair;

        [SerializeField]
        public SpriteRenderer hairSprite01;
        [SerializeField]
        public SpriteRenderer hairSprite02;

        private Vector2 scale;
        private Vector2 currSpriteScale;
```

##### SceneCamera:

实现摄像头的抖动等功能，实际上可用cinemachine的虚拟摄像机的功能代替

##### Game:

最高层的脚本，管控整个游戏的流程，并且负责提供一些API供低层的脚本使用



##### 









