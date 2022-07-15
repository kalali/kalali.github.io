# Getting started with CouchDB and MongoDB, Part II: Beginning with Security

 In the first part of this series I discussed how can we [perform CRUD operation in MongoDB and CouchDB](/2013/09/2013-09-12-getting-started-with-couchdb-and-mongodb-nosql-document-databases-part-i/). In this part we will look into security features of MongoDB and CouchDB. In the next two installments of this series I will go into how to apply the configuration that are described here and how to write codes that work with them.

### Basic security requirements

There are some basic features and functionalities which are required by different applications, in addition to tons of other more advanced fine grained features, which one can expect a semi-structured data storage server to provide. If the not provided, other measures should be applied to limit the effect of missing features risks. The basic features are as listed below:

* **Authentication**: Authentication refers to ensuring that if a token is presented to our system the system can verify that the token is valid, for example a username can be verified using the password that accompany it.
* **Access control: **To limit the access to specific resources/ set of resources to an authenticated client or a group of authenticated clients sharing common characteristics like a assigned role_name.
* **Transport safety: **To ensure that the data is being transferred over the network safely without possibility of tampering or reading depending on the required level of confidentiality.
* **On-Disk/In-memory Data Encryption: **To keep the data encrypted on disk to prevent RAW access to the data without presence of the decryption key/token. Same applies to the systems that are data memory intensive, the memory data in memory should be protected from memory dump/views.

### General Security precautions

* Do not run the server on default network interface which it is configured, usually 0.0.0.0 which means all available interfaces. Imagine you having a LAN and WAN interface and you want your NoSQL storage to only listen on the LAN interface while by default it listens on all interfaces (including your outbound WAN).
* Do not use the default port numbers, change the port numbers that your backend application server, database, NoSQL storage, ABCD network server listen on. No need to wait for intrusion on the default port number.
* Do not run your network server, e.g NoSQL server on root or any other privileged user.  Create a user which is relaxed enough in disk/network access to let the network server perform its task and not relaxer!
* Do not let the above user to have access to any portion of file server other than the one that it requires.
* Enable disk quota!
* If on-disk/in-memory encryption is supported depending on sensitivity of the data, enable it.
* Enable transport security. One way or another transport security is required to prevent others peeping, tampering the data or request responses!
* Make sure you enable your iptable and limit the above user to only have access to port/interfaces that it meant to have access nothing more.
* Disable any sample welcome page, any default "I am UP" message, any default "Welcome page" or anything else that your network server may have out of the box. This will prevent people from peeping into seeing what version of what network server you are running.
* Disable any extra interface/communication channel that your network server has and you are not using it. E.g In an application server we just don't need the JMX interface and thus we disable it rather than having a door into the application server, although the door is locked but...
* Do not use anything default, no port, no default username, no default password, no default welcome message, no default... Nothing default!

### MongoDB and Security

Below is some description on how MongoDB cover the basic security requirements.

* **Authentication**: Supports two types of access. Either no authentication is enabled and anonymous users can do read/write or authentication is enabled (per database) and users are authenticated and then checked for their authorization before accessing the database. At the token level, CouchDB supports OAuth, cookie based authentication and http basic authentication. We will go into details of these in the next installments of this blog series.
* **Authorization**: Supports role based access control. There are several roles which can be assigned to users. each role add some more privilege to the user(remember unauthenticated users cannot interact with the database when authentication is enabled). Details of the roles [MongoDB access control documentation](http://docs.mongodb.org/manual/reference/user-privileges/).
* **Transport** Security: In the community edition there is no build-in transport security out of the box, you can use the MongoDB enterprise or you need to build it from the source to get SSL included in your non-enterprise copy. Procedure described at [Configure SSL](http://docs.mongodb.org/manual/tutorial/configure-ssl/).
* **On-disk/In-memory encryption**: I am not aware of any built-in support for any of these. One possibility is to use OS level disk encryption and/or use application level encryption for the sensitive fields, which imposes limitation on indexing and similarity searches, in the document.

### CouchDB and Security

Below is some description on how Apache CouchDB cover the basic security requirements.

* **Authentication**: By default any connection to CouchDB has access to perform CRUD on the server (creating/reading/deleting databases) and also CRUD operations on documents stored in the databases. When authentication is enabled, we go into how each one of these tasks can be performed in next blog entry, the user's credentials is checked to let it perform CRUD on server or on databases or to perform read action on the database.
* **Authorization**: There are 3 roles in CouchDB, the server admin role which can do anything and everything possible in the server, the database admin role on a database and the read-only role on a database.
* **Transport**: Apache CouchDB has built-in supports for transport security using SSL and thus all communication that requires transport security (encryption, integrity) can be made over HTTPS.
* **On-Disk/In-memory encryption**: I am not aware of any built-in support for any of these. One possibility is to use OS level disk encryption and/or use application level encryption for the sensitive fields in the document. Application level encryption will imposes limitation on indexing and similarity searches.

This blog entry is wrote based on CouchDB 1.3.1 and MongoDB 2.4.5 and thus the descriptions are valid for these versions and they may differ in other versions. The sample codes should work with the mentioned versions and any newer release.

