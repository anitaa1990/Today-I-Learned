# LocalBroadcastReceiver implementation in iOS

I was recently looking for a way to pass data from the background to the user interface. In android, we can use ```localBroadcastReceiver```. But in iOS how do we implement this, especially in Swift 4?

## Step 1: Initiating the message from the background
In order to send the message We will be using the helper class ```NotificationCenter```

```
/*  This is the data object we can pass to the UI  */
let dataToSend = ["data" : "Sending message from backend"]
NotificationCenter.default.post(name: Notification.Name(rawValue: "{key_to_identify_the_messae}"), 
                                object: nil, 
                                userInfo: dataToSend)
```


## Step 2: Receiving the message in the UI
To get the messages for that specific key we need to subscribe to the ```NotificationCenter```. </br>
  * Inside the ```viewDidAppear``` method in our ```ViewController```, we can register our subscriber for the specified key.
  * We also specify a selector to have a function to handle when the message is received.
  ```
  NotificationCenter.default.addObserver(self, selector: #selector(handleNotification(withNotification:)),
        name: NSNotification.Name(rawValue:"notification_key"), object: nil)
  ```        
  * We can get the message we passed from the background in the declared function:
  ```
  @objc func handleNotification(withNotification notification : NSNotification) {
    let data = notification.userInfo?["data"]
  }
  ```
