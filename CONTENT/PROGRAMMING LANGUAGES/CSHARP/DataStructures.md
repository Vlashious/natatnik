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
