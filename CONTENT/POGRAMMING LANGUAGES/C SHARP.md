# C#
[Go back](../../README.md)
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