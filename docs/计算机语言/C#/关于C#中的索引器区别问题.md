# C#中索引器的实现区别（附网上的错误

在Unity中，如果说你想遍历修改一个Vector3 list的z值，应该怎么做？

在看到这个问题之后，你有可能会想到以下两种解法

``` c#
List<Vector2> list = new List<Vector2>();
//使用foreach遍历
foreach(var item in list)
{
    item.z = 1;
}
//使用for遍历
for(int i = 0; i < list.Count; i++)
{
    list[i].z = 1;
}
```

先说结论，第一种是错的，第二种也是错的。（

众所周知，C#有值类型和引用类型，在解决问题之前，我们先看看值类型和引用类型遍历的所有情况。

``` c#
public class TestClass
{
    public int a;
    public int b;
}
List<Vector2> vector2s = new List<Vector2>();
Vector2[] vector2Array = new Vector2[5];
List<TestClass> testClasses = new List<TestClass>();
TestClass[] testClassArray = new TestClass[5];
private void Start()
{
    //使用foreach遍历List<Vector2>，修改List<Vector2>中的元素的x值，会报错无法改变foreach中的元素，报错CS1656
    foreach (var vector2 in vector2s)
    {
        vector2.x = 1;
    }
    //使用foreach遍历List<Vector2>，直接new元素，会报错无法改变foreach中的元素,报错CS1656
    foreach (var item in vector2s)
    {
        item = new Vector2(1, 1);
    }

    //使用for循环遍历List<Vector2>，修改List<Vector2>中的元素的x值，会报错CS1612
    for (int i = 0; i < vector2s.Count; i++)
    {
        vector2s[i].x = 1;
    }
    //使用for循环遍历List<Vector2>，直接new元素，不会报错
    for (int i = 0; i < vector2s.Count; i++)
    {
        vector2s[i] = new Vector2(1, 1);
    }
    //使用foreach遍历Vector2[]，修改Vector2[]中的元素的x值，报错CS1656
    foreach (var vector2 in vector2Array)
    {
        vector2.x = 1;
    }
    //使用foreach遍历Vector2[]，直接new元素，会报错无法改变foreach中的元素,报错CS1656
    foreach (var item in vector2Array)
    {
        item = new Vector2(1, 1);
    }
    //使用for循环遍历Vector2[]，修改Vector2[]中的元素的x值，不会报错
    for (int i = 0; i < vector2Array.Length; i++)
    {
        vector2Array[i].x = 1;
    }
    //使用for循环遍历Vector2[]，直接new元素，不会报错
    for (int i = 0; i < vector2Array.Length; i++)
    {
        vector2Array[i] = new Vector2(1, 1);
    }
    //使用foreach遍历List<TestClass>，修改List<TestClass>中的元素的a值，不会报错
    foreach (var testClass in testClasses)
    {
        testClass.a = 1;
    }
    //使用foreach遍历List<TestClass>，直接new元素，会报错无法改变foreach中的元素,报错CS1656
    foreach (var item in testClasses)
    {
        item = new TestClass();
    }
    //使用for循环遍历List<TestClass>，修改List<TestClass>中的元素的a值，不会报错
    for (int i = 0; i < testClasses.Count; i++)
    {
        testClasses[i].a = 1;
    }
    //使用for循环遍历List<TestClass>，直接new元素，不会报错
    for (int i = 0; i < testClasses.Count; i++)
    {
        testClasses[i] = new TestClass();
    }
    //使用foreach遍历TestClass[]，修改TestClass[]中的元素的a值，不会报错
    foreach (var testClass in testClassArray)
    {
        testClass.a = 1;
    }
    //使用foreach遍历TestClasss，直接new元素，会报错无法改变foreach中的元素,报错CS1656
    foreach (var item in testClasses)
    {
        item = new TestClass();
    }
    //使用for循环遍历TestClass[]，修改TestClass[]中的元素的a值，不会报错
    for (int i = 0; i < testClassArray.Length; i++)
    {
        testClassArray[i].a = 1;
    }
    //使用for循环遍历TestClass[],不会报错
    for(int i = 0; i < testClassArray.Length; i++)
    {
        testClassArray[i] = new TestClass();
    }
}
```

从上述的所有情况可以看出，报错一般就两种.

