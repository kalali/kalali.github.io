# Introducing NIO.2 (JSR 203) Part 1: What are new features?

 I will write a series of blog to discuss what are the new features introduced in NIO.2 (JSR 203). The [NIO.2 implementation](http://openjdk.java.net/projects/nio/) is a part of [OpenJDK](http://openjdk.java.net/) project and we can alreay use it by downloading a copy of OpenJDK [binary](http://download.java.net/jdk7/binaries/).  In the first entry I will just go through what are these new I/O features of Java 7, which help developer iron out better applications easier. Talking about File systems support and features which let us  deal with file system we can name the following features:

1.  **Platform friendly-ness of NIO.2**: We can deal with all file systems in a unified model.
2.  **File tree walk:** We can walk on a file tree and examine each node using the built-in APIs, NIO.2 to let us know whether the current member is a file, a directory or a symbolic link. We can then perform any operation we want on that node.
3.  **File Operations (Copy, Delete, Move):** Basic operations are supported with plethora of options. The move operation can be performed in atomic way.
4.  **Symbolic links support:** count soft and hard links as well as managing them.
5.  **Support for file attributes in NIO.2:** Managing file systems attributes is fully supported for different file systems including DOS, POSIX...
6.  **File system change notification:** We can setup a watch service and receive notification when a change happens on the path we are watching.
7.  **SPI for providing new file systems support,** for example to support zip, zfs, btrfs, we can implement the provider interfaces and use the unified API to access that specific file system using our implementation

Working with sockets and reding/ writing files we can name the following features:

1.  **Multicasting is now supported** in the DatagramChannel meaning that we can send and receive IP datagrams from a complete group. Both IPv4 and IPv6 are supported.
2.  **Asynchronous I/O for sockets and Files**: Now we can have Asynchronous read and write both on channles and files. It basically means we can have event driven I/O over both networks and files.

Other improvement, features:

1.  Support for very large multi gigabyte buffers
2.  Some MXBeans are included to monitor IO specific buffers.
3.  Interoperability between Java 7 IO and previous versions using the Path API.

I will post sample codes for each of these features in upcomming entries. So stay tuned if you want to learn more about NIO.2

