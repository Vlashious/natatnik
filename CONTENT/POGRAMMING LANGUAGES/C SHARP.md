# C#
[Go back](../../README.md)
## Static Types
Static members created for the whole lifetime of your program, thus they're never going to be destructed. It's convenient to use them, so don't be shy **put some** **_MORE_**. But there are some limitations.
### Shared and Instance members
Shared members, like static members, are shared among all the instances of the class, but can be accessed only through the class. Instance members live within some _instance_ of a class and destroyed when the _instance_ is destroyed, while Shared members live within the _class_ and are not destroyed when some instance is destroyed.
```c#
public class Item
{
    public int name; // Instance member.
    public int ID; // Instance member.

    public static int itemCount = 0; // Shared member.
}

Item bread = new Item(); // bread.itemCount will cause a compile error.
Item.itemCount++; // itemCount = 1;
bread = null; // Instance is deleted, but Item.itemCount still can be accessed;
              // Item.itemCount = 1;
Item sword = new Item(); // sword.itemCount will cause a compile error.
Item.itemCount++; // itemCount = 2;
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
## Tips
- It's always better to use switch statements if there are more than 2 else-if statements.