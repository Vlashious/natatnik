# Command Pattern
Is nothing more than a behavioral pattern that lets you record the actions or events of your application. It also allows you the ability to isolate your features into their own classes, for example, instead of having a player class that checks for user input, and then perfoms an action, you would isolate the entire action into its own class, allowing you to keep track of the entire action and control it without any distraction. This will ultimately allow to create a rewind system, or queue system in your application.<br>
Can be really useful in strategy or tactics games.<br>
Command Pattern in a nutshell just remembers the actions being performed.<br>
Prons:
- Features are isolated and contained withing themselves, they are not relying upon other software work.
- You can change one action without affecting another.
Cons:
- Adds a layer of complexity.
- Additional class files, which is not terrible, but can be seen as a con.
## Creating the interface for commands
```c#
public interface ICommand
{
    void Execute();

    void Undo();
}
```
## User Click
```c#
public class UserClick : MonoBehavior
{
    private void Update()
    {
        if(Input.GetMouseButtonDown(0))
        {
            Ray rayOrigin = Camera.main.ScreenPointToRay(Input.mousePosition);
            RaycastHit hitInfo;

            if(Physics.Raycast(rayOrigin, out hitInfo))
            {
                if(hitInfo.collider.tag == "Cube")
                {
                    // Execute the click command.
                    ICommand click = new ClickCommand(hitInfo.collider.gameObject, new Color(
                        Random.value, Random.value, Random.value
                    ));
                    click.Execute();
                    CommandManager.Instance.AddCommand(click);
                }
            }
        }
    }
}
```
## User Click command
```c#
public class ClickCommand : ICommand
{
    private GameObject _cube;
    private Color _color;
    private Color _previousColor;

    public ClickCommand(GameObject cube, Color color)
    {
        this._cube = cube;
        this._color = color;
    }

    public void Execute()
    {
        _previousColor = _cube.GetComponent<MeshRenderer>.material.color;
        _cube.GetComponent<MeshRenderer>.material.color = _color;
    }

    public void Undo()
    {
        _cube.GetComponent<MeshRenderer>.material.color = _previousColor;
    }
}
```
## Command Manager
```c#
public class CommandManager : MonoBehavior
{
    private static CommandManager _instance;
    public static CommandManager Instance
    {
        get
        {
            if(_instance == null)
            {
                Debug.Log("Command manager is null");
            }
            return _instance;
        }
    }

    private List<ICommand> _commandBuffer = new List<ICommand>();

    private void Awake()
    {
        _instance = this;
    }

    public void AddCommand(ICommand command)
    {
        _commandBuffer.Add(command);
    }

    public void Play()
    {
        StartCoroutine(PlayRoutine());
    }

    IEnumerator PlayRoutine()
    {
        foreach(var command in _commandBuffer)
        {
            command.Execute();
            yield return new WaitForSeconds(1f);
        }
    }

    public void Reverse()
    {
        StartCoroutine(RewindRoutine());
    }

    IEnumerator RewindRoutine()
    {
        foreach(var command in Enumerable.Reverse<_commandBuffer>)
        {
            command.Undo();
            yield return new WaitForSeconds(1f);
        }
    }
}
```