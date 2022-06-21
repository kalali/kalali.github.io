# Introducing NIO.2 (JSR 203) Part 3: File System Attributes and Permissions support in NIO.2

 In two previous entries I covered [Introducing NIO.2 (JSR 203) Part 1: What are new features?](http://weblogs.java.net/blog/kalali/archive/2010/05/19/introducing-nio2-jsr-203-part-1-what-are-new-features) and [Introducing NIO.2 (JSR 203) Part 2: The Basics](http://www.java.net/blog/kalali/archive/2010/06/01/introducing-nio2-jsr-203-part-1-basics) In this entry I will discuss Attributes introduced in NIO.2. Using attributes we can read platform specific attributes of an element in the file system. For example to hide a file system in DOS file system or to check the last access date of a file in a UNIX machine. Using NIO.2 we can check which attributes are supported in the platform we are running on and then we can decide how to deal with the available attributes. Following sample code shows how we can detect the available attributes and then how to manipulate them.
 
{{< highlight java >}}
  FileSystem fs = FileSystems.getDefault();
  Path p = fs.getPath("/home/masoud/netbeans-6.9-ml-linux.sh");
 //checking available attributes:
  Set<String> supportedViews = fs.supportedFileAttributeViews();
 //We always have at least one member in the set, the basic view.
  BasicFileAttributes ba = p.getFileAttributeView(BasicFileAttributeView.class, LinkOption.NOFOLLOW_LINKS).readAttributes();
 //Printing some basic attributes
   System.out.println(p.toString() + " last access:  " + ba.lastAccessTime());
   System.out.println(p.toString() + " last modified " + ba.lastModifiedTime());
        // As I know I am in NIX machine I access the unix attributes.
        // If I didnt I should have iterate over the set to determine which
        // attributes are supported
        if (supportedViews.contains("unix")) {
            PosixFileAttributes pat = Attributes.readPosixFileAttributes(p, LinkOption.NOFOLLOW_LINKS);
            System.out.println(pat.group().getName());
            System.out.println(pat.owner().getName());
        }
{{< /highlight >}}
I placed plethora of comments on the code so reading and understanding it get easier. In the next snippet we will see how we can read permissions of file system element. The first step in checking permissions is using the checkAccess method as shown below. the method throw an exception if the permission is not present or it will execute with no exception if the permission is present.
{{< highlight java >}}
 FileSystem fs = FileSystems.getDefault();
 Path p = fs.getPath("/home/masoud/netbeans-6.9-ml-linux.sh");
    try {
            // A method to check the access permissin
            p.checkAccess(AccessMode.EXECUTE);
        } catch (IOException ex) {
            Logger.getLogger(perm.class.getName()).log(Level.SEVERE, null, ex);
        }

        // Extracting all permissions of a file and iterating over them.
        //I know that I am dealing with NIX fs so I go directly with that attributes
        // otherwise we should check which attributes are supported and then we can
        // use them.

        PosixFileAttributes patts = Attributes.readPosixFileAttributes(p, LinkOption.NOFOLLOW_LINKS);
        Set<PosixFilePermission> st = patts.permissions();
        for (Iterator<PosixFilePermission> it = st.iterator(); it.hasNext();) {
            System.out.println(it.next().toString());
        }

        // Using PosixFilePermissions to convert permissions to different representations
        System.out.println(PosixFilePermissions.toString(st));
{{< /highlight >}}
As you can see in the code we can use the helper class to convert the permission set to a simpl OS represeted permission of the element. for example the set can be translated to *rwx---*

if the file has owner read, write and execute permissions attached to it. The helper calss can convert the os represenation of the permission to the permissions set for later use in other nio classess or methods. In the next entry I will conver more on permissions and security by tackling the Access Control List (ACL) support in the nio.2

