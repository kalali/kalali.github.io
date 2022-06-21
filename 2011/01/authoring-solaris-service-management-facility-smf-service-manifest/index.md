# Writing Solaris Service Management Facility (SMF) service manifest

 SMF services are basically daemons staying in background and waiting for the requests which they should server, when the request come the daemon wake ups, serve the request and then wait for the next request to come. The services are building using software development platforms and languages but they have one common aspect which we are going to discuss here. The service manifests which describe the service for the SMF and let the SMF manage and understand the service life cycle. To write a service manifest we should be familiar with XML and the service manifest schema located at /usr/share/lib/xml/dtd/service_bundle.dtd.1.  This file specifies what elements can be used for describing a service for the SMF. Next thing we need is a text editor and preferable an XML aware text editor. The Gnome gedit can do the task for us. The service manifest file composed of 6 important elements which are listed below:

*   The service declaration:  specifies the service name, type and instantiation model
*   Zero or more dependency declaration elements: Specifies the service dependencies
*   Lifecycle methods: Specifies the start, stop and refresh methods
*   Property groups: Which property groups the service description has.
*   Stability element: how stable the service interface is considering version changes
*   Template element: more human readable information for the service.

To describe a service, first thing we need to do is identifying the service itself. Following snippet shows how we can declare a service named jws.
{{< highlight xml >}}
<service name='network/jws’ type='service' version='1'>
<single_instance/>
{{< /highlight >}}


The first line specifies the service name, version and its type. The service name attribute forms the FMRI of the service which for this instance will be svc:/network/jws. In the second line we are telling SMF that it should only instantiate one instance of this service which will be svc:/network/jws:default. We can use the create_default_instance element to manipulate automatic creation of the default instance. All of the elements which we are going to mention in the following sections of this article are immediate child elements of the service element which itself is a child element of the service_bundle element. The next important element is dependency declaration element. We can have one or more of this element in our service manifest.
{{< highlight xml >}}
<dependency name='net-physica' grouping='require_all ' restart_on='none' type='service'>
<service_fmri value='svc:/network/physical'/>
</dependency>
{{< /highlight >}}


Here we are telling that our service depends on the svc:/network/physical service and this service needs to be online before our service can start. Some of the values for the grouping attribute are as follow:

*   The require_all which represent that all services marked with this grouping must be online before our service came online
*   The require_any which represents that any of the services in this grouping suffice and our service can become online if one of them is online
*   The optional_all presence of the services marked with this grouping is optional for our service. Our service works with or without them.
*   The exclude_all:  specifies the services which may have conflict with our service and we cannot become online in presence of them

The next important elements are specifying how the SMF should start, stop and refresh the service. For these tasks we use three exec_method elements as follow:
{{< highlight xml >}}
<exec_method name='start' type='method' exec='/opt/jws/runner start' timeout_seconds='60'>
</exec_method>
{{< /highlight >}}


This is the start method, SMF will invoke what the exec attribute specifies when it want to start the service.
{{< highlight xml >}}
<exec_method name='stop' type='method' exec=':kill' timeout_seconds='60'>
</exec_method>
{{< /highlight >}}


The SMF will terminate the process it started for the service using a kill signal. By default it uses the SIGTERM but we can specify our own signal. For example we can use 'kill -9' or 'kill -HUP' or any other signal we find appropriate for our service termination.
{{< highlight xml >}}

{{< /highlight >}}
<exec_method name='refresh' type='method' exec='/opt/jws/runner reload_cof' timeout_seconds='60'>
</exec_method>

The final method which we should describe is the refresh method in which we should reload the service configuration without disturbing its function. The start and stop methods description are required to be present in the manifest in order to import it into the SMF repository but the refresh method description and implementation is optional. The timeout_seconds='60' specifies that the SMF should wait for 60 seconds before aborting the method execution and retrying it over. We can use longer timeout when we know that the execution of the method take longer or lower timeout when we know that our service starts sooner. The property group elements specify which property groups the SMF should associate with the service.  We can use as many of the SMF built-in property groups or define our own property groups.
{{< highlight xml >}}
<property_group name='startd' type='framework'>
<propval name='ignore_error' type='astring' value='core,signal'/>
</property_group>
{{< /highlight >}}


The above snippet shows how we can define using a framework built-in property group. The built-in property groups and their properties have meaning for the SMF framework and change its way of dealing with our service.  For example the above snippet says that the SMF should ignore core dump termination of suppresses of this service via a kill signal from another application. Next important element is the stability element which specifies whether the property names, service name, its dependencies and other service attributes may or may not change for the next release. For example the following snippet specifies that the service attributes may change in the next release.
{{< highlight xml >}}
<stability value='Unstable'/>
{{< /highlight >}}


Finally we have the template element which contains descriptive information about the service, for example we can have something like the following element which describes our service for human users.
{{< highlight xml >}}
<template>
<common_name>
<loctext xml:lang=’Java'>Java Network Server </loctext>
</common_name>
<documentation>
<manpage title='JWS' section='1M' manpath='/usr/share/man'/>
</documentation>
</template>
{{< /highlight >}}


The entire element is self describing except for the man page element which specifies where the man utility command should look for the man pages for the jws service.

All of these elements should be saved into an XML file with the following structure:
{{< highlight xml >}}
<?xml version='1.0'?>
<!DOCTYPE service_bundle SYSTEM '/usr/share/lib/xml/dtd/service_bundle.dtd.1'>
<service_bundle ...>
<service ...>
<single_instance .../>
<dependency ... />
<exec_method... />
<property_group .../>
<stability .../>
<template ...>
</service>
</service_bundle>
{{< /highlight >}}


This is a raw representation of what the service manifest bare bone cal look like. The syntax is not accurate and the attributes and child elements are omitted to make it easier to scan by the eyes. The service_bundle, service, and  start and stop method elements are mandatory while other elements are optional. Now we should install the service into the SMF repository and then administrate it. For installing the service we can use the following command.

\# svccfg import /path/to/manifest.xml

When we import the service manifest into the service repository, SMF will scan the service profiles and check whether this service is required to be enabled either directly or because of a dependent service or not. If required, the SMF will use the start method specified in the manifest to start the service. But before starting it, SMF will check its dependencies and start any required service recursively if they are not already online. Reviewing the XML schema located at /usr/share/lib/xml/dtd/service_bundle.dtd.1 and the staying tuned for my next few entries is recommended for grasping a better understanding of the whole SMF and Solaris Services administration/management.

