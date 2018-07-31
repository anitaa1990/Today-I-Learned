# Notification Callbacks in Swift 4 Xcode 11 for iOS 10+ devices

Recently I had to implement notification in iOS using FCM. You can checkout this [link](https://www.appcoda.com/firebase-push-notifications/) for details on how to setup notification in iOS using FCM. But the purpose of this post is to specifically talk about the callbacks we need to define in code in order to receive the notifications. 

So let's look at some of the callbacks below.

In iOS 10 and above, you can set the ```UNUserNotificationCenter``` delegate to receive display notifications from Apple and FCM. The [UNUserNotificationCenter](https://developer.apple.com/documentation/usernotifications/unusernotificationcenter) has 2 callback methods:
  * Method called when app is in foreground:
  ```
  func userNotificationCenter(_ center: UNUserNotificationCenter,
                                  willPresent notification: UNNotification,
                                  withCompletionHandler completionHandler: @escaping (UNNotificationPresentationOptions) -> Void) {

          let userInfo = notification.request.content.userInfo
          guard let data =
              try? JSONSerialization.data(withJSONObject: userInfo, options: .prettyPrinted),
              let prettyPrinted = String(data: data, encoding: .utf8) else { return }

          print("Present:\n\(prettyPrinted)")

          completionHandler([.alert, .sound, .badge])
      }
  ```  
  <b>Note:</b> In the above callback, we need to specifically call this method: ```completionHandler([.alert, .sound, .badge])``` to display the notification to the user in the notification tray.

  
  * Method called when app is in the background or if the app has been killed:
  ```
      func userNotificationCenter(_ center: UNUserNotificationCenter,
                                didReceive response: UNNotificationResponse,
                                withCompletionHandler completionHandler: @escaping () -> Void) {
        let userInfo = response.notification.request.content.userInfo

        // Print full message.
        guard let data =
            try? JSONSerialization.data(withJSONObject: userInfo, options: .prettyPrinted),
            let prettyPrinted = String(data: data, encoding: .utf8) else { return }
        
        print("Center:\n\(prettyPrinted)")
        
        completionHandler()
    }
   ```
   <b>Note:</b> When the app is in the background or the app is killed, if a notification is received, it will automatically be dispalyed to the user in the notification tray. The above callback will only be called when the user clicks on the notification from the notification tray.
    
    
Lastly, I wanted to point out the ```payload``` that needs to be sent from FCM API in order to invoke the above callbacks in the app:
```
{
	"notification": {
		"body": "This week’s edition is now available.",
		"title": "Portugal vs. Denmark - background",
		"text": "5 to 1",
		"content_available": 1
	},
	"to": "{fcmToken}",
	"priority": "high",
	"mutable_content": true
}
```
    
