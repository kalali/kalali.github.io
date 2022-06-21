# Introducing NIO.2 (JSR 203) Part 2: The Basics

 In this part we will discuss the basic classes that we will work with them to have file system operations like copying a file, dealing with symbolic links, deleting a file, and so on. I will write a separate entry to introduce classes which are new to Java 7 for dealing with streams and file contents, watching service and directory tree walking. If you want to know what are new features in Java SE 7 for dealing with IO take a look at   [Introducing NIO.2 (JSR 203) Part 1: What are new features?](/blog/kalali/archive/2010/05/19/introducing-nio2-jsr-203-part-1-what-are-new-features) Before NIO.2, dealing with file system was mainly done using the File class and no other base class was available. In NIO.2 it there are some new classes at our disposal to take advantage of their existence to do our job. [FileSystems](http://openjdk.java.net/projects/nio/javadoc/java/nio/file/FileSystems.html): Everything starts with this factory class. We use this class to get an instance of the FileSystem we want to work on. The nio.2 provides a SPI to developed support for new file systems. For example an in-memory file system, a ZIP file system and so on. Following two methods are most important methods in FileSystems class.

1.  The getDefault() returns the default file system available to the JVM. Usually the operating system default files system.
2.  The getFileSystem(URI uri) returns a file system from the set of available file system providers that match the given uir schema.

[Path](http://openjdk.java.net/projects/nio/javadoc/java/nio/file/Path.html): This is the abstract class which provides us with all File system functionalities we may need to perform over a file, a directory or a link. [FileStore](http://openjdk.java.net/projects/nio/javadoc/java/nio/file/FileStore.html): This class represents the underplaying storage. For example /dev/sda2 in \*NIX machines and I think c: in windows machines. We can access the storage attributes using  [FileStoreSpaceAttributes](http://openjdk.java.net/projects/nio/javadoc/java/nio/file/attribute/FileStoreSpaceAttributes.html ) object. Available space, empty space and so on. Following two sample codes shows how to copy a file and then how to copy it.
{{< highlight java >}}
public class Main {

    public static void main(String\[\] args) {

        try {
            Path sampleFile = FileSystems.getDefault().getPath("/home/masoud/sample.txt");
            sampleFile.deleteIfExists();
            sampleFile.createFile(); // create an empty file
            sampleFile.copyTo(FileSystems.getDefault().getPath("/home/masoud/sample2.txt"), StandardCopyOption.COPY_ATTRIBUTES.REPLACE_EXISTING);
            // Creating a link
            Path dir = FileSystems.getDefault().getPath("/home/masoud/dir");
            dir.deleteIfExists();
            dir.createSymbolicLink(sampleFile);
        } catch (IOException ex) {
            Logger.getLogger(Main.class.getName()).log(Level.SEVERE, null, ex);
        }
    }
{{< /highlight >}}
And the next sample shows how we can use the FileStore class. In this sample we get the underlying store for a file and examined its attributes. We can an iterator over all available storages using FileSystem.getFileStores() method and examine all of them in a loop.
{{< highlight java >}}
 public class Main {

    public static void main(String\[\] args) throws IOException {
        long aMegabyte = 1024 \* 1024;
        FileSystem fs = FileSystems.getDefault();
        Path sampleFile = fs.getPath("/home/masoud/sample.txt");
        FileStore fstore = sampleFile.getFileStore();
        FileStoreSpaceAttributes attrs = Attributes.readFileStoreSpaceAttributes(fstore);
        long total = attrs.totalSpace() / aMegabyte;
        long used = (attrs.totalSpace() - attrs.unallocatedSpace()) / aMegabyte;
        long avail = attrs.usableSpace() / aMegabyte;
        System.out.format("%-20s %12s %12s %12s%n", "Device", "Total Space(MiB)", "Used(MiB)", "Availabile(MiB)");
        System.out.format("%-20s %12d %12d %12d%n", fstore, total, used, avail);

            }
{{< /highlight >}}
In next entry I will discuss how we can manage file attributes along with discussing the security features of the nio.2 file system.

