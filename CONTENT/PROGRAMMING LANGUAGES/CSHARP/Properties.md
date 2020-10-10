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
