# 1 Inventory Window

Project 窗口右键单击 > Create > Inventory 即可创建 Inventory 资产，双击创建的 Asset 会打开 Inventory Window 窗口。

![00](images/00.png)

**1. Groups**

用于显示当前的文件夹结构，右键菜单可添加新文件夹、改名、删除文件夹，左键按住可拖拽文件夹来移动位置。

![01](images/01.png)

**2. Types**

默认显示继承自 InventoryAsset.Object 的类型，选中类型拖拽到 Instances 窗口会在选中的文件夹创建此类型的实例，创建的实例同时会保存为 Inventory 的子资产。

![01](images/03.png)

右键菜单可刷新此列表，或者显示继承自 ScriptableObject 的类型，显示的列表包含继承自 InventoryAsset.Object 的类型，这些类型同样可以通过拖拽到 Instances 窗口来创建实例。

![04](images/04.png)

![02](images/02.png)

> 选中继承自 InventoryAsset.Object 类型所在的 C# 文件，在 Inspector 窗口可设置默认 Icon，此处设置的图标会在 Types 窗口显示。
>
> 不要随意实例化不认识的继承自 ScriptableObject 实例，这可能导致编辑器或插件的设置被异常修改而报错。

![05](images/05.png)

**3. Instances**

显示选中的文件夹所包含的实例，在 Groups 窗口点击空白处可选中根文件夹。

右键菜单可删除实例、重命名、刷新、选中实例类型的脚本、选中实例。

![06](images/06.png)

左键可拖拽到 Groups 窗口上的文件夹以改变此实例的父文件夹，拖拽到 Groups 窗口的空白处会修改父文件夹为根文件夹。

左键可拖拽到可接收实例类型的变量字段上为变量赋值。

![07](images/07.png)

左键拖拽到此窗口里其它实例上可移动在此文件夹中的位置，鼠标弹起时会根据位置将拖动的实例移动到选中的实例之上或之下。

**4. Inspector**

显示的内容与编辑器里 Inspector 窗口的内容一致，因为 Unity 内置格式的原因，带有 Foldout 元素的部分排版会有些许差异。

![08](images/08.png)

右键菜单可选中实例类型的脚本、选中实例。

![09](images/09.png)

继承自 InventoryAsset.Object 的类型通过在此窗口修改 Icon 的值，来修改 Instances 窗口中显示的图标，修改完后需在 Instances 窗口右键刷新来刷新图标。

![10](images/10.png)

继承自 ScriptableObject 的类型在 Instances 窗口会显示 Select Icon。![11](images/11.png)

# 2 Inventory Asset

直接通过 Inventory 来查找其中的资产会有一定的效率问题，而且可能会错误的使用一些用于 Inventory Window 的方法。为了避免这些问题，不直接使用 Inventory 资产，而是通过 Inventory 生成 InventoryAsset 资产，通过 InventoryAsset 查找子资产效率最高，并且没有其它方法来干扰查找。

选中 Inventory 资产，在 Inspector 窗口点击 Generate Runtime Asset 会生成 InventoryAsset 资产，Asset Path 根路径为 Assets 文件夹。

![12](images/12.png)

在脚本中引用 InventoryAsset 实例，然后通过此实例查找子资产。

![13](images/13.png)

可通过路径、类型、名称、索引等多种方式查找子资产。

**1. 通过路径查找**

通过路径查找文件夹：

* 从根文件夹查找：

  ~~~c#
  public Folder InventoryAsset.FindFolder(string path);
  public Folder InventoryAsset.Folder.FindFolder(string path);
  ~~~

* 从当前文件夹查找：

  ~~~c#
  public Folder InventoryAsset.Folder.FindFolderRelative(string path);
  ~~~

~~~c#
InventoryAsset.Folder fruits = _Asset.FindFolder("Foods.Fruits");

InventoryAsset.Folder foods = _Asset.FindFolder("Foods");
InventoryAsset.Folder fruits = foods.FindFolderRelative("Fruits");
~~~

通过路径查找资产：

* 从根文件夹查找：

  ~~~c#
  public Folder InventoryAsset.FindObject(string path);
  public Folder InventoryAsset.Folder.FindObject(string path);
  ~~~

* 从当前文件夹查找：

  ~~~c#
  public Folder InventoryAsset.Folder.FindObjectRelative(string path);
  ~~~

~~~c#
ScriptableObject apple = _Asset.FindObject("Foods.Fruits.Apple");
ScriptableObject apple = _Asset.FindFolder("Foods").FindObject("Foods.Fruits.Apple");
ScriptableObject apple = _Asset.FindFolder("Foods").FindObjectRelative("Fruits.Apple");
ScriptableObject apple = _Asset.FindFolder("Foods.Fruits").FindObjectRelative("Apple");
~~~

**2. 通过类型和名称查找**

从根文件夹查找：

~~~c#
public List<T> InventoryAsset.Query<T>(string name = null) where T : ScriptableObject;
public T InventoryAsset.Q<T>(string name = null) where T : ScriptableObject;
~~~

从当前文件夹查找：

~~~c#
public List<T> InventoryAsset.Folder.Query<T>(string name = null) where T : ScriptableObject;
public T InventoryAsset.Folder.Q<T>(string name = null) where T : ScriptableObject;
~~~

其中 Query 为查询所有符合条件的对象，Q 为查找第一个符合条件的对象，当 name 为空时仅通过类型查找。

~~~c#
List<Food> foods = _Asset.Query<Food>();
Food apple = _Asset.Q<Food>("Apple");
List<Food> fruits = _Asset.FindFolder("Foods.Fruits").Query<Food>();
~~~

**3. 通过索引查找**

选中 InventoryAsset 资产，在 Inspector 窗口点击 Generate Reference Script 会生成 索引脚本。

![14](images/14.png)

查找文件夹：

~~~c#
InventoryAsset.Folder foods = _Asset[ShopInventory.Foods.index];
InventoryAsset.Folder fruits = _Asset[ShopInventory.Foods.Fruits.index];
~~~

查找子资产对象：

~~~c#
ScriptableObject bread = _Asset[ShopInventory.Foods.Bread];
ScriptableObject apple = _Asset[ShopInventory.Foods.Fruits.Apple];
~~~

> 此方法无查询消耗。

生成的脚本：

~~~c#
//------------------------------------------------------------------------------
// <auto-generated>
//     This code was auto-generated by InventoryAssetEditor
//     version x.x
//     from Assets/Demo/Inventory/ShopInventory/ShopInventory.asset
//
//     Changes to this file may cause incorrect behavior and will be lost if
//     the code is regenerated.
// </auto-generated>
//------------------------------------------------------------------------------

public class ShopInventory
{
    public const int index = 5;
    public class Foods
    {
        public const int index = 3;
        public const long Bread = 12884901888;
        public const long Cheese = 12884901889;
        public const long Mushroom = 12884901890;
        public class Fruits
        {
            public const int index = 0;
            public const long Apple = 0;
            public const long GreenApple = 1;
        }
        public class Meats
        {
            public const int index = 1;
            public const long FishSteak = 4294967296;
            public const long Ham = 4294967297;
            public const long Meat = 4294967298;
        }
        public class Drinks
        {
            public const int index = 2;
            public const long Beer = 8589934592;
            public const long Wine = 8589934593;
            public const long Wine2 = 8589934594;
        }
    }
    public class Potions
    {
        public const int index = 4;
        public const long Potion = 17179869184;
    }
}
~~~

