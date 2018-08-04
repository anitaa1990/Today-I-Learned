# Send result back to multiple activities

So let’s take a simple use case where we have 3 activities A,B,C. We want to pass data from Activity C back to Activity A. Sounds simple right? We can pass data from Activity C -> Activity B -> Activity A.

But here’s the catch. Activity B is to be finished before redirecting to Activity C.

Intended Flow:
```
A started B
B started C; B finishes itself
C sends result to A
```

So how can we send data back from Activity C to A?
```
public static void redirectToActivityB() {
    Intent intent = new Intent(context, ActivityB.class);
    startActivityForResult(intent, ACTIVITY_REQUEST_CODE);
}

public static void redirectToActivityC() {
    Intent intent = new Intent(context, ActivityC.class);
    intent.addFlags(Intent.FLAG_ACTIVITY_FORWARD_RESULT);
    startActivity(intent);
    finish();
}
```

We can see from the above example that all we need to call is ```intent.addFlags(Intent.FLAG_ACTIVITY_FORWARD_RESULT)```.

This will tell the intent that this activity will be finished and removed from the backstack, hence the parent should handle the result.
