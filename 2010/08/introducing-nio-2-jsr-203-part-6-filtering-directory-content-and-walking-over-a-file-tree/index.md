# Introducing NIO.2 (JSR 203) Part 6: Filtering directory content and walking over a file tree

 In this part we will look at how the directory tree walker and the directory stream reader works. These two features are another couple of long requested features which was not included in the core java before Java 7. First, lets see what directory stream reader is, this API allows us to filter content of a directory on the file system and extract the file names that matches our filter criteria. The feature works for very large folders with thousands of files. For filtration we can use [PathMatcher](http://download.java.net/jdk7/docs/api/java/nio/file/PathMatcher.html) expression matching the file name or we can filter the directory content based on the different file attributes. for example based on the file permissions or the file size. Following sample code shows how to use the DirectoryStream along with filtering. For using the PathMatcher expression we can just use another overload of the newDirectoryStream method which accepts the PathMatcher expression instead of the filter.
{{< highlight java >}}
public class DirectoryStream2 {

    public static void main(String args\[\]) throws IOException {
	//Getting default file system and getting a path
        FileSystem fs = FileSystems.getDefault();
        Path p = fs.getPath("/usr/bin");

	//creating a directory streamer filter
        DirectoryStream.Filter
 filter = new DirectoryStream.Filter
() {

            public boolean accept(Path file) throws IOException {
                long size = Attributes.readBasicFileAttributes(file).size();
                String perm = PosixFilePermissions.toString(Attributes.readPosixFileAttributes(file).permissions());
                if (size > 8192L && perm.equalsIgnoreCase("rwxr-xr-x")) {
                    return true;
                }
                return false;
            }
        };
	// creating a directory streamer with the newly developed filter
        DirectoryStream
 ds = p.newDirectoryStream(filter);
        Iterator
 it = ds.iterator();
        while (it.hasNext()) {

            Path pp = it.next();

            System.out.println(pp.getName());
        }
    }
} 
{{< /highlight >}}
The above code is self explaining and I will not explain it any further than the in-line comments. Next subject of this entry is directory tree walking or basically file visitor API. This API allows us to walk over a file system tree and execute any operation we need over the files we found. The good news is that we can scan down to any depth we require using the provided API. With the directory tree walking API, the Java core allows us to register a vaster class with the directory tree walking API and then for each entry the API come across, either a file or a folder, it calls our visitor methods. So the first thing we need is a visitor to register it with the tree walker. Following snippet shows a simple visitor which only prints the file type using the Files.probeContentType() method.
{{< highlight java >}}
class visit extends SimpleFileVisitor
 {

    @Override
    public FileVisitResult visitFile(Path file, BasicFileAttributes attrs) {
        try {
            System.out.println(Files.probeContentType(file));

        } catch (IOException ex) {
            Logger.getLogger(visit.class.getName()).log(Level.SEVERE, null, ex);
        }
        return super.visitFile(file, attrs);
    }

    @Override
    public FileVisitResult postVisitDirectory(Path dir, IOException exc) {
        return super.postVisitDirectory(dir, exc);
    }

    @Override
    public FileVisitResult preVisitDirectory(Path dir) {
        return super.preVisitDirectory(dir);
    }

    @Override
    public FileVisitResult preVisitDirectoryFailed(Path dir, IOException exc) {
        return super.preVisitDirectoryFailed(dir, exc);
    }

    @Override
    public FileVisitResult visitFileFailed(Path file, IOException exc) {
        return super.visitFileFailed(file, exc);
    }
} 
{{< /highlight >}}
As you can see we extended the SimpleFileVisitor and we have visitor methods for all possible cases. Now that we have the visitor class, the rest of the code is straight forward. following sample shows how to walk over _/home/__masoud_  directory down to two levels.
{{< highlight java >}}

public class FileVisitor {

    public static void main(String args\[\]) {

        FileSystem fs = FileSystems.getDefault();
        Path p = fs.getPath("/home/masoud");
        visit v = new visit();
        Files.walkFileTree(p, EnumSet.allOf(FileVisitOption.class), 2, v);

    }
} 
{{< /highlight >}}
You can grab the latest version of Java 7 aka Dolphin from [here](http://dlc.sun.com.edgesuite.net/jdk7/binaries/index.html "Here") and from the same page you can grab the latest JavaDoc which is generated from the same source code that is used to generate the binary bits. Other entries of this series are under the [NIO.2](http://kalali.me/tag/nio-2/) tag of my weblog.

