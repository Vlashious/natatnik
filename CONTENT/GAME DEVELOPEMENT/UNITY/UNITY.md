# Unity Engine
[Go Back](../../../README.md)
## Unity Basics
### Debug Commands
`Debug.DrawRay(position, direction, color)` draws a line in the Scene View.<br>
### Mesh Renderer
Controls the render of an object, for example, it's color.<br>
`object.GetComponent<Renderer>()` returns the Mesh Renderer of the object.<br>
`object.GetComponent<Renderer>().material` grants access to the material of the object. `material.color` lets get and set the color of the material.
### Quaternion
Quaternion is the rotation of an object in x, y, z and w position. Used by default in Unity.<br>
`Quaternion.identity` means no rotation.<br>
`Quaternion.Euler(x, y, z)` rotation in Euler.<br>
[`Quaternion.LookRotation`](#quaternionlookrotation-implementation-example) searches for rotation to a certain point in space.<br>
[`Quaternion.Slerp`](#quaternionlookrotation-implementation-example) spherical interpolation. In combination with `Quaternion.LookRotation()` can give an effect of smooth look-to-object movement rather then instant snap.
### Coroutine
Coroutines are being used to call some event while not braking the flow of your main code execution. For example, if we wanted to increment some variable every 1 second and did it in the `Update()` method, it would've been a mess. What we can do is just create a `Coroutine` to do it.
```c#
public class Message : MonoBehavior
{

    private int _points;

    void Start()
    {
        StartCoroutine(IncrementPoints());
    }

    IEnumerator IncrementPoints()
    {
        while(true)
        {
            _points++;

            // Wait for 1 second.
            yield return new WaitForSeconds(1.0f);
        }
    }
}
```
## Tips
### How to control the time flow
`Time.timeScale` lets you control the time flow of the game.<br>
`Time.timeScale = 0` lets you easily put your game on pause.<br>
`Time.timeScale = 1` sets the time flow back to normal.
### Moving to different color space
`Edit > Project Settings > Player > Color Space`
### Usage of Loops
- Iterator through all monsters in a "blast" zone and deal damage.
- Shatter an object (looping through each piece and adding physics).
- When you add an item to a player inventory, you need to loop through the database to see if that item exists before adding it.
- You want to turn all game objects in view RED (loop through each object and affect them).
### Usage of Custom Classes
Custom Classes are classes that don't derive from MonoBehavior class. Custom Classes are useful when creating something that has identical methods/variables/etc. Like Item system, Power-up system. So first, you design what an Item **IS**, then you create items via the constructor.
## Code Examples
### Destructable objects
Created through the smoke-and-mirror effect, where the whole object gets replaced by the broken one.
```c#
public class Object : MonoBehavior
{
    public GameObject fracturedObject;
    public GameObject explosionEffect;

    void Update()
    {
        if (Input.GetKeyDown(KeyCode.Space))
        {
            // Play the explosion.
            Instantiate(explosionEffect, transform.position, Quaternion.identity);

            // Instantiate a fractured object.
            GameObject fracturedObj = Instantiate(fracturedObject, transform.position, Quaternion.identity);

            // Get Rigidbody of the little piecies.
            Rigibody[] allRigidBodies = fracturedObj.GetComponentsInChildren<RigidBody>();
            if (allRigidBodies.Length > 0) {
                foreach (var body in allRigidBodies)
                {
                    // Apply the explosion force to each little piece.
                    body.AddExplosiobForce(500, transform.position, 1);
                }
            }
            // Delete the whole object.
            Destroy(this.gameOjbect);
        }
    }
}
```

### Quaternion.LookRotation implementation example
```c#
public class Aim : MonoBehavior
{
    private GameObject aimObj;

    void Update()
    {
        // Find the direction to the aim.
        Vector3 directionToFace = aimObj.transform.position - transform.position;

        // Draw ray from the object to the aim.
        Debug.DrawRay(transform.position, directionToFace, Color.green);

        // Get rotation.
        Quaternion endPos = Quaternion.LookRotation(directionToFace);

        // Smoothly slerp between the current rotation and the rotation needed.
        transform.rotation = Quaternion.Slerp(transform.position, endPos, Time.deltaTime);
    }
}
```
### Custom RPG Spell system example
```c#
    public class Spell
    {
        public string name;
        public int levelRequired;
        public int itemIdRequired;
        public int expGain;

        public Spell(string name, int levelRequired, int itemIdRequired, int expGain)
        {
            this.name = name;
            this.levelRequired = levelRequired;
            this.itemIdRequired = itemIdRequired;
            this.expGain = expGain;
        }

        public void Cast()
        {
            Debug.Log(name + " is casted.");
        }
    }

    public class Wizard : MonoBehavior
    {
        private Spells[] spells;
        public int level = 1;
        public int exp;

        void Start()
        {
            spells = new Spells[2];
            spells[0] = new Spell("Fire Blast", 1, 27, 35);
            spells[1] = new Spell("Ice Blast", 2, 27, 35);
        }

        void Update()
        {
            if (Input.GetKeyDown(KeyKode.Space))
            {
                foreach(var spell in Spells)
                {
                    if(spell.levelRequired == level)
                    {
                        spell.Cast();
                        exp += spell.expGain;
                    }
                }
            }
        }
    }
```
### Inventory Item DB System
```c#
public class Item
{
    public string name;
    public string description;
    public string ID;
}

public class ItemDB : MonoBehavior
{
    public List<Item> itemDatabase = new List<Item>();

    public void AddItem(int ID, Player player)
    {
        foreach (var item in itemDatabase)
        {
            if (item.ID == ID)
            {
                Debug.Log("We have a match!");
                player.inventory[0] = item;
                return;
            }
        }

        Debug.Log("Item was not found :(");
    }

    public void RemoveItem(int ID, Player player)
    {
        foreach (var item in itemDatabase)
        {
            if (item.ID = ID)
            {
                player.inventory[0] = null;
            }
        }
    }
}

public class Player : MonoBehavior
{
    public Item[] inventory = new Item[10];

    private ItemDB _itemDB;

    private void Start()
    {
        _itemDB = GameObject.Find("ItemDB").GetComponent<ItemDB>();
    }

    private void Update()
    {
        if (Input.GetKeyDown(KeyCode.Space))
        {
            _itemDB.AddItem(0, this);
        }
        else if (Input.GetKeyDown(KeyCode.R))
        {
            _itemDB.RemoveItem(0, this);
        }
    }
}
```
## Post Processing stack
### How to enable
Fisrt, import a Post Processing Stack from a `Package Manager`. Put a `Post Process Layer` on your `Camera`. In `Inspector`, create a new Layer explicitly for Post Processing. Assign this layer in a `Post Process Layer` on `Camera`. Next step is to create a Volume. Create an empty object, add to it a component called `Post Process Volume`.<br>
!IMPORTANT! in order for it to work, all OBJECTS and VOLUMES that participate in post processing must be layered on the post processing layer created earlier.