# Unity锚点机制及坐标转换

## 参考资料

[Unity3D RectTransform使用详解：布局、属性、方法、设计 - 知乎](https://zhuanlan.zhihu.com/p/139252379)
[Unity的坐标系统及它们互相转换的方法_unity 世界坐标转局部坐标-CSDN博客](https://blog.csdn.net/mr_five55/article/details/134841438)

## Unity坐标系

### **一、Unity中的坐标系**

1. **世界坐标系（World Space）**

   - 全局坐标系，所有物体的绝对位置。
   - 通过`transform.position`获取。

2. **局部坐标系（Local Space）**

   - 相对于父物体的坐标系。
   - 通过`transform.localPosition`获取。

3. **屏幕坐标系（Screen Space）**

   - 以像素为单位，左下角为原点 `(0, 0)`，右上角为 `(Screen.width, Screen.height)`。
   - 用于鼠标位置或UI转换。

4. **视口坐标系（Viewport Space）**

   - 归一化坐标系，范围 `[0, 1]`，左下角 `(0, 0)`，右上角 `(1, 1)`。

   - 常用于相机相关计算。
     

### **二、Position属性的分类**

#### **1. Transform类（用于3D/2D非UI对象）**

- `transform.position`：世界坐标系中的位置。
- `transform.localPosition`：相对于父物体的局部位置。

#### **2. RectTransform类（用于UI对象）**

- `rectTransform.anchoredPosition`：相对于锚点的位置（单位通常是像素）。
- `rectTransform.localPosition`：继承自Transform，但通常不建议直接使用（受锚点布局影响）

### **三、坐标转换示例**

#### **1. 世界坐标 ↔ 局部坐标**

csharp

复制

```
// 局部坐标转世界坐标
Vector3 worldPos = parentTransform.TransformPoint(localPos);

// 世界坐标转局部坐标
Vector3 localPos = parentTransform.InverseTransformPoint(worldPos);
```

#### **2. 世界坐标 ↔ 屏幕坐标**

csharp

复制

```
// 世界坐标转屏幕坐标
Vector3 screenPos = Camera.main.WorldToScreenPoint(worldPos);

// 屏幕坐标转世界坐标（需指定深度，如Z值）
Vector3 worldPos = Camera.main.ScreenToWorldPoint(new Vector3(screenPos.x, screenPos.y, 10));
```

#### **3. 屏幕坐标 ↔ UI坐标（RectTransform）**

csharp

复制

```
// 将屏幕坐标转换为UI局部坐标
RectTransformUtility.ScreenPointToLocalPointInRectangle(
    rectTransform.parent as RectTransform, 
    Input.mousePosition, 
    Camera.main, 
    out Vector2 localPos
);
rectTransform.anchoredPosition = localPos;
```

## Unity UI中的锚点机制和锚点坐标

### Pivot

Pivot代表了RectTransform的中心点，即：相对于RectTransform的坐标（如Anchor或Rotation）都会以它为基准点，（0，0）代表自身区域的左下，（1，1）代表自身区域的右上，如果超过了[0, 1]范围，就说明超过了自身区域之外,这个点是所有**旋转，翻转**的中心点。

### 锚点与锚框(Anchors)

Anchors 由两个归一化的 Vector 组成 - Min 和 Max，这两个向量的值都是相对于父物体中并且归一化的值。两个向量会组成一个矩形，矩形的四个顶点就是四个 Anchor(锚点)，这两个向量对应了 RectTransform 类中的 `anchorMin` 和 `anchorMax` 属性。Unity 中，Anchors 是可以手动编辑的，如下:
![img](https://typorapicturefivuvuv.oss-cn-shanghai.aliyuncs.com/picgo/v2-229d4146a5b0721f576be5f5054b28ff_1440w.jpg)
这边需要注意的是，Anchors必须得有父类的RectTransform,如果没有父类的RectTransform,那么RectTransform属性面板上的Anchor Presets将不可设置，但Anchors仍可设置。

### AnchoredPosition

AnchorPosition是子类的Pivot（中心点）到（父类上）Anchors（锚点）的相对位置，这个位置同样也是RectTransform面板上显示的——**PosX，PosY，PosZ**——在代码中中是**anchoredPosition3D**（PosX，PosY，PosZ），或anchoredPosition（PosX，PosY）。

### 面板的变化

当anchors不在表现为一个点，也就是说anchorMin和anchorMax不一样时，面板会发生变化，分为以下几种，这种情况称为拉伸，从Inspector中可以看出
![img](https://typorapicturefivuvuv.oss-cn-shanghai.aliyuncs.com/picgo/v2-ed5117a347b729737d391ffc5368d458_1440w.jpg)

如果anchorMin和anchorMax的y相等，x不相等，此时锚点的左右两边会分开，面板上的x和width会消失，取而代之的是left和right,这个就代表当前元素到左右两半锚点的相对位置。此时如果说父元素进行变化，相对于锚点的左右相对位置会保持不变

![img](https://typorapicturefivuvuv.oss-cn-shanghai.aliyuncs.com/picgo/v2-953725d5e811321ea5cefce08c818a56_1440w.jpg)

如果anchorMin和anchorMax的x相等，y不相等，此时锚点上下两半会分开，面板上的y和height会消失，取而代之的是top和botton，这就代表当前元素到上下两半锚点的相对位置，此时如果父元素进行变化，相对于锚点的上下相对位置会保持不变。

![img](https://typorapicturefivuvuv.oss-cn-shanghai.aliyuncs.com/picgo/v2-9886619a2fc7871490eb851ff31e6648_1440w.jpg)

以此类推，如果anchorMin，anchorMax的x不相等，y也不相等，此时锚点的四个小三角形会散开，变成一个矩形，此时面板会变成left,right,top,bottom,也就是说此时面板上只能配置相对位置。

![img](https://typorapicturefivuvuv.oss-cn-shanghai.aliyuncs.com/picgo/v2-5b07b71c0c5a0ba7f8243c3a2030a66b_1440w.jpg)

### 在面板变化的情况下的anchoredPosition

如果说anchors不再是一个点，而是一个边，那么此时对于anchoredPosition计算的基准点是这个矩形的中点。