The generic event arguments allows you to define an event argument with a single value very quickly.

Using the generic event arguments you can define an event like this:

```
public event EventHandler<EventArgs<string>> MyEvent;
```

Then you can throw the event:

```
public void OnMyEvent(string value)
{
    if (this.MyEvent != null)
    {
        this.MyEvent(this, new EventArgs<string>(value));
    }
}
```

And register the event:

```
public class MyClass(ClassWithMyEvent objectWithMyEvent)
{
    objectWithMyEvent.MyEvent += this.HandleMyEvent;
}

private void HandleMyEvent(object sender, EventArgs<string> e)
{
    string s = e.Value;
}
```