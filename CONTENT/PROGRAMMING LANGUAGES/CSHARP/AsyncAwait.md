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
