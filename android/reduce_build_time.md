# Reduce build time of an Android app

I recently read this awesome [article](https://medium.com/exploring-code/how-to-decrease-your-gradle-build-time-by-65-310b572b0c43) on how to reduce the build time of an android app. I just wanted to highlight the basic concepts here.

 * Firstly, there are a few commands we can add to the gradle.properties file:
    * ```org.gradle.configureondemand=true``` - This command will tell gradle to only build the projects that it really needs to build.
    * ```org.gradle.daemon=true``` - Daemon keeps the instance of the gradle up and running in the background even after your build finishes. This will remove the time required to initialize the gradle and decrease your build timing significantly.
    * ```org.gradle.parallel=true``` - Allow gradle to build your project in parallel. If you have multiple modules in you project, then by enabling this, gradle can run build operations for independent modules parallelly.
    * ```org.gradle.jvmargs=-Xmx3072m -XX:MaxPermSize=512m -XX:+HeapDumpOnOutOfMemoryError -Dfile.encoding=UTF-8``` - Since android studio 2.0, gradle uses dex in the process to decrease the build timings for the project. Generally, while building the applications, multiple dx processes runs on different VM instances. But starting from the Android Studio 2.0, all these dx processes runs in the single VM and that VM is also shared with the gradle. This decreases the build time significantly as all the dex process runs on the same VM instances. But this requires larger memory to accommodate all the dex processes and gradle. That means you need to increase the heap size required by the gradle daemon. By default, the heap size for the daemon is about 1GB.
    
   Secondly, we should ensure that dynamic dependency is not used. i.e. do not use</br> 
   ```implementation 'com.android.support:appcompat-v7:27.0.+'```.</br> 
   This command means gradle will go online and check for the latest version every time it builds the app.</br>Instead use fixed versions i.e. ```'com.android.support:appcompat-v7:27.0.2'``` 
    
