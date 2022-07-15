# Getting started with CouchDB and MongoDB, Part I: CRUD operations

 In a series of blog I am going to write to cover CouchDB and MongoDB as two popular document databases. In each one of the entries I will show how can you do similar tasks with each one of these two so that you get some understanding of the differences and similarities between them. I am not going to do any comparison here but reading the series you can draw conclusion on which one seems more suitable for your case based judging by how different tasks can be accomplished using these two.

### Some technicalities

* All of the source codes for these series is available at [https://github.com/kalali/nosql](https://github.com/kalali/nosql) which you can get and run the samples.
* All sample codes assume default port numbers unless I mention otherwise in the blog entry itself.
* All the blog entries use CouchDB 1.3.1 and MongoDB 2.4.5 and thus I expect them to work fine with this version and any newer ones.

### A little about these two databases

* **MongoDB** and **CouchDB**: Both of them are document databases which lets you store freeform data into the storage in form of a document with key/value fields of data. Just look at JSON to see how a document would look like.
* **Apache CouchDB**: Provides built-in support for reading/writing JSON documents over a REST interface. Result of a query is JSON and the data you store is in form of JSON all through the HTTP interface. There are tens of access API implemented, each with its own unique characteristics, around the REST interface in different languages which one can use to avoid using the REST interface directly. List of these implementations are available at: [Related Projects](http://wiki.apache.org/couchdb/Related_Projects). Apache CouchDB has more emphasis on availability rather than consistency if you look at it from [CAP theorem](http://en.wikipedia.org/wiki/CAP_theorem) perspective. CouchDB is cross platform and developed in ErLang.
* **MongoDB**: MongoDB does not provide a built-in REST interface to interact with the document store and it does not provide direct support for storing and retrieving JSON documents however it provides a fast native protocol which can be access using the [MongoDB Drivers and Client Libraries](http://docs.mongodb.org/ecosystem/drivers/) which are available for almost any language you can think of, and also there are some [bridges](http://docs.mongodb.org/ecosystem/tools/http-interfaces/) which one can use to create a REST interface on top of the native interface. MongoDB has more emphasis on consistency rather than availability if you look at it from [CAP theorem](http://en.wikipedia.org/wiki/CAP_theorem) perspective. MongoDB is cross platform and developed in C++.

### Downloading and starting


* **MongoDB**: You can download MongoDB from  the [download page](http://www.mongodb.org/downloads) and after extracting it you can start it by navigating to bin directory and running ./mongo to start the document store. The default administration port is 28017 and the access port number is 27017 by default.
* **Apache CouchDB**: You can download Apache CouchDB from the [download page](http://couchdb.apache.org/#download) or if you are running some Linux distribution you can get it from the package repositories or you can just build it from the code. After installation run _sudo couchdb_ to start the server.

The basic scenario for this blog entry is to create a database, add a document, find that document back, update the document and read it back from the store, delete the document and finally to delete the database to make the sample code's execution repeatable.

The CouchDB sample code for the sample scenario:

 
{{< highlight java >}}

import javax.json.JsonObject;
import javax.json.JsonReader;
import javax.ws.rs.client.Client;
import javax.ws.rs.client.ClientBuilder;
import javax.ws.rs.client.Entity;
import javax.ws.rs.core.Form;
import javax.ws.rs.core.MediaType;
import javax.ws.rs.core.Response;
import java.io.StringReader;
import java.util.UUID;

/\*\*
 \* @author Masoud Kalali
 \*/

public class App {
    private static final String DB_BASE_URL = "http://127.0.0.1:5984/";
    private static final String STORE_NAME = "book_ds";
    private static Client c = ClientBuilder.newClient();

    public static void main(String\[\] args) {

        createDocumentStore();

        addingBookToDocumentStore();

        JsonObject secBook = readBook();

        String revision = secBook.getStringValue("_rev");
        updateBook(revision);

        JsonObject secBookAfterUpdate = readBook();
        deleteBookDocument(secBookAfterUpdate);

        deleteDocumentStore();
    }

    private static void createDocumentStore() {
        System.out.println("Creating Document Store...");
        javax.ws.rs.core.Response r = c.target(DB_BASE_URL).path(STORE_NAME).request().accept(MediaType.APPLICATION_JSON_TYPE).put(Entity.form(new Form()));
        if (!r.getStatusInfo().getFamily().equals(Response.Status.Family.SUCCESSFUL)) {
            throw new RuntimeException("Failed to create document storage: " + r.getStatusInfo().getReasonPhrase());
        }
    }

    private static void addingBookToDocumentStore() {
        System.out.println("Library document store created. Adding a book to the library...");
        javax.ws.rs.core.Response r = c.target(DB_BASE_URL).path(STORE_NAME).path("100").request(MediaType.APPLICATION_JSON_TYPE).put(Entity.text("{\\n" +
                "   \\"name\\": \\"GlassFish Security\\",\\n" +
                "   \\"author\\": \\"Masoud Kalali\\"\\n" +
                "}"));

        if (!r.getStatusInfo().getFamily().equals(Response.Status.Family.SUCCESSFUL)) {
            throw new RuntimeException("Failed to create book document in the store: " + r.getStatusInfo().getReasonPhrase());
        }
    }

    private static JsonObject readBook() {

        System.out.println("Reading the book from the library...");
        javax.ws.rs.core.Response r = c.target(DB_BASE_URL).path(STORE_NAME).path("100").request(MediaType.APPLICATION_JSON_TYPE).get();
        if (!r.getStatusInfo().getFamily().equals(Response.Status.Family.SUCCESSFUL)) {
            throw new RuntimeException("Failed to read the book document from the data store: " + r.getStatusInfo().getReasonPhrase());

        }
        String ec = r.readEntity(String.class);
        System.out.println(ec);
        JsonReader reader = new JsonReader(new StringReader(ec));
        return reader.readObject();
    }

    private static void updateBook(String revision) {
        System.out.println("Updating the GlassFish Security book...");
        javax.ws.rs.core.Response r = c.target(DB_BASE_URL).path(STORE_NAME).path("100").request(MediaType.APPLICATION_JSON_TYPE).put(Entity.text(

                "{\\"_rev\\":\\"" + revision + "\\",\\"name\\":\\"GlassFish Security And Java EE Security\\",\\"author\\":\\"Masoud Kalali\\"}"
        ));

        if (!r.getStatusInfo().getFamily().equals(Response.Status.Family.SUCCESSFUL)) {
            throw new RuntimeException("Failed to update book document in the data store: " + r.getStatusInfo().getReasonPhrase());
        }
    }

    private static void deleteBookDocument(JsonObject secBook) {
        System.out.println("Deleting the the GlassFish Security book's document...");
        javax.ws.rs.core.Response r = c.target(DB_BASE_URL).path(STORE_NAME).path("100").queryParam("rev", secBook.getStringValue("_rev")).request().buildDelete().invoke();
        if (!r.getStatusInfo().getFamily().equals(Response.Status.Family.SUCCESSFUL)) {
            throw new RuntimeException("Failed to delete book document in the data store: " + r.getStatusInfo().getReasonPhrase());
        }
    }

    private static void deleteDocumentStore() {
        System.out.println("Deleting the document store...");
        javax.ws.rs.core.Response r = c.target(DB_BASE_URL).path(STORE_NAME).request().accept(MediaType.APPLICATION_JSON_TYPE).delete();
        if (!r.getStatusInfo().getFamily().equals(Response.Status.Family.SUCCESSFUL)) {
            throw new RuntimeException("Failed to delete document storage: " + r.getStatusInfo().getReasonPhrase());
        }
    }
}
{{< /highlight >}}
I used JAX-RS and Some JSON-P in the code to add some salt to it rather than using plan http connection and plain-text content, but that aside the most important thing that you may want to remember is that we are using HTTP verbs for different actions, for example, PUT to create, DELETE to delete, POST to update and GET to query and read. The other important factor that you may have noticed is the _rev or the revision attribute of the document. You see that in order to update the document I specified the revision string, _rev in the document, also to delete the document I use the _rev id. This is required because at each point in time there might be multiple revisions of the document living in the database and you may read an older revision and that is the revision you can update (you can update a document revision because someone else might have updated the document while you were doing the update and thus your revision is old and you are updating your own revision). The POM file for the couchDB sample is as follow:
 
{{< highlight xml >}}
  
 4.0.0

    me.kalali.couchdb
    couchdb_ch01
    1.0-SNAPSHOT
    jar

    couchdb_ch01
    http://maven.apache.org

    UTF-8 

    org.glassfish.jersey.media
            jersey-media-moxy
            2.2 
        org.glassfish.jersey.core
            jersey-client
            2.2 
        org.glassfish
            javax.json
            1.0-b02 

    org.codehaus.mojo
                exec-maven-plugin
                1.2.1
                run-main
                        package
                        java 
                me.kalali.couchdb.ch01.App 
{{< /highlight >}}
Now, doing a clean install for the CouchDB project sample we get:

Creating Document Store...
Library document store created. Adding a book to the library...
Reading the book back from the library...
{{< highlight json >}}
{"_id":"100","_rev":"1-99fb78c8607deb8ff966e2e2b3f3f7b3","name":"GlassFish Security","author":"Masoud Kalali"}
{{< /highlight >}}
Updating the GlassFish Security book...
Book details after update:
{{< highlight json >}}
{"_id":"100","_rev":"2-27c86bc501856c8bbe301b5a27f8da02","name":"GlassFish Security And Java EE Security","author":"Masoud Kalali"}
{{< /highlight >}}
Deleting the the GlassFish Security book's document...
Deleting the document store...

If you pay attention you see that the _rev attribute has changed after updating the document, that is because by default read operation reads the latest revision of the document and in the second read after we updated the document the revision is changed and document that is read is the latest revision.

### The MongoDB code for the sample scenario
 
{{< highlight java >}}
import com.mongodb.BasicDBObject;
import com.mongodb.DB;
import com.mongodb.DBCollection;
import com.mongodb.DBObject;
import com.mongodb.MongoClient;
import com.mongodb.WriteResult;

import java.net.UnknownHostException;

/\*\*
 \* @author Masoud Kalali
 \*/
public class App {
    private static final String STORE_NAME = "book_ds";
    private static MongoClient mongoClient;

    public static void main(String\[\] args) throws UnknownHostException {

        mongoClient = new MongoClient("127.0.0.1", 27017);

        DBCollection collection = createDocumentStore();

        addBookToCollection(collection);

        DBObject secBook = readBook(collection);
        System.out.println("Book details read from document store: \\n" + secBook.toString());

        updateBook(collection);

        DBObject secBookAfterUpdate = readBook(collection);
        System.out.println("Book details after update: \\n" + secBookAfterUpdate.toString());

        deleteBookDocument(collection);

        deleteDocumentStore();
    }

    private static DBCollection createDocumentStore() {
        System.out.println("Creating Document Store...");
        DB db = mongoClient.getDB(STORE_NAME);
        DBCollection collection = db.getCollection("books");
        return collection;
    }

    private static void addBookToCollection(DBCollection collection) {
        System.out.println("Library document store created. Adding a book to the library...");
        DBObject object = new BasicDBObject();
        object.put("name", "GlassFish Security");
        object.put("_id", "100");
        object.put("author", "Masoud Kalali");
        collection.insert(object);
    }

    private static DBObject readBook(DBCollection collection) {

        DBObject secBook = new BasicDBObject();
        secBook.put("_id", "100");
        return collection.findOne(secBook);
    }

    private static void updateBook(DBCollection collection) {
        System.out.println("Updating the GlassFish Security book...");
        DBObject matchings = collection.findOne(new BasicDBObject("_id", "100"));
        DBObject updateStmt = new BasicDBObject();
        updateStmt.put("name", "GlassFish Security And Java EE Security");
        updateStmt.put("_id", "100");
        updateStmt.put("author", "Masoud Kalali");
        WriteResult result = collection.update(matchings, updateStmt);
        System.out.println("Updated documents count: " + result.getN());
    }

    private static void deleteBookDocument(DBCollection collection) {
        DBObject object = new BasicDBObject();
        object.put("_id", "100");
        WriteResult result = collection.remove(object);
        System.out.println("Deleted documents count: " + result.getN());
    }

    private static void deleteDocumentStore() {
        System.out.println("Deleting the document store...");
        mongoClient.dropDatabase(STORE_NAME);
    }
}
{{< /highlight >}}
As you can see in the code we are using the Mongo API rather than using direct HTTP communication and that is because MongoDB does not provide a built-in REST interface but rather it has its own native protocol as explained above. The other important part which will be in comparison with CouchDB is absence of revision or _rev or an attribute with similar semantic role. That is because MongoDB does not keep multiple version of the document Look at [CAP theorem](http://en.wikipedia.org/wiki/CAP_theorem) for more information on consistency and availability attributes of these two databases. Now, the POM file for the MongoDB sample is as follow:

 
 {{< highlight xml >}}
 4.0.0

    me.kalali.mongodb
    mongodb_ch01
    1.0-SNAPSHOT
    jar

    mongodb_ch01
    http://maven.apache.org

    UTF-8 

    org.mongodb
            mongo-java-driver
            2.10.1 

    org.codehaus.mojo
                exec-maven-plugin
                1.2.1
                run-main
                        package
                        java 
                me.kalali.mongodb.ch01.App 
{{< /highlight >}}
The output for doing a _clean install_ on the MongoDB sample will be as shown below:

Creating Document Store...
Library document store created. Adding a book to the library...
Book details read from document store:
{{< highlight json >}}
{ "_id" : "100" , "name" : "GlassFish Security" , "author" : "Masoud Kalali"}
{{< /highlight >}}
Updating the GlassFish Security book...
Updated documents count: 1
Book details after update:
{{< highlight json >}}
{ "_id" : "100" , "name" : "GlassFish Security And Java EE Security" , "author" : "Masoud Kalali"}
{{< /highlight >}}
Deleting the the GlassFish Security book's document...
Deleting the document store...

The sample code for this blog entry is available at [GutHub:Kalali](https://github.com/kalali/nosql/)

This blog entry is wrote based on CouchDB 1.3.1 and MongoDB 2.4.5 and thus the descriptions are valid for these versions and they may differ in other versions. The sample codes should work with the mentioned versions and any newer release.

