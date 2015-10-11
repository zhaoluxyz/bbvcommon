# Introduction #

The AsyncWorker is used for execution of asynchronous operations. It is based on the BackgroundWorker of the .net library but simplifies usage from within code (BackgroundWorker is built to be used within the designer of views).

The AsyncWorker provides automatic switching back to the thread where the operation was started.
Please note that this works only if the thread from which the operation was started is associated with a thread synchronizer capable of thread switching. This is true for example in the user interface thread.

Therefore the AsyncWorker is best used to execute operations from the user interface without blocking it and with the possibility to update the user interface on completion of the operation.

Finally, the AsyncWorker improves the BackgroundWorker so that when an operation throws an exception then it is not swallowed anymore.

# Samples #

## Simple Async Operation ##

```
DoWorkEventHandler worker = delegate
{
    // take your time to do something because you will not block anyone...
};

AsyncWorker asyncWorker = new AsyncWorker(worker);

asyncWorker.RunWorkerAsync();
```


## Async Operation with Arguments ##

```
DoWorkEventHandler worker = delegate(object sender, DoWorkEventArgs e)
{
    // do something with the argument (unfortunately not type safe - please blame .net and not me)
    object argument = e.Argument;
};

AsyncWorker asyncWorker = new AsyncWorker(worker);

asyncWorker.RunWorkerAsync("hello world");
```


## Do Something on Completion ##

```
DoWorkEventHandler worker = delegate(object sender, DoWorkEventArgs e)
{
    e.Result = "the result";
};

RunWorkerCompletedEventHandler completed = delegate(object sender, RunWorkerCompletedEventArgs e)
{
    // do something with the result, e.g. updating the UI, no thread switching needed
    object result = e.Result;
};

AsyncWorker asyncWorker = new AsyncWorker(worker, completed);

asyncWorker.RunWorkerAsync();
```

## Failing Operation with no Completion Handler ##

If the operation throws an exception and there is no completion handler set then the global handler for unhandled exceptions is notified:

```
// first we need to register for unhandled exceptions
AppDomain.CurrentDomain.UnhandledException += (s, e) => 
    {
        // handle the unhandled exception
    };

DoWorkEventHandler worker = delegate
{
    throw new InvalidOperationException("test");
};

AsyncWorker asyncWorker = new AsyncWorker(worker);

asyncWorker.RunWorkerAsync();
```


## Failing Operation with Completion Handler ##

If the operation throws an exception and there is a completion handler set then the completion handler is responsible to handle the exception. The global handler for unhandled exceptions will not be notified:

```
DoWorkEventHandler worker = delegate
{
    throw new InvalidOperationException();
};

RunWorkerCompletedEventHandler completed = delegate(object sender, RunWorkerCompletedEventArgs e)
{
    // handle the exception (null if no exception)
    Exception exception = e.Error;
};

AsyncWorker asyncWorker= new AsyncWorker(worker, completed);

asyncWorker.RunWorkerAsync();
```


## Cancel an Operation ##

```
DoWorkEventHandler worker = delegate(object sender, DoWorkEventArgs e)
{
    AsyncWorker genericWorker = (AsyncWorker)sender;
    genericWorker.WorkerSupportsCancellation = true;

    while (!genericWorker.CancellationPending)
    {
        // do our job here
    }

    e.Cancel = true;
};

RunWorkerCompletedEventHandler completed = delegate(object sender, RunWorkerCompletedEventArgs e)
{
    // check whether operation completed or not
    bool cancelled = e.Cancelled;
};

AsyncWorker asyncWorker = new AsyncWorker(worker, completed);

asyncWorker.RunWorkerAsync();
asyncWorker.CancelAsync();
```

## Show Progress ##

```
DoWorkEventHandler worker = delegate(object sender, DoWorkEventArgs e)
{
     AsyncWorker genericWorker = (AsyncWorker)sender;
     genericWorker.WorkerReportsProgress = true;

     for (int i = 0; i <= 100; i += 10)
     {
         genericWorker.ReportProgress(i, "current step = " + i);
     }
};

ProgressChangedEventHandler progress = (sender, e) =>
{
    // update UI with this data:
    int percentage = e.ProgressPercentage;
    object step = e.UserState;
};

AsyncWorker asyncWorker = new AsyncWorker(worker, progress, null);

asyncWorker.RunWorkerAsync();
```