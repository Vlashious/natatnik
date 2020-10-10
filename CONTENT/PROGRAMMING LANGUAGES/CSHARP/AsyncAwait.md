# async / await

The Task type has a static method Delay that returns a task that completes after the specified time.
```c#
static async Task<T> DelayResult<T>(T result, TimeSpan delay)
{
    await Task.Delay(delay);
    return result;
}
```

## Reporting progress
There is a need to respond to progress while an asynchronous operation is executing.
We can use the provided IProgress\<T> and Progress\<T> types. Our async method should
take an IProgress\<T> argument; the T is whatever type of progress you need to report:
```c#
static async Task MyMethodAsync(IProgress<double> progress = null)
{
    double percentComplete = 0;
    while (!done)
    {
    if (progress != null)
    progress.Report(percentComplete);
    }
}
```
Calling code can use this in the following way:
```c#
static async Task CallMyMethodAsync()
{
    var progress = new Progress<double>();
    progress.ProgressChanged += (sender, args) =>
    {
        // Do stuff with the progress.
    };
    await MyMethodAsync(progress);
}
```

## Task.WhenAll
This method takes several tasks and returns a task that completes when all of those tasks have completed:
```c#
Task task1 = Task.Delay(TimeSpan.FromSeconds(1));
Task task2 = Task.Delay(TimeSpan.FromSeconds(2));
Task task3 = Task.Delay(TimeSpan.FromSeconds(1));
await Task.WhenAll(task1, task2, task3);
```
If all the tasks have the same result type and they all complete successfully, then the
Task.WhenAll task will return an array containing all the task results:
```c#
Task task1 = Task.FromResult(3);
Task task2 = Task.FromResult(5);
Task task3 = Task.FromResult(7);
int[] results = await Task.WhenAll(task1, task2, task3);
// "results" contains { 3, 5, 7 }
```
