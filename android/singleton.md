# How to implement a Singleton class in Android?

I recently read an article on how to implement the perfect Singleton from [here](https://medium.com/exploring-code/how-to-make-the-perfect-singleton-de6b951dfdb0). I wanted to summarize the key points from the article here:
* <b>Step 1</b>: Make the constructor of the class as ```private```. This ensures that other classes are not able to create a new instance of the Singleton class.
* <b>Step 2</b>: Create one public static method (commonly name as for getInstance()) to provide a single entry point to create a new instance of the class. Check if a new instance of the class is available. If not, create a new instance of the class. This is known as <b>Lazy Initialization</b>  
* <b>Step 3</b>: Ensure that the Singleton class is <b>Reflection Proof</b>. A new instance of the Singleton can be created using Java Reflection API. In order to prevent that, we need to add a check to the constructor. If the constructor is already initialized, throw a run-time exception.
* <b>Step 4</b>: Ensure that the Singleton class is <b>Thread Safe</b>. When two threads try to initialize the Singleton class at almost the same time, then two different instances of the Singleton class is created. In order to prevent that:
   * Make ```getInstance()``` method ```synchorized```. This can ensure that the second thread will have to wait until the ```getInstance()``` method is completed for the first thread. But this can mean there is unnecessary locking meaning performance is affected.
   * Use <b>Double Check locking method</b>: This means make the Singleton class in the <b>synchronized</b> block only if the instance is null. So, the synchronized block will be executed only when the ```instance``` is null and prevent unnecessary synchronization once the instance variable is initialized.
   * Use <b>volatile</b> keyword: Without volatile modifier, it’s possible for another thread in Java to see the half initialized state of ```instance``` variable.
* <b>Step 5</b>: Ensure that the Singleton class is safe from<b>Serialization</b>. When we need to implement ```Serializable``` interface in a Singleton class, we need to ensure that the ```instance``` is maintained before and after serialization. By default when deserialized, a new instance of the Singleton class will be created. To prevent creation of another instance, implementat the ```readResolve()``` method. This method replaces the object read from the stream and ensures that nobody can create another instance by serializing and deserializing the singleton.
  
  
Final implementation of a Singleton Class:  
  
```
public class SingletonClass implements Serializable {
    /* Step 4: Use volatile keyword to ensure that the Singleton is thread safe.  */
    private static volatile SingletonClass sSoleInstance;

    /*  Step 1: Make the constructor of the class as 'private'. 
        This ensures that other classes are not able to create 
        a new instance of the Singleton class.  */
    private SingletonClass(){

        /*  Step 3: Ensure that the Singleton class is Reflection Proof. 
            A new instance of the Singleton can be created using Java Reflection API. 
            In order to prevent that, we need to add a check to the constructor. 
            If the constructor is already initialized, throw a run-time exception.  */
        if (sSoleInstance != null){
            throw new RuntimeException("Use getInstance() method to get the single instance of this class.");
        }
    }

    /*   
     * Step 2: Create one public static method to provide a single entry point to create a new instance of the class. 
     * Check if a new instance of the class is available. If not, create a new instance of the class.
     */
    public static SingletonClass getInstance() {
        if (sSoleInstance == null) {
        
            /* Step 4: Use Double Check locking method. This means make the Singleton class in the synchronized block 
            only if the instance is null. So, the synchronized block will be executed only when the 'instance' is null 
            and prevent unnecessary synchronization once the instance variable is initialized.  */
            synchronized (SingletonClass.class) {
                if (sSoleInstance == null) sSoleInstance = new SingletonClass();
            }
        }

        return sSoleInstance;
    }


    /* Step 5: Ensure that the Singleton class is safe from Serialization. When we need to implement 
     * Serializable interface in a Singleton class, we need to ensure that the instance is maintained 
     * before and after serialization. By default when deserialized, a new instance of the Singleton class will be created. 
     * To prevent creation of another instance, implementat this method. 
     * This method replaces the object read from the stream and ensures that nobody can create another 
     * instance by serializing and deserializing the singleton.  */
    protected SingletonClass readResolve() {
        return getInstance();
    }
}
```
