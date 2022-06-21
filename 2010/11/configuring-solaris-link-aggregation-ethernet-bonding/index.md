# Configuring Solaris Link Aggregation (Ethernet bonding)

 Link aggregation or commonly known Ethernet bonding allows us to enhance the network availability and performance by combining multiple network interfaces together and form an aggregation of those interfaces which act as a single network interface with greatly enhanced availability and performance.

When we aggregate two or more network interfaces, we are forming a new network interface on top of those physical interfaces combined in the link layer.

We need to have at least two network interfaces in our machine to create a link aggregation. The interfaces must be unplumb-ed in order for us to use them in a link aggregation. Following command shows how to unplumb a network interface.
{{< highlight bash >}}
\# ifconfig e1000g1 down unplumb
{{< /highlight >}}
We should disable the NWAM because link aggregation and NWAM cannot co-exist.
{{< highlight bash >}}
\# svcadm disable network/physical:nwam
{{< /highlight >}}
The interfaces we want to use in a link aggregation must not be part of virtual interface; otherwise it will not be possible to create the aggregation. To ensure that an interface is not part of a virtual interface checks the output for the following command.
{{< highlight bash >}}
\# dladm show-link
{{< /highlight >}}
Following figure shows that my e1000g0 has a virtual interface on top of it so it cannot be used in an aggregation.

[![](post-img/3180_04_14.png "3180_04_14")](post-img/3180_04_14.png)

To delete the virtual interface we can use the dladm command as follow
{{< highlight bash >}}
\# dladm delete-vlan vlan0
{{< /highlight >}}
The link aggregation as the name suggests works in the link layer and therefore we will use dladm command to make the necessary configurations.  We use create-aggr subcommand of dladm command with the following syntax to create aggregation links.
{{< highlight bash >}}
dladm  create-aggr \[-l interface_name\]\*  aggregation_name
{{< /highlight >}}
In this syntax we should have at least two occurrence of -l interface_name option followed by the aggregation name.

Assuming that we have e1000g0 and e1000g1 in our disposal following commands configure an aggregation link on top of them.
{{< highlight bash >}}
\# dladm create-aggr -l e1000g0 -l e1000g1 aggr0
{{< /highlight >}}
Now that the aggregation is created we can configure its IP allocation in the same way that we configure a physical or virtual network interface. Following command plumb the aggr0 interface, assign a static IP address to it and bring the interface up.
{{< highlight bash >}}
\# ifconfig aggr0 plumb 10.0.2.25/24 up
{{< /highlight >}}
Now we can use ifconfig command to see status of our new aggregated interface.
{{< highlight bash >}}
\# ifconfig aggr0
{{< /highlight >}}
The result of the above command should be similar to the following figure.

[![](post-img/3180_04_15.png "3180_04_15")](post-img/3180_04_15.png)

To get a list of all available network interfaces either virtual or physical we can use the dladm command as follow
{{< highlight bash >}}
\# dladm show-link
{{< /highlight >}}
And to get a list of aggregated interfaces we can use another subcommand of dladm as follow.
{{< highlight bash >}}
\# dladm show-aggr
{{< /highlight >}}
The output for previous dladm commands is shown in the following figure.

[![](post-img/3180_04_16.png "3180_04_16")](post-img/3180_04_16.png)

We can change an aggregation link underlying interfaces by adding an interface to the aggregation or removing one from the aggregation using add-aggr and remove-aggr subcommands of dladm command.  For example:
{{< highlight bash >}}
\# dladm add-aggr -l e1000g2 aggr0
\# dladm remove-aggr -l e1000g1 aggr0
{{< /highlight >}}
The aggregation we created will survive the reboot but our ifconfig configuration will not survive a reboot unless we persist it using the interface configuration files.

To make the aggregation IP configuration persistent we just need to add create /etc/hostname.aggr0 file with the following content:
{{< highlight bash >}}
10.0.2.25/24
{{< /highlight >}}
The interface configuration files are discussed in recipe 2 and 3 of this chapter in great details. Reviewing them is recommended.

To delete an aggregation we can use delete-aggr subcommand of dladm command. For example to delete aggr0 we can use the following commands.
{{< highlight bash >}}
\# ifconfig aggr0 down unplumb
\# dladm delete-aggr aggr0
{{< /highlight >}}
As you can see before we could delete the aggregation we should bring down its interface and unplumb it.

In recipe 11 we discussed IPMP which allows us to have high availability by grouping network interfaces and when required automatically failing over the IP address of any failed interface to a healthy one. In this recipe we saw how we can join a group of interfaces together to have a better performance. By grouping a set of aggregations we can have the high availability that IPMP offers along with the performance boost that link aggregation offers.

The link aggregation works in layer 2 meaning that the aggregation groups the interfaces in layer 2 of network where network packets are dealt with. In this layer the network layer’s packets are extracted or created with the designated IP address of the aggregation and then are delivered to the lower, higher layer.

