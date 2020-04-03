# C#
[Go Back](../../README.md)
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
### Utility Helper Classes
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
## Properties
### Simple Properties
Can be treated as "smart variables". Not only can you retrieve information from them, but you can also run functionality through them. To create them, you need a variable they're working with manually.
```c#
private bool isGameOver = false;

public bool IsGameOver
{
    get
    {
        return isGameOver;
    }
    set
    {
        if(value == true)
        {
            // Additional functionality through the variable.
            Debug.Log("Oh no the game is over!");
        }
        // value is a hidden default member which represents the data being passed to the property
        isGameOver = value;
    }
}
```
### Auto Properties
Is a cleaner way to do a property, when additional functionality is not needed.
```c#
public bool IsGameOver { get; set; } // Every script can access and set this property.
public bool IsGameOver { get; private set; } // Only the class itself can set this property, external scripts can only read.
public bool IsGameOver { get; protected set; } // ...
```
## Namespaces
Allow you to develope code without interfering with the code already exists. It's always a good practice to use a namespace.
```c#
namespace MySpace
{

}
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
## Dictionary
Allows quickly retrieve information. Rather then looping through a list, you can retrieve information by its key instantly. Keys must be unique and must exist.<br>
`dictionary.ContainsKey(int)` checks if certain key is in the dictionary.
```c#
public Dictionary<int, Item> itemDictionary = new Dictionary<int, Item>();

private void Start()
{
    itemDictionary.Add(0, new Item());
}
```
### When to use
When working with large lists, like some implementation of shop.
### Looping through a dictionary
```c#
// Looping through each PAIR in the dictionary.
foreach (var item in itemDictionary)
{
    // ...
}

// Looping through the KEYS only.
foreach (var key in itemDictionary.Keys)
{
    // ...
}

// Looping through the VALUES only.
foreach (var item in itemDictionary.Values)
{
    // ...
}
```
## Delegates
A variable, which holds a method or several methods.<br>
`public delegate void ChangeColor(Color newColor)` delegate signature.<br>
`public ChangeColor onColorChange` creating a variable.<br>
```c#
public delegate void ChangeColor(Color newColor);
public ChangeColor onColorChange;

public delegate void OnComplete();
public OnComplete onComplete;

private void Start()
{
    onColorChange = UpdateColor;
    onColorChange(Color.green);
    
    onComplete += Task1;
    onComplete += Task2;
    onComplete += Task3;
    
    onComplete -= Task3;
    if(onComplete)
    {
        onComplete();
    }
}

public void UpdateColor(Color newColor)
{
    Debug.Log("Changing color!");
}

private void Task1()
{
    Debug.Log("Task 1 finished!");
}

private void Task2()
{
    Debug.Log("Task 2 finished!");
}

private void Task3()
{
    Debug.Log("Task 3 finished!");
}
```
## Events (Specialized Delegates)
They allow us to create a type of broadcast systems, that allows other classes and objects to subscribe and desubscribe to our delegates. Isolates functionality between different parts of the program. Makes it possible to create extremely flexible systems. When delegate variables can be invoked in different parts of the program, events allow only subscribing/unsubscribing to the delegate itself.<br>
!IMPORTANT! somewhere in the code there must be an unsubscribing line, usually when a subscribed object gets destroyed.
```c#
public class Main : MonoBehavior
{

    public delegate void ActionClick();
    public static event ActionClick onClick;

    public void ButtonClick()
    {
        if(onClick)
        {
            onClick();
        }
    }
}

public class Cube : MonoBehavior
{

    private void Start()
    {
        Main.onClick += TurnRed;
    }

    public void TurnRed()
    {
        GetComponent<MeshRenderer>().material.color = Color.red;
    }
    
    private void OnDisable()
    {
        Main.onClick -= TurnRed;
    }
}
```
## Actions
Actions are safe-typy combined delegates and events.<br>
`public Action<T> action` signature of an action.
## Return-type Delegates
Non-void delegates.<br>
`public Func<T, IResult> delegate;` T is on the input, and IResult is the output.
## Lambda Expression
```c#
public Func<int, string> CharacterLength;

CharacterLength = (name) => name.Length; // => stands for GOTO

int count = CharacterLength("Joh"); // count = 3
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
## LINQ (Language Integrated Query)
`using System.Linq` is the way to access it.
### Queries
Allows us to filter through data, like arrays or lists.<br>
```c#
public string[] names = {"jon", "alex", "uladz", "maryna", "jon", "uladz"};

bool nameFound = names.Any(name => name == "jon"); // Going to return true/false if jon is in the array using a lambda expression.
// Any is like an inline while loop.

bool nameFound = names.Contains("jon"); // Does the container have jon name?
// Contains is a lighter version of Any without a lambda expression.

var uniqueNames = names.Distinct() // Removes all the duplicates and puts them in a container.
// Distinct removes all the duplicates.

var result = names.Where(name => name.Length > 5); // Creates a list based on names array with names > 5 characters each.
// Where allows to create container based on some containers using some predicate logic.

OrderByDescending() // Sorts list in a descending order. (x) => x
Average() // Computes the average value.
```
### Query Syntax
Query Syntax is basically using Linq library in a query format, like SQL, SQLite, MySQL, etc.
```c#
public int[] score = new int[] {97, 81, 92, 60};

var scoreQuerySyntax = 
    from score in scores
    where score > 80
    select score:

var scoreMethodSyntax = scores.Where(score => score > 80);

// scoreQuerySyntax = {97, 81, 92}.
// scoreMethodSyntax = {97, 81, 92};
```
## Tips
- It's always better to use switch statements if there are more than 2 else-if statements.
- Void return type lambda expressions are used with Action keyword, not Func.
