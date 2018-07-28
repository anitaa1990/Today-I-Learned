# How to store custom objects in UserDefaults

We can store values in iOS using ```UserDefault```. It supports storing ```strings```, ```integers```, ```lists``` etc. But if we need to store custom objects, what should we do?

* Let's say we have a custom object called ```NotificationModel```. It is important to note that we need to extend the custom object with ```Codable``` to ensure that the custom object is encodable or decodable.
```
public class NotificationModel : NSObject, Codable {
    
    private var _title: String = ""
    var title: String {
        set { _title = title }
        get { return _title }
    }
    
    
    private var _body: String = ""
    var body: String {
        set { _body = body }
        get { return _body }
    }
    
    
    private var _timeStamp: String = ""
    var timeStamp: String {
        set { _timeStamp = timeStamp }
        get { return _timeStamp }
    }
    
    init(info: Dictionary<String, AnyObject>) {
        self._title = info["title"] as! String
        self._body = info["body"] as! String
        self._timeStamp = String(Date().toMillis())
    }
    
    required public init(coder aDecoder: NSCoder) {
        self._title = aDecoder.decodeObject(forKey: "title") as! String
        self._body = aDecoder.decodeObject(forKey: "body") as! String
        self._timeStamp = aDecoder.decodeObject(forKey: "timeStamp") as! String
    }
    
    public func encode(with aCoder: NSCoder) {
        aCoder.encode(_title, forKey: "title")
        aCoder.encode(_body, forKey: "body")
        aCoder.encode(_timeStamp, forKey: "timeStamp")
    }
}
```


* We can initialize the custom object class like this:
  ```
  let data: Dictionary<String, AnyObject> = ["title" : "Anitaa" as AnyObject, "body" : "Test1" as AnyObject, "timeStamp" : "1532683817" as AnyObject]
  let notification: NotificationModel = NotificationModel(info: data)
  ```
  
  
* Now let's store the notification object received from the app in ```UserDefaults```. We need to use ```JSONEncoder``` to encode the object first:
  ```
  let notification: NotificationModel = NotificationModel(info: data)
  
  if let encoded = try? JSONEncoder().encode(notification) {
      UserDefaults.standard.set(encoded, forKey: "notification")
  }
  ```
  
* Now let's get the list of notifications from ```UserDefault```. We use ```JSONDecoder``` to decode the object first:
  ```
  if let notifications = UserDefaults.standard.data(forKey: "notification"),
    let notifications = try? JSONDecoder().decode(Notification.self, from: notifications) {

    dump(notification)
  }
  ```
