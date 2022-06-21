# Introducing NIO.2 (JSR 203) Part 5: Watch Service and Change Notification

 For long time Java developers used in-house developed solutions to monitor the file system for changes. Some developed general purpose libraries to ease the task of others who deal with the same requirement.  Commercial and  free/ open source libraries like [http://jnotify.sourceforge.net/](http://jnotify.sourceforge.net/), [http://jpathwatch.wordpress.com/](http://jpathwatch.wordpress.com/) and  [http://www.teamdev.com/jxfilewatcher](http://www.teamdev.com/jxfilewatcher) among others. Java 7 comes with NIO.2 or JSR 203 which provides native file system watch service. The  watch service provided in Java 7  uses the underlying file system functionalities to watch the file system for changes, so if we are running on Windows, MacOS or Linux... we are sure that the watch service is not imposing polling overhead on our application because the underlying OS and file system provides the required functionalities to allow Java to register for receiving notification on file system changes. If the underlying file system does not provide the watch-ability, which I doubt it for any mainstream file system,  Java will fall back to some rudimentary polling mechanism to keep the code working but the performance will degrade. From the mentioned libraries the [jpathwatch](http://jpathwatch.wordpress.com/) API is the same as the Java 7 APIs to make it easier to migrate an IO based application from older version of Java to Java 7 when its time arrives.

The whole story starts with WatchService which we register our interest in watching a path using it. The WatchService itself is an interface with several implementatins for different file system and operating systems.

We have four class to work with when we are developing a system with file system watch capability.

1.  A **Watchable**: A watchable is an object of a class implementing the Watchable interface. In our case this is the Path class which is the one of the central classes in the NIO.2
2.  A **set of event types**: We use it to specify which types of events we are interested in. For example whether we want to receive creation, deletion, ... events. In our case we will use StandardWatchEventKind which implements the WatchEvent.Kind<T>.
3.  An **event modifier**: An event modifier that qualifies how a Watchable is registered with a WatchService. In our case we will deal with nothing specific up to now as there is no implementation of this interface included in the JDK distribution.
4.  The **Wacher**: This is the watcher who watch some watchable. In our case the watcher watches the File System for changes. The abstract class is java.nio.file.WatchService but we will be using the FileSystem object to create a watcher for the File System.

Now that we know the basics, let's see how a complete sample will look like and then we will break down the sample into different parts and discuss them one by one.
{{< highlight java >}}
import java.io.IOException;
import java.nio.file.FileSystem;
import java.nio.file.FileSystems;
import java.nio.file.Path;
import java.nio.file.StandardWatchEventKind;
import java.nio.file.WatchEvent;
import java.nio.file.WatchKey;
import java.nio.file.WatchService;
import java.util.List;
import java.util.logging.Level;
import java.util.logging.Logger;

public class WatchSer {
    public static void main(String args\[\]) throws InterruptedException {
        try {
            FileSystem fs = FileSystems.getDefault();
            WatchService ws = null;
            try {
                ws = fs.newWatchService();
            } catch (IOException ex) {
                Logger.getLogger(WatchSer.class.getName()).log(Level.SEVERE, null, ex);
            }
            Path path = fs.getPath("/home/masoud/Pictures");
            path.register(ws, StandardWatchEventKind.ENTRY_CREATE, StandardWatchEventKind.ENTRY_MODIFY, StandardWatchEventKind.OVERFLOW, StandardWatchEventKind.ENTRY_DELETE);

            WatchKey k = ws.take();

            List> events = k.pollEvents();
            for (WatchEvent object : events) {
                if (object.kind() == StandardWatchEventKind.ENTRY_MODIFY) {
                    System.out.println("Modify: " + object.context().toString());
                }
                if (object.kind() == StandardWatchEventKind.ENTRY_DELETE) {
                    System.out.println("Delete: " + object.context().toString());
                }
                if (object.kind() == StandardWatchEventKind.ENTRY_CREATE) {
                    System.out.println("Created: " + object.context().toString());
                }
            }
        } catch (IOException ex) {
            Logger.getLogger(WatchSer.class.getName()).log(Level.SEVERE, null, ex);
        }
    }
}
{{< /highlight >}}
At the beginning of the code we create a FileSystem object and then create a WatchService for the file system underneath the FileSystem object we created previously.
{{< highlight java >}}
            FileSystem fs = FileSystems.getDefault();
            WatchService ws = null;
            try {
                ws = fs.newWatchService();
            } catch (IOException ex) {
                Logger.getLogger(WatchSer.class.getName()).log(Level.SEVERE, null, ex);
            }
{{< /highlight >}}
At the next step we create a Path object which basically is our watchable, what we want to watch, and then register it with the WatchService object we created in the previous step.

  Path path = fs.getPath("/home/masoud/Pictures");
   path.register(ws, StandardWatchEventKind.ENTRY_CREATE, StandardWatchEventKind.ENTRY_MODIFY, StandardWatchEventKind.OVERFLOW, StandardWatchEventKind.ENTRY_DELETE);

Next we get a key which represents the registration of a watchable with a watch service. This WatchKey object represents our access door to events propagated by the WatchService object. After getting the key, we can poll for the events arrived at the key. An File system event may get represented by multiple WatchService events. For example a rename is represented by deletion and creation events.
{{< highlight java >}}
 WatchKey key = ws.take();
 List> events = key.pollEvents();

Now that we have the list of events we can iterate over the events list and see whether we are intrested in the event or not.
{{< highlight java >}}
for (WatchEvent object : events) {
                if (object.kind() == StandardWatchEventKind.ENTRY_MODIFY) {
                    System.out.println("Modify: " + path.toRealPath(true)+"/"+ object.context().toString());

                }
                if (object.kind() == StandardWatchEventKind.ENTRY_DELETE) {
                    System.out.println("Delete: " +  path.toRealPath(true)+"/"+ object.context().toString());
                }
                if (object.kind() == StandardWatchEventKind.ENTRY_CREATE) {
                    System.out.println("Created: " +  path.toRealPath(true)+"/"+ object.context().toString());
                }
            }
{{< /highlight >}}
In our sample code we are running the watch service in the application thread while we can use multiple threads to handle the events. One reason that the WatchService is not implemented using the Listener is the urge for using multiple threads to deal with the events in cases where there are thousands of events or event processing take longer than application threshold. You can grab the latest version of Java 7 aka Dolphin from [here](http://dlc.sun.com.edgesuite.net/jdk7/binaries/index.html "Here") and from the same page you can grab the latest JavaDoc which is generated from the same source code that is used to generate the binary bits. Other entries of this series are under the [NIO.2](http://kalali.me/tag/nio-2/) tag of my weblog.

