# C#反射

## 参考资料

[C# 反射（Reflection）超详细解析-CSDN博客](https://blog.csdn.net/weixin_45136016/article/details/139095147)

[带你秒懂C#反射机制 - 个人文章 - SegmentFault 思否](https://segmentfault.com/a/1190000022032802)

## 最省流解释

反射机制是在运行状态中，对于任意一个类，都能够知道这个类的所有属性和方法，对于任意一个对象，都能够调用它的任意一个方法和属性，是一种动态获取的信息以及动态调用对象的方法的功能。

## 整体的框架（哪些重要的类）

### `System.Reflection` 核心类与功能

| 类/结构               | 功能描述                                                   | Unity 应用示例                                               |
| :-------------------- | :--------------------------------------------------------- | :----------------------------------------------------------- |
| **`Assembly`**        | 表示一个程序集，用于加载和分析程序集元数据。               | 动态加载外部插件： `Assembly assembly = Assembly.LoadFile("MyPlugin.dll");` |
| **`Type`**            | 表示类型声明（类、接口、数组等），提供类型元数据访问。     | 检查组件类型： `Type componentType = typeof(EnemyAI);` `if (go.GetComponent(componentType)) ...` |
| **`MethodInfo`**      | 描述方法的元数据，支持动态调用方法。                       | 动态调用协程： `MethodInfo method = typeof(Player).GetMethod("StartCoroutine");` `method.Invoke(player, new[] { "Respawn" });` |
| **`PropertyInfo`**    | 描述属性的元数据，支持动态获取/设置属性值。                | 动态修改 UI 属性： `PropertyInfo prop = typeof(Image).GetProperty("color");` `prop.SetValue(uiImage, Color.red);` |
| **`FieldInfo`**       | 描述字段的元数据，支持动态获取/设置字段值。                | 修改私有字段（如单例实例）： `FieldInfo field = typeof(GameManager).GetField("instance", BindingFlags.NonPublic | BindingFlags.Static);` |
| **`ConstructorInfo`** | 描述构造函数的元数据，支持动态创建对象实例。               | 动态创建配置对象： `ConstructorInfo ctor = typeof(WeaponConfig).GetConstructor(new Type[0]);` `WeaponConfig config = (WeaponConfig)ctor.Invoke(null);` |
| **`MemberInfo`**      | 所有成员（方法、属性、字段等）的基类，提供通用成员元数据。 | 遍历所有成员： `foreach (MemberInfo member in typeof(Enemy).GetMembers()) { ... }` |
| **`ParameterInfo`**   | 描述方法参数的元数据，如参数类型和名称。                   | 验证事件回调参数： `ParameterInfo[] parameters = onClickMethod.GetParameters();` `if (parameters.Length == 0) ...` |

### 其他重要的类

`Activator` 是动态创建对象实例的核心工具类，常用于反射中根据类型信息生成对象。

------

#### **`Activator` 核心方法**

| 方法/重载                                                  | 功能描述                                       | 典型应用场景                          |
| :--------------------------------------------------------- | :--------------------------------------------- | :------------------------------------ |
| **`CreateInstance(Type type)`**                            | 根据 `Type` 创建对象实例（调用无参构造函数）   | 动态生成配置类或非 MonoBehaviour 对象 |
| **`CreateInstance(Type type, params object[] args)`**      | 根据 `Type` 和参数创建实例（支持含参构造函数） | 动态创建需要初始化的对象              |
| **`CreateInstance(string assemblyName, string typeName)`** | 根据程序集名称和类型名称创建实例               | 动态加载跨程序集的类型（如插件系统）  |
| **`CreateInstance<T>()`**                                  | 泛型版本，直接返回 `T` 类型实例                | 简化泛型对象的创建流程                |

注意，Activator无法创建unity的monobehavior实例，具体原因是创建unity中的实例涉及到unity的native memory，而直接反射创建的类在managed memory中。

## 具体应用

1、初始化一些类，避免手动将一些类加入字典，可以先对程序集扫一遍，把有的类中的方法，数据，加入字典中存起来，方便其他代码获取。除此之外，可以配合自定义attribute，来进行扫描，提高效率。例如实现状态机，所有的状态通过反射在开始的时候载入。

2、在做一些自定义的编辑器工具时，可以通过反射批量重命名材质的名字，也可以批量为指定的editor添加功能。



