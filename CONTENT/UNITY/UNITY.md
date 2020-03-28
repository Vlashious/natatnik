# Unity Engine
[Go Back](../../README.md)
## Post Processing stack
### How to enable
Fisrt, import a Post Processing Stack from a `Package Manager`. Put a `Post Process Layer` on your `Camera`. In `Inspector`, create a new Layer explicitly for Post Processing. Assign this layer in a `Post Process Layer` on `Camera`. Next step is to create a Volume. Create an empty object, add to it a component called `Post Process Volume`.
!IMPORTANT! in order for it to work, all OBJECTS and VOLUMES that participate in post processing must be layered on the post processing layer created earlier.
### Hot to pause a game with ease
`Time.timeScale = 0` lets you easily put your game on pause. Also it can be used to control the time flow (slow motion, etc.)
### Moving to different color space
`Edit > Project Settings > Player > Color Space`
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

            // Instantiate a broken object.
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

            Destroy(this.gameOjbect); // Delete the whole object.
        }
    }
}
```
