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
