# Introducing NIO.2 (JSR 203) Part 4: Changing File System Attributes and Permissions

 This is the 4th installment of my entries covering NIO.2. In this entry I will discuss more about what we can do with attributes and permissions.  The NIO.2 lets us write the permissions and attributes of a file in addition to reading them. For example we can change the rwx permissions using the Attributes utility class and  PosixFilePermission. You can execute the following sample code with almost no changes, the only thing you need to change is the path to our sample file and then you are ready to see how it works.
{{< highlight java >}}
import java.io.IOException;
import java.nio.file.\*;
import java.nio.file.attribute.\*;
import java.util.\*;

public class Perm2 {

    public static void main(String args\[\]) throws IOException {
        FileSystem fs = FileSystems.getDefault();
        Path p = fs.getPath("/home/masoud/dl");

        // Reading attributes using attributes views.
        // here we read both basic and posix attributes of a file
        Map att = (Map) p.readAttributes("posix:\*, basic:\*", LinkOption.NOFOLLOW_LINKS);
        // printing the attributes to output
        Set en = att.keySet();
        for (Iterator it = en.iterator(); it.hasNext();) {
            String string = it.next();
            System.out.println(string + ": " + att.get(string));
        }
        System.out.println("-------------Attributes printing finished----------");

        // definind a new permission set for our file
        Set
 st2 = PosixFilePermissions.fromString("rwxrwxrwx");
        // Setting the attribute using Attributes utility class
        Attributes.setPosixFilePermissions(p, st2);

        // looking up and creating a principal for the given user. If use does
        // not exists it will throws a UserPrincipalNotFoundException
        UserPrincipal up = fs.getUserPrincipalLookupService().lookupPrincipalByName("masoud");

        // Setting the owner to the owner we just looked up.
        // We should have enough permisison to change the owner otherwise it will
        // throw a FileSystemException: /home/masoud/a: Operation not permitted sort of thing
        Attributes.setOwner(p, up);

        //another way to read and write the owner value for a file is using FileOwnerAttributeView
        FileOwnerAttributeView fo = p.getFileAttributeView(FileOwnerAttributeView.class, LinkOption.NOFOLLOW_LINKS);
        fo.setOwner(up);

        //Now that we have changed the permissions lets see the permissions again:
        att = (Map) p.readAttributes("posix:permissions", LinkOption.NOFOLLOW_LINKS);
        System.out.println("New Permissions:" + ": " + att.get("permissions"));
    }
}
{{< /highlight >}}
Let's see what we have done starting from top: at the beginning we have just initialized the FileSystem object and got a file reference to _/home/masoud/dl_. Then we read all of this files basic and posix attributes using the attributes view notation. The following sample code shows this step
{{< highlight java >}}
 FileSystem fs = FileSystems.getDefault();
        Path p = fs.getPath("/home/masoud/dl");

        // Reading attributes using attributes views.
        // here we read both basic and posix attributes of a file
        Map att = (Map) p.readAttributes("posix:\*, basic:\*", LinkOption.NOFOLLOW_LINKS);
        // printing the attributes to output
        Set en = att.keySet();
        for (Iterator it = en.iterator(); it.hasNext();) {
            String string = it.next();
            System.out.println(string + ": " + att.get(string));
        }
System.out.println("-------------Attributes printing finished----------");
{{< /highlight >}}
Next step we have use the PosixPermissions class to create a permission set using the OS permissions presentation and then applied these permissions on our file
{{< highlight java >}}
 // definind a new permission set for our file
        Set
 st2 = PosixFilePermissions.fromString("rwxrwxrwx");
        // Setting the attribute using Attributes utility class
        Attributes.setPosixFilePermissions(p, st2);
{{< /highlight >}}
In the next step we created a user principal for a user named _masoud_ and changed the ownership of our file to this user. We have done this using both available methods.
{{< highlight java >}}
// looking up and creating a principal for the given user. If use does
        // not exists it will throws a UserPrincipalNotFoundException
        UserPrincipal up = fs.getUserPrincipalLookupService().lookupPrincipalByName("masoud");

        // Setting the owner to the owner we just looked up.
        // We should have enough permisison to change the owner otherwise it will
        // throw a FileSystemException: /home/masoud/a: Operation not permitted sort of thing
        Attributes.setOwner(p, up);

        //another way to read and write the owner value for a file is using FileOwnerAttributeView
        FileOwnerAttributeView fo = p.getFileAttributeView(FileOwnerAttributeView.class, LinkOption.NOFOLLOW_LINKS);
        fo.setOwner(up);
{{< /highlight >}}
Finally we read the file permissions to see what has changed after we applied this new permissions on our file. using the following snippet.
{{< highlight java >}}
 //Now that we have changed the permissions lets see the permissions again:
        att = (Map) p.readAttributes("posix:permissions", LinkOption.NOFOLLOW_LINKS);
        System.out.println("New Permissions:" + ": " + att.get("permissions"));
{{< /highlight >}}
The output of running the above sample code is shown below:
{{< highlight bash >}}
lastModifiedTime: 2010-07-06T23:15:32Z
fileKey: (dev=805,ino=1922268)
isDirectory: false
lastAccessTime: 2010-07-11T14:15:46Z
isOther: false
isSymbolicLink: false
owner: masoud
permissions: \[OTHERS_READ, OWNER_WRITE, GROUP_WRITE, OWNER_READ, GROUP_READ, OTHERS_EXECUTE, OWNER_EXECUTE, GROUP_EXECUTE, OTHERS_WRITE\]
isRegularFile: true
creationTime: null
group: dip
size: 219
-------------Attributes printing finished----------
Permissions:: \[OTHERS_READ, OWNER_WRITE, GROUP_WRITE, OWNER_READ, GROUP_READ, OTHERS_EXECUTE, OWNER_EXECUTE, GROUP_EXECUTE, OTHERS_WRITE\]
{{< /highlight >}}
You may ask what is the attribute view notation and what is meaning of that _posix:permissions_ or _posix:\*_. The first one means that we want to retrieve the permissions property of the file while the second one means that we want to retrieve all attributes of the file which can be seen using the posix view. Now you are asking where you can found list of this attributes view notations, then you question is very right. The following links shows what attributes are available to read using each one of those views. Each attribute is one of the properties included in the interface.

*   BasicFileAttributes: [http://download.oracle.com/docs/cd/E17409_01/javase/7/docs/api/java/nio/file/attribute/BasicFileAttributes.html](http://download.oracle.com/docs/cd/E17409_01/javase/7/docs/api/java/nio/file/attribute/BasicFileAttributes.html)
*   PosixFileAttributes: [http://download.oracle.com/docs/cd/E17409_01/javase/7/docs/api/java/nio/file/attribute/PosixFileAttributes.html](http://download.oracle.com/docs/cd/E17409_01/javase/7/docs/api/java/nio/file/attribute/PosixFileAttributes.html)

Sure we have other views, you can find them in the JavaDocs linked above. Next entry will be the last entry covering File System functionalities of the NIO.2 including the watch and change notification service and the ACL list. After that we will look at new features included for IO developers. You can find other entries of this series under the [NIO.2](http://kalali.me/tag/nio-2/) tag of my weblog.

