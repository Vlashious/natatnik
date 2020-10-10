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
