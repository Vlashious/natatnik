# C#
[Go Back](../../../README.md)

- [Data structures](DataStructures.md)
- [Properties](Properties.md)
- [Delegates, Actions, Events, Func](Delegates.md)
- [LINQ](LINQ.md)
- [async / await](AsyncAwait.md)

## Namespaces
Allow you to develope code without interfering with the code already exists. It's always a good practice to use a namespace.
```c#
namespace MySpace
{

}
```

## Reference and Value types
Reference types are stored in heap, when Value types are stored in stack. It means that the reference types actually store the adress **location** where the information is located, when value types store the data itself.<br>
When changing a reference type inside a method, everything will be changed normally outside of the method too, because the method has changed the data in the certain location in memory.<br>
When changing a value type inside a method, everything will be changed **INSIDE** of the method only, what means when the method has finished, every change is being lost, because the method had been working with the **COPY** of the data of the value type rather than the **ACTUAL** data. If you want to change the value of the value type variable **INSIDE** a method, you should use `ref` keyword:
```c#
void Method(ref int arg)
{
    arg += 5;
}

int number = 1;
Method (ref number); // When calling the method ref keyword is used too.
```

## Enums
Allow you to create readable selections that are based of integer values and you can use them in the English format. You can cast it to the integer type by using `(int)`.
```c#
// Code without enums.
public class SelectDifficulty
{
    public int easy;
    public int normal;
    public int hard;
    public int currentSelectedLever;
}

// Code with enums.
public class SelectDifficulty
{
    public enum LevelSelector
    {
        Easy, // 0
        Normal, // 1
        Hard // 2
    }

    public LevelSelector currentDifficulty;

    public CheckDifficulty()
    {
        switch(currentDifficulty)
        {
            case LevelSelector.Easy:
                // Some code.
                break;
            case LevelSelector.Normal:
                // Some code.
                break;
            case LevelSelector.Hard:
                // Some code.
                break;
        }
    }
}

public class SelectDifficulty
{
    public enum LevelSelector
    {
        Easy = 0, // 0
        Normal = 43, // 43
        Hard = 33 // 33
        ...       // 34
        // You can assign values in enums manually.
    }
}
```

## Utility Helper Classes
Utility classes are basically helper methods that you have created, so that you can ideally streamline your developement process. Imagine you are creating objects for several of your applications: insted of manually copying and pasting your code into each application, you could have one utility helper class that has that method.
```c#
public static class UtilityHelper
{
    public static void CreateObject()
    {
        GameObject.CreatePrimitive(PrimitiveType.Cube);
    }

    public static void SetPosToZero(GameObject obj)
    {
        obj.transform.postion = Vector3.zero;
    }

    public static void ChangeColor(GameObject obj)
    {
        Color color = new Color(Random.value, Random.value, Random.value);
        obj.GetComponent<Renderer>().material.color = color;
    }
}

public class Player : MonoBehavior
{
    private void Update()
    {
        if (Input.GetKeyDown(KeyCode.Space))
        {
            UtilityHelper.CreateObject();
        }

        if (Input.GetKeyDown(KeyCode.R))
        {
            UtilityHelper.SetPosToZero(this);
        }

        if (Input.GetKeyDown(KeyCode.C))
        {
            UtilityHelper.ChangeColor(this);
        }
    }
}
```

## Static Types
Static members created for the whole lifetime of your program, thus they're never going to be destructed. It's convenient to use them, so don't be shy **put some** **_MORE_**. But there are some limitations.<br>
If a class is static, it can **not** inherit nor have **non-static** methods within it.
### Static and Instance members
Shared members, like static members, are shared among all the instances of the class, but can be accessed only through the class. Instance members live within some _instance_ of a class and destroyed when the _instance_ is destroyed, while Static members live within the _class_ and are not destroyed when some instance is destroyed.
```c#
public class Item
{
    public int name; // Instance member.
    public int ID;   // Instance member.

    public static int itemCount = 0; // Shared member.
}

Item bread = new Item(); // bread.itemCount will cause a compile error.
Item.itemCount++;        // itemCount = 1;

bread = null;            // Instance is deleted, but Item.itemCount still can be accessed;
                         // Item.itemCount = 1;

Item sword = new Item(); // sword.itemCount will cause a compile error.
Item.itemCount++;        // itemCount = 2;
```
### Static member initialization
```c#
public class Employee
{
    public Employee()
    {
        Console.WriteLine("Instance members"); // Called everytime the instance is created.
    }

    static Employee()
    {
        Console.WriteLine("Static members");   // Is called once.
    }
}

var e1 = new Employee();
var e2 = new Employee();
var e3 = new Employee();
```
Output:
```
Static members
Instance members
Instance members
Instance members
```
