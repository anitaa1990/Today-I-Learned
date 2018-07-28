# What is the use of android:largeHeap="true"?

We have the option to add ```largeHeap="true"``` in our ```AndroidManifest.xml``` under the  ```Application``` tab.

* <b>What do you get when this is defined to ```true```:</b></br>
  You get larger heap for your app, which means decreasing risk of ```OutOfMemoryError```.
  
* <b>What do some of the disadvantages:</b></br>
  * Larger heap makes garbage collections take longer. Because the garbage collector basically has to traverse your entire live set of objects. Usually, garbage collection pause time is about 5ms, and you may think few milliseconds are not a big deal. But every millisecond count. Android device has to update its screen in every 16 ms and longer GC time might push your frame processing time over the 16 millisecond barrier, which can cause a visible hitching.
  * Android system may kill processes in the LRU cache beginning with the process least recently used, but also giving some consideration toward which processes are most memory intensive. So if you use larger heap, your process would more likely to be killed when it's backgrounded, which means it may take longer time when users want to switch from other apps to yours.
  
<b>Conclusion</b></br>
Avoid using largeHeap option as much as possible
  
  
