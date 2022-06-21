# Monitoring ZFS pools performance using zpool iostat

 Performance, performance, performance; this is what we hear in all software development and management sessions. ZFS provides few utility commands to monitor one or more pools' performance. You should remember that we used fsstat command to monitor the UFS performance metrics. We can use iostat subcommand of the zpool command to monitor the performance metrics of ZFS pools. The iostat subcommand provides some options and arguments which we can see in its syntax shown below:
{{< highlight bash >}}
iostat \[-v\] \[pool\] ... \[interval \[count\]\]
{{< /highlight >}}
The -v option will show detailed performance metrics about the pools it monitor including the underlying devices.  We can pass as many pools as we want to monitor or we can omit passing a pool name so the command shows performance metrics of all commands.  The interval and count specifies how many times we want the sampling to happen what is the interval between each subsequent sampling. For example we can use the following command to view detailed performance metrics of fpool for 100 times in 5 seconds interval we can use the following command.
{{< highlight bash >}}
\# zpool iostat -v fpool 5 100
{{< /highlight >}}
The output for this command is shown in the following figure.

![](post-img/3180_02_24.png "Monitor ZFS pool performance")

The first row shows the entire pool capacity stats including how much space were used upon the sampling and how much was available. The second row shows how many reads and writes operations performed during the interval time and finally the last column shows the band width used for reading from this pools and writing into the pool.

The zpool iostat retrieve some of its information from the read-only attributes of the requested pools and the system metadata and calculate some other outputs by collecting sample information on each interval.

