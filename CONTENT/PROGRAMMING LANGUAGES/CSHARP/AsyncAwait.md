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

## Task.WhenAny
This method takes a sequence of tasks and returns a task that completes when any of the tasks complete. The result of the returned task is the task that completed.
```c#
// Returns the length of data at the first URL to respond.
private static async Task<int> FirstRespondingUrlAsync(string urlA, string urlB)
{
    var httpClient = new HttpClient();
    // Start both downloads concurrently.
    Task<byte[]> downloadTaskA = httpClient.GetByteArrayAsync(urlA);
    Task<byte[]> downloadTaskB = httpClient.GetByteArrayAsync(urlB);
    // Wait for either of the tasks to complete.
    Task<byte[]> completedTask =
    await Task.WhenAny(downloadTaskA, downloadTaskB);
    // Return the length of the data retrieved from that URL.
    byte[] data = await completedTask;
    return data.Length;
}
```
You have a collection of tasks to await, and you want to do some processing on each task after it completes. However, you want to do the processing for each one as soon as it completes, not waiting for any of the other tasks.
```c#
static async Task<int> DelayAndReturnAsync(int val)
{
    await Task.Delay(TimeSpan.FromSeconds(val));
    return val;
}
// Currently, this method prints "2", "3", and "1".
// We want this method to print "1", "2", and "3".
static async Task ProcessTasksAsync()
{
    // Create a sequence of tasks.
    Task<int> taskA = DelayAndReturnAsync(2);
    Task<int> taskB = DelayAndReturnAsync(3);
    Task<int> taskC = DelayAndReturnAsync(1);
    var tasks = new[] { taskA, taskB, taskC };
    // Await each task in order.
    foreach (var task in tasks)
    {
        var result = await task;
        Trace.WriteLine(result);
    }
}

// After refactoring.
static async Task<int> DelayAndReturnAsync(int val)
{
    await Task.Delay(TimeSpan.FromSeconds(val));
    return val;
}

static async Task AwaitAndProcessAsync(Task<int> task)
{
    var result = await task;
    Trace.WriteLine(result);
}
// This method now prints "1", "2", and "3".
static async Task ProcessTasksAsync()
{
    // Create a sequence of tasks.
    Task<int> taskA = DelayAndReturnAsync(2);
    Task<int> taskB = DelayAndReturnAsync(3);
    Task<int> taskC = DelayAndReturnAsync(1);
    var tasks = new[] { taskA, taskB, taskC };
    var processingTasks = tasks.Select(async t =>
    {
        var result = await t;
        Trace.WriteLine(result);
    }).ToArray();
    // Await all processing to complete
    await Task.WhenAll(processingTasks);
}
```
