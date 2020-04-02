# SINGLETON
A software engineering concept, where you have global access to a class, and this class only exist in one instance.
```c#
// Example using c#

public class GameManager : MonoBehavior
{
    private static GameManager _instance;
    public static GameManager Instance
    {
        get
        {
            if(_instance == null)
            {
                Debug.LogError("Game Manager is null");
                GameObject go = new GameObject("Manager"); // Lazy instantiation
                go.AddComponent<GameManager>();            // if singleton doesn't exist.
                                                           // !IMPORTANT! It won't dynamically populate
                                                           // all variables, so you have to do it manually.
                                                           // It's the only downfall of lazy instantiation.
            }
            else
            {
                return _instance;
            }
        }
    }

    private void Awake()
    {
        _instance = this; // Game Manager initialization.
    }

    public void DisplayName()
    {
        Debug.Log("My name is John");
    }
}

public class Player : MonoBehavior
{
    private GameManager _gm;

    private void Start()
    {
        _gm = GameObject.Find("Game_Manager").GetComponent<GameManager>();
        _gm.DisplayName(); // Accessing game manager without a singleton pattern.

        GameManager.Instance.DisplayName(); // Accesing game manager with a singleton pattern.
    }
}
```
# MONO SINGLETON
Allows converting of any class into a singleton.
```c#
public abstract class MonoSingleton<T> : MonoBehavior where T : MonoSigleton<T>
{
    private static T _instance;
    public static T Instance
    {
        get
        {
            if(_instance == null)
            {
                Debug.Log("The " + typeof(T).ToString() + " is null");
            }
            else
            {
                return _instance;
            }
        }
    }

    private void Awake()
    {
        _instance = this as T;

        Init();
    }

    public virtual void Init() {} // Optional to override.
}

public class Player : MonoSingleton<Player>
{
    public string name;

    public override void Init()
    {
        base.Init();
        Debug.Log("Player initialized.");
    }
}
```