# CSharp

## Linq

#### Difference between Expression<Func<T, bool>> and Func<T, bool>

* [Difference](http://stackoverflow.com/questions/2664841/difference-between-expressionfunc-and-func)
* [Conversion](http://stackoverflow.com/questions/1217749/how-to-convert-an-expressionfunct-bool-to-a-predicatet)

```csharp
Func<T, bool> func = expression.Compile();
Predicate<T> pred = t => func(t);
Predicate<T> pred = func.Invoke;
```

## File and directory handling

```csharp
private IEnumerable<Document> GetFiles(string baseFolder, DirectoryInfo directory)
{
    return directory
        .GetFiles()
        .Where(f => !f.Name.StartsWith("~"))
        .Where(f => f.Extension != ".ini" && f.Extension != ".lnk" && f.Extension != ".config" &&
            f.Extension != ".db" && f.Extension != ".exe")
        .Select(file => new Document(
                        file.FullName.Replace(baseFolder, string.Empty),
                        file.LastWriteTimeUtc.ToShortDateString(),
                        false,
                        GetFileImage(file.Extension))
                        ).ToList();
}

```

### Get Filename from full path

Use Path.GetFileName()

```csharp
public Thumbnail(string fullname, byte[] content, string contentType, bool isDirectory)
{
    Fullname = fullname;
    Name = Path.GetFileName(fullname); 
    Content = content;
    ContentType = contentType;
    IsDirectory = isDirectory;
}
```


## Iterators

### yield

* [Interesting use of the C# yield keyword in Nerd Dinner tutorial](http://stackoverflow.com/questions/1971053/interesting-use-of-the-c-sharp-yield-keyword-in-nerd-dinner-tutorial)

## Task parallelism

* [Difference from the traditional ThreadStart](http://stackoverflow.com/questions/7889746/creating-threads-task-factory-startnew-vs-new-thread)

### Difference

There is a big difference. Tasks are scheduled on the ThreadPool and could even be executed synchronous if appropiate.
If you have a long running background work you should specify this by using the correct Task Option.
You should prefer Task Parallel Library over explicit thread handling, as it is more optimized. Also you have more features like Continuation.

So a StartNew does not guarantee a seperate thread? –  Jon Oct 25 '11 at 13:16

No it doesn´t. It just starts tasks. This could enqueue the task at the thread pool or execute it synchronously. The TPL is about freeing you from managing the threads/concurrency yourself and using the best for your platform (like utilizing cores) –  sanosdole Oct 25 '11 at 13:21

But the reason I create a thread is because I want to process code on another thread whilst the main thread is doing something else. Is there no way to specify that I want to do something always on another thread? –  Jon Oct 26 '11 at 7:09
        
There is the TaskCreationOptions.LongRunning option that will always create another thread, but the whole point is why do you need another thread? If you just want to do something in parallel (Main does sth. while Task runs) it is preferable to let a optimized library decide how to utilize system resources like threads to do this in the most efficient way. –  sanosdole Oct 26 '11 at 8:21
        
This msdn article describes how tasks are scheduled. It covers longrunning & inlining (synchronous execution). msdn.microsoft.com/en-us/library/dd997402.aspx 

### Benefits of Task

* Adding continuations (Task.ContinueWith)
* Waiting for multiple tasks to complete (either all or any)
* Capturing errors in the task and interrogating them later
* Capturing cancellation (and allowing you to specify cancellation to start with)
* Potentially having a return value
* Using await in C# 5
* Better control over scheduling (if it's going to be long-running, say so when you create the task so the task scheduler can take that into account)

```csharp
DataInThread = new Thread(ThreadProcedure);
// Or...
Task t = Task.Factory.StartNew(ThreadProcedure);
```

```csharp
public void Start()
{
    _logger.Info("Starting worker thread...");
    _cancellationToken = _cancellationTokenSource.GetToken();
    try
    {
        _main = Task.Factory.StartNew(StartProcessing, _cancellationToken, TaskCreationOptions.LongRunning, TaskScheduler.Default);
    }
    catch (Exception ex)
    {
        _logger.Fatal("Unable to start Identity Event Publisher", ex);
        throw;
    }
}

public void Stop()
{
    _logger.Info("Stopping");
    _cancellationTokenSource.Cancel();
    try
    {
        Task.WaitAll(_main);
    }
    catch (AggregateException ae)
    {
        ae.Flatten().Handle(ex =>
        {
            return true;
        });
    }
}


```

* [http://stackoverflow.com/questions/6800705/why-is-taskscheduler-current-the-default-taskscheduler](http://stackoverflow.com/questions/6800705/why-is-taskscheduler-current-the-default-taskscheduler)

You need to specify TaskCreationOptions.LongRunning to spawn a new thread. TaskScheduler.Default makes sure you uses default task scheduler. Otherwise, it will set to TaskScheduler.Current, and use whatever scheduler is there.


## Code Contracts

Contract.Requires(sessionManager != null); was greyed out, as ReSharper didn't understand it. By putting "CONTRACTS_FULL" to Conditional Compilation symbol, you can make Resharper to understand it.

* [code-contracts-grayed-out](http://social.msdn.microsoft.com/Forums/en-US/11ddd785-4b6d-4712-ae34-e2086c25db38/code-contracts-statements-grayed-out?forum=codecontracts)
* [Code contract from MSDN](http://msdn.microsoft.com/en-us/library/dd264808(v=vs.110).aspx)

So in order to convert Code Contract into run time checks, you have to run ccrewrite.exe and it [should be installed on the build server](http://stackoverflow.com/questions/3569108/microsoft-code-contracts-and-ci-build-server?rq=1) 

## String

### Convert a string into base64 string

Convert it to byte with UTF8 Encoding and then convert it to base64 string
* [http://stackoverflow.com/questions/11743160/how-do-i-encode-and-decode-a-base64-string](http://stackoverflow.com/questions/11743160/how-do-i-encode-and-decode-a-base64-string)
* [http://stackoverflow.com/questions/1886686/c-sharp-byte-to-url-friendly-string](http://stackoverflow.com/questions/1886686/c-sharp-byte-to-url-friendly-string)

```csharp
public string FullNameBase64 
{ 
    get { return Convert.ToBase64String(Encoding.UTF8.GetBytes(FullName)); } 
}
```

However, you need to generate URL safe encoding. This can be done by HttpServerUtility

```csharp
public string FullNameBase64 
{ 
    get { return HttpServerUtility.UrlTokenEncode(Encoding.UTF8.GetBytes(FullName)); } 
}

```