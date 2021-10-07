---
title: 'Express Tutorial Part 3: Using a Database (with Mongoose)'
slug: Learn/Server-side/Express_Nodejs/mongoose
tags:
  - Beginner
  - CodingScripting
  - Database
  - Express
  - Learn
  - Node
  - ODM
  - mongoose
  - nodejs
  - orm
  - server-side
---
<div>{{LearnSidebar}}</div>

<div>{{PreviousMenuNext("Learn/Server-side/Express_Nodejs/skeleton_website", "Learn/Server-side/Express_Nodejs/routes", "Learn/Server-side/Express_Nodejs")}}</div>

<p>This article briefly introduces databases, and how to use them with Node/Express apps. It then goes on to show how we can use <a href="https://mongoosejs.com/">Mongoose</a> to provide database access for the <a href="/en-US/docs/Learn/Server-side/Express_Nodejs/Tutorial_local_library_website">LocalLibrary</a> website. It explains how object schema and models are declared, the main field types, and basic validation. It also briefly shows a few of the main ways in which you can access model data.</p>

<table>
 <tbody>
  <tr>
   <th scope="row">Prerequisites:</th>
   <td><a href="/en-US/docs/Learn/Server-side/Express_Nodejs/skeleton_website">Express Tutorial Part 2: Creating a skeleton website</a></td>
  </tr>
  <tr>
   <th scope="row">Objective:</th>
   <td>To be able to design and create your own models using Mongoose.</td>
  </tr>
 </tbody>
</table>

<h2 id="Overview">Overview</h2>

<p>Library staff will use the Local Library website to store information about books and borrowers, while library members will use it to browse and search for books, find out whether there are any copies available, and then reserve or borrow them. In order to store and retrieve information efficiently, we will store it in a <em>database</em>.</p>

<p>Express apps can use many different databases, and there are several approaches you can use for performing <strong>C</strong>reate, <strong>R</strong>ead, <strong>U</strong>pdate and <strong>D</strong>elete (CRUD) operations. This tutorial provides a brief overview of some of the available options and then goes on to show in detail the particular mechanisms selected.</p>

<h3 id="What_databases_can_I_use">What databases can I use?</h3>

<p><em>Express</em> apps can use any database supported by <em>Node</em> (<em>Express</em> itself doesn't define any specific additional behavior/requirements for database management). There are <a href="https://expressjs.com/en/guide/database-integration.html">many popular options</a>, including PostgreSQL, MySQL, Redis, SQLite, and MongoDB.</p>

<p>When choosing a database, you should consider things like time-to-productivity/learning curve, performance, ease of replication/backup, cost, community support, etc. While there is no single "best" database, almost any of the popular solutions should be more than acceptable for a small-to-medium-sized site like our Local Library.</p>

<p>For more information on the options see <a href="https://expressjs.com/en/guide/database-integration.html">Database integration</a> (Express docs).</p>

<h3 id="What_is_the_best_way_to_interact_with_a_database">What is the best way to interact with a database?</h3>

<p>There are two common approaches for interacting with a database: </p>

<ul>
 <li>Using the databases' native query language (e.g. SQL)</li>
 <li>Using an Object Data Model ("ODM") or an Object Relational Model ("ORM"). An ODM/ORM represents the website's data as JavaScript objects, which are then mapped to the underlying database. Some ORMs are tied to a specific database, while others provide a database-agnostic backend.</li>
</ul>

<p>The very best <em>performance</em> can be gained by using SQL, or whatever query language is supported by the database. ODM's are often slower because they use translation code to map between objects and the database format, which may not use the most efficient database queries (this is particularly true if the ODM supports different database backends, and must make greater compromises in terms of what database features are supported).</p>

<p>The benefit of using an ORM is that programmers can continue to think in terms of JavaScript objects rather than database semantics — this is particularly true if you need to work with different databases (on either the same or different websites). They also provide an obvious place to perform data validation.</p>

<div class="notecard note">
  <p><strong>Note:</strong> Using ODM/ORMs often results in lower costs for development and maintenance! Unless you're very familiar with the native query language or performance is paramount, you should strongly consider using an ODM.</p>
</div>

<h3 id="What_ORMODM_should_I_use">What ORM/ODM should I use?</h3>

<p>There are many ODM/ORM solutions available on the NPM package manager site (check out the <a href="https://www.npmjs.com/browse/keyword/odm">odm</a> and <a href="https://www.npmjs.com/browse/keyword/orm">orm</a> tags for a subset!).</p>

<p>A few solutions that were popular at the time of writing are:</p>

<ul>
 <li><a href="https://www.npmjs.com/package/mongoose">Mongoose</a>: Mongoose is a <a href="https://www.mongodb.org/">MongoDB</a> object modeling tool designed to work in an asynchronous environment.</li>
 <li><a href="https://www.npmjs.com/package/waterline">Waterline</a>: An ORM extracted from the Express-based <a href="https://sailsjs.com/">Sails</a> web framework. It provides a uniform API for accessing numerous different databases, including Redis, MySQL, LDAP, MongoDB, and Postgres.</li>
 <li><a href="https://www.npmjs.com/package/bookshelf">Bookshelf</a>: Features both promise-based and traditional callback interfaces, providing transaction support, eager/nested-eager relation loading, polymorphic associations, and support for one-to-one, one-to-many, and many-to-many relations. Works with PostgreSQL, MySQL, and SQLite3.</li>
 <li><a href="https://www.npmjs.com/package/objection">Objection</a>: Makes it as easy as possible to use the full power of SQL and the underlying database engine (supports SQLite3, Postgres, and MySQL).</li>
 <li><a href="https://www.npmjs.com/package/sequelize">Sequelize</a> is a promise-based ORM for Node.js and io.js. It supports the dialects PostgreSQL, MySQL, MariaDB, SQLite, and MSSQL and features solid transaction support, relations, read replication and more.</li>
 <li><a href="https://node-orm.readthedocs.io/en/latest/">Node ORM2</a> is an Object Relationship Manager for NodeJS. It supports MySQL, SQLite, and Progress, helping to work with the database using an object-oriented approach.</li>
 <li><a href="https://graphql.org/">GraphQL</a>: Primarily a query language for restful APIs, GraphQL is very popular, and has features available for reading data from databases.</li>
</ul>

<p>As a general rule, you should consider both the features provided and the "community activity" (downloads, contributions, bug reports, quality of documentation, etc.) when selecting a solution. At the time of writing Mongoose is by far the most popular ODM, and is a reasonable choice if you're using MongoDB for your database.</p>

<h3 id="Using_Mongoose_and_MongoDb_for_the_LocalLibrary">Using Mongoose and MongoDb for the LocalLibrary</h3>

<p>For the <em>Local Library</em> example (and the rest of this topic) we're going to use the <a href="https://www.npmjs.com/package/mongoose">Mongoose ODM</a> to access our library data. Mongoose acts as a front end to <a href="https://www.mongodb.com/what-is-mongodb">MongoDB</a>, an open source <a href="https://en.wikipedia.org/wiki/NoSQL">NoSQL</a> database that uses a document-oriented data model. A “collection” of “documents” in a MongoDB database <a href="https://docs.mongodb.com/manual/core/databases-and-collections/#collections">is analogous to</a> a “table” of “rows” in a relational database.</p>

<p>This ODM and database combination is extremely popular in the Node community, partially because the document storage and query system looks very much like JSON, and is hence familiar to JavaScript developers.</p>

<div class="notecard note">
  <p><strong>Note:</strong> You don't need to know MongoDB in order to use Mongoose, although parts of the <a href="https://mongoosejs.com/docs/guide.html">Mongoose documentation</a> <em>are</em> easier to use and understand if you are already familiar with MongoDB.</p>
</div>

<p>The rest of this tutorial shows how to define and access the Mongoose schema and models for the <a href="/en-US/docs/Learn/Server-side/Express_Nodejs/Tutorial_local_library_website">LocalLibrary website</a> example.</p>

<h2 id="Designing_the_LocalLibrary_models">Designing the LocalLibrary models</h2>

<p>Before you jump in and start coding the models, it's worth taking a few minutes to think about what data we need to store and the relationships between the different objects.</p>

<p>We know that we need to store information about books (title, summary, author, genre, ISBN) and that we might have multiple copies available (with globally unique ids, availability statuses, etc.). We might need to store more information about the author than just their name, and there might be multiple authors with the same or similar names. We want to be able to sort information based on the book title, author, genre, and category.</p>

<p>When designing your models it makes sense to have separate models for every "object" (a group of related information). In this case some obvious candidates for these models are books, book instances, and authors.</p>

<p>You might also want to use models to represent selection-list options (e.g. like a drop-down list of choices), rather than hard-coding the choices into the website itself — this is recommended when all the options aren't known up front or may change. A good example is a genre (e.g. fantasy, science fiction, etc.).</p>

<p>Once we've decided on our models and fields, we need to think about the relationships between them.</p>

<p>With that in mind, the UML association diagram below shows the models we'll define in this case (as boxes). As discussed above, we've created models for the book (the generic details of the book), book instance (status of specific physical copies of the book available in the system), and author. We have also decided to have a model for the genre so that values can be created dynamically. We've decided not to have a model for the <code>BookInstance:status</code> — we will hard code the acceptable values because we don't expect these to change. Within each of the boxes, you can see the model name, the field names and types, and also the methods and their return types.</p>

<p>The diagram also shows the relationships between the models, including their <em>multiplicities</em>. The multiplicities are the numbers on the diagram showing the numbers (maximum and minimum) of each model that may be present in the relationship. For example, the connecting line between the boxes shows that <code>Book</code> and a <code>Genre</code> are related. The numbers close to the <code>Book</code> model show that a <code>Genre</code> must have zero or more <code>Book</code>s (as many as you like), while the numbers on the other end of the line next to the <code>Genre</code> show that a book can have zero or more associated <code>Genre</code>s.</p>

<div class="notecard note">
  <p><strong>Note:</strong> As discussed in our <a href="#mongoose_primer">Mongoose primer</a> below it is often better to have the field that defines the relationship between the documents/models in just <em>one</em> model (you can still find the reverse relationship by searching for the associated <code>_id</code> in the other model). Below we have chosen to define the relationship between <code>Book</code>/<code>Genre</code> and <code>Book</code>/<code>Author</code> in the Book schema, and the relationship between the <code>Book</code>/<code>BookInstance</code> in the <code>BookInstance</code> Schema. This choice was somewhat arbitrary — we could equally well have had the field in the other schema.</p>
</div>

<p><img alt="Mongoose Library Model  with correct cardinality" src="library_website_-_mongoose_express.png"></p>

<div class="notecard note">
  <p><strong>Note:</strong> The next section provides a basic primer explaining how models are defined and used. As you read it, consider how we will construct each of the models in the diagram above.</p>
</div>

<h2 id="Mongoose_primer">Mongoose primer</h2>

<p>This section provides an overview of how to connect Mongoose to a MongoDB database, how to define a schema and a model, and how to make basic queries. </p>

<div class="notecard note">
  <p><strong>Note:</strong> This primer is heavily influenced by the <a href="https://www.npmjs.com/package/mongoose">Mongoose quick start</a> on <em>npm</em> and the <a href="https://mongoosejs.com/docs/guide.html">official documentation</a>.</p>
</div>

<h3 id="Installing_Mongoose_and_MongoDB">Installing Mongoose and MongoDB</h3>

<p>Mongoose is installed in your project (<strong>package.json</strong>) like any other dependency — using NPM. To install it, use the following command inside your project folder:</p>

<pre class="brush: bash">npm install mongoose
</pre>

<p>Installing <em>Mongoose</em> adds all its dependencies, including the MongoDB database driver, but it does not install MongoDB itself. If you want to install a MongoDB server then you can <a href="https://www.mongodb.com/download-center">download installers from here</a> for various operating systems and install it locally. You can also use cloud-based MongoDB instances.</p>

<div class="notecard note">
  <p><strong>Note:</strong> For this tutorial, we'll be using the <a href="https://www.mongodb.com/">MongoDB Atlas</a> cloud-based <em>database as a service</em> free tier to provide the database. This is suitable for development and makes sense for the tutorial because it makes "installation" operating system independent (database-as-a-service is also one approach you might use for your production database).</p>
</div>

<h3 id="Connecting_to_MongoDB">Connecting to MongoDB</h3>

<p><em>Mongoose</em> requires a connection to a MongoDB database. You can <code>require()</code> and connect to a locally hosted database with <code>mongoose.connect()</code>, as shown below.</p>

<pre class="brush: js">//Import the mongoose module
var mongoose = require('mongoose');

//Set up default mongoose connection
var mongoDB = 'mongodb://127.0.0.1/my_database';
mongoose.connect(mongoDB, {useNewUrlParser: true, useUnifiedTopology: true});

//Get the default connection
var db = mongoose.connection;

//Bind connection to error event (to get notification of connection errors)
db.on('error', console.error.bind(console, 'MongoDB connection error:'));</pre>

<p>You can get the default <code>Connection</code> object with <code>mongoose.connection</code>. Once connected, the open event is fired on the <code>Connection</code> instance.</p>

<div class="notecard note">
  <p><strong>Note:</strong> If you need to create additional connections you can use <code>mongoose.createConnection()</code>. This takes the same form of database URI (with host, database, port, options etc.) as <code>connect()</code> and returns a <code>Connection</code> object).</p>
</div>

<h3 id="Defining_and_creating_models">Defining and creating models</h3>

<p>Models are <em>defined</em> using the <code>Schema</code> interface. The Schema allows you to define the fields stored in each document along with their validation requirements and default values. In addition, you can define static and instance helper methods to make it easier to work with your data types, and also virtual properties that you can use like any other field, but which aren't actually stored in the database (we'll discuss a bit further below).</p>

<p>Schemas are then "compiled" into models using the <code>mongoose.model()</code> method. Once you have a model you can use it to find, create, update, and delete objects of the given type.</p>

<div class="notecard note">
  <p><strong>Note:</strong> Each model maps to a <em>collection</em> of <em>documents</em> in the MongoDB database. The documents will contain the fields/schema types defined in the model <code>Schema</code>.</p>
</div>

<h4 id="Defining_schemas">Defining schemas</h4>

<p>The code fragment below shows how you might define a simple schema. First you <code>require()</code> mongoose, then use the Schema constructor to create a new schema instance, defining the various fields inside it in the constructor's object parameter.</p>

<pre class="brush: js">//Require Mongoose
var mongoose = require('mongoose');

//Define a schema
var Schema = mongoose.Schema;

var SomeModelSchema = new Schema({
  a_string: String,
  a_date: Date
});
</pre>

<p>In the case above we just have two fields, a string and a date. In the next sections, we will show some of the other field types, validation, and other methods.</p>

<h4 id="Creating_a_model">Creating a model</h4>

<p>Models are created from schemas using the <code>mongoose.model()</code> method:</p>

<pre class="brush: js">// Define schema
var Schema = mongoose.Schema;

var SomeModelSchema = new Schema({
  a_string: String,
  a_date: Date
});

<strong>// Compile model from schema
var SomeModel = mongoose.model('SomeModel', SomeModelSchema );</strong></pre>

<p>The first argument is the singular name of the collection that will be created for your model (Mongoose will create the database collection for the above model <em>SomeModel</em> above), and the second argument is the schema you want to use in creating the model.</p>

<div class="notecard note">
  <p><strong>Note:</strong> Once you've defined your model classes you can use them to create, update, or delete records, and run queries to get all records or particular subsets of records. We'll show you how to do this in the <a href="#using_models">Using models</a> section, and when we create our views.</p>
</div>

<h4 id="Schema_types_fields">Schema types (fields)</h4>

<p>A schema can have an arbitrary number of fields — each one represents a field in the documents stored in <em>MongoDB</em>.
An example schema showing many of the common field types and how they are declared is shown below.</p>

<pre class="brush: js">var schema = new Schema(
{
  name: String,
  binary: Buffer,
  living: Boolean,
  updated: { type: Date, default: Date.now() },
  age: { type: Number, min: 18, max: 65, required: true },
  mixed: Schema.Types.Mixed,
  _someId: Schema.Types.ObjectId,
  array: [],
  ofString: [String], // You can also have an array of each of the other types too.
  nested: { stuff: { type: String, lowercase: true, trim: true } }
})</pre>

<p>Most of the <a href="https://mongoosejs.com/docs/schematypes.html">SchemaTypes</a> (the descriptors after “type:” or after field names) are self-explanatory. The exceptions are:</p>

<ul>
 <li><code>ObjectId</code>: Represents specific instances of a model in the database. For example, a book might use this to represent its author object. This will actually contain the unique ID (<code>_id</code>) for the specified object. We can use the <code>populate()</code> method to pull in the associated information when needed.</li>
 <li><code><a href="https://mongoosejs.com/docs/schematypes.html#mixed">Mixed</a></code>: An arbitrary schema type.</li>
 <li><code>[]</code>: An array of items. You can perform JavaScript array operations on these models (push, pop, unshift, etc.). The examples above show an array of objects without a specified type and an array of <code>String</code> objects, but you can have an array of any type of object.</li>
</ul>

<p>The code also shows both ways of declaring a field:</p>

<ul>
 <li>Field <em>name</em> and <em>type</em> as a key-value pair (i.e. as done with fields <code>name</code>, <code>binary</code> and <code>living</code>).</li>
 <li>Field <em>name</em> followed by an object defining the <code>type</code>, and any other <em>options</em> for the field. Options include things like:
  <ul>
   <li>default values.</li>
   <li>built-in validators (e.g. max/min values) and custom validation functions.</li>
   <li>Whether the field is required</li>
   <li>Whether <code>String</code> fields should automatically be set to lowercase, uppercase, or trimmed (e.g. <code>{ type: <strong>String</strong>, lowercase: true, trim: true }</code>)</li>
  </ul>
 </li>
</ul>

<p>For more information about options see <a href="https://mongoosejs.com/docs/schematypes.html">SchemaTypes</a> (Mongoose docs).</p>

<h4 id="Validation">Validation</h4>

<p>Mongoose provides built-in and custom validators, and synchronous and asynchronous validators. It allows you to specify both the acceptable range of values and the error message for validation failure in all cases.</p>

<p>The built-in validators include:</p>

<ul>
 <li>All <a href="https://mongoosejs.com/docs/schematypes.html">SchemaTypes</a> have the built-in <a href="https://mongoosejs.com/docs/api.html#schematype_SchemaType-required">required</a> validator. This is used to specify whether the field must be supplied in order to save a document.</li>
 <li><a href="https://mongoosejs.com/docs/api.html#schema-number-js">Numbers</a> have <a href="https://mongoosejs.com/docs/api.html#schema_number_SchemaNumber-min">min</a> and <a href="https://mongoosejs.com/docs/api.html#schema_number_SchemaNumber-max">max</a> validators.</li>
 <li><a href="https://mongoosejs.com/docs/api.html#schema-string-js">Strings</a> have:
  <ul>
   <li><a href="https://mongoosejs.com/docs/api.html#schema_string_SchemaString-enum">enum</a>: specifies the set of allowed values for the field.</li>
   <li><a href="https://mongoosejs.com/docs/api.html#schema_string_SchemaString-match">match</a>: specifies a regular expression that the string must match.</li>
   <li><a href="https://mongoosejs.com/docs/api.html#schema_string_SchemaString-maxlength">maxLength</a> and <a href="https://mongoosejs.com/docs/api.html#schema_string_SchemaString-minlength">minLength</a> for the string.</li>
  </ul>
 </li>
</ul>

<p>The example below (slightly modified from the Mongoose documents) shows how you can specify some of the validator types and error messages:</p>

<pre class="brush: js">var breakfastSchema = new Schema({
  eggs: {
    type: Number,
    min: [6, 'Too few eggs'],
    max: 12,
    required: [true, 'Why no eggs?']
  },
  drink: {
    type: String,
    enum: ['Coffee', 'Tea', 'Water',]
  }
});
</pre>

<p>For complete information on field validation see <a href="https://mongoosejs.com/docs/validation.html">Validation</a> (Mongoose docs).</p>

<h4 id="Virtual_properties">Virtual properties</h4>

<p>Virtual properties are document properties that you can get and set but that do not get persisted to MongoDB. The getters are useful for formatting or combining fields, while setters are useful for de-composing a single value into multiple values for storage. The example in the documentation constructs (and deconstructs) a full name virtual property from a first and last name field, which is easier and cleaner than constructing a full name every time one is used in a template.</p>

<div class="notecard note">
  <p><strong>Note:</strong> We will use a virtual property in the library to define a unique URL for each model record using a path and the record's <code>_id</code> value.</p>
</div>

<p>For more information see <a href="https://mongoosejs.com/docs/guide.html#virtuals">Virtuals</a> (Mongoose documentation).</p>

<h4 id="Methods_and_query_helpers">Methods and query helpers</h4>

<p>A schema can also have <a href="https://mongoosejs.com/docs/guide.html#methods">instance methods</a>, <a href="https://mongoosejs.com/docs/guide.html#statics">static methods</a>, and <a href="https://mongoosejs.com/docs/guide.html#query-helpers">query helpers</a>. The instance and static methods are similar, but with the obvious difference that an instance method is associated with a particular record and has access to the current object. Query helpers allow you to extend mongoose's <a href="https://mongoosejs.com/docs/queries.html">chainable query builder API</a> (for example, allowing you to add a query "byName" in addition to the <code>find()</code>, <code>findOne()</code> and <code>findById()</code> methods).</p>

<h3 id="Using_models">Using models</h3>

<p>Once you've created a schema you can use it to create models. The model represents a collection of documents in the database that you can search, while the model's instances represent individual documents that you can save and retrieve.</p>

<p>We provide a brief overview below. For more information see: <a href="https://mongoosejs.com/docs/models.html">Models</a> (Mongoose docs).</p>

<h4 id="Creating_and_modifying_documents">Creating and modifying documents</h4>

<p>To create a record you can define an instance of the model and then call <code>save()</code>. The examples below assume SomeModel is a model (with a single field "name") that we have created from our schema.</p>

<pre class="brush: js">// Create an instance of model SomeModel
var awesome_instance = new SomeModel({ name: 'awesome' });

// Save the new model instance, passing a callback
awesome_instance.save(function (err) {
  if (err) return handleError(err);
  // saved!
});
</pre>

<p>Creation of records (along with updates, deletes, and queries) are asynchronous operations — you supply a callback that is called when the operation completes. The API uses the error-first argument convention, so the first argument for the callback will always be an error value (or null). If the API returns some result, this will be provided as the second argument.</p>

<p>You can also use <code>create()</code> to define the model instance at the same time as you save it. The callback will return an error for the first argument and the newly-created model instance for the second argument.</p>

<pre class="brush: js">SomeModel.create({ name: 'also_awesome' }, function (err, awesome_instance) {
  if (err) return handleError(err);
  // saved!
});</pre>

<p>Every model has an associated connection (this will be the default connection when you use <code>mongoose.model()</code>). You create a new connection and call <code>.model()</code> on it to create the documents on a different database.</p>

<p>You can access the fields in this new record using the dot syntax, and change the values. You have to call <code>save()</code> or <code>update()</code> to store modified values back to the database.</p>

<pre class="brush: js">// Access model field values using dot notation
console.log(awesome_instance.name); //should log 'also_awesome'

// Change record by modifying the fields, then calling save().
awesome_instance.name="New cool name";
awesome_instance.save(function (err) {
   if (err) return handleError(err); // saved!
});
</pre>

<h4 id="Searching_for_records">Searching for records</h4>

<p>You can search for records using query methods, specifying the query conditions as a JSON document. The code fragment below shows how you might find all athletes in a database that play tennis, returning just the fields for athlete <em>name</em> and <em>age</em>. Here we just specify one matching field (sport) but you can add more criteria, specify regular expression criteria, or remove the conditions altogether to return all athletes.</p>

<pre class="brush: js">var Athlete = mongoose.model('Athlete', yourSchema);

// find all athletes who play tennis, selecting the 'name' and 'age' fields
Athlete.find({ 'sport': 'Tennis' }, 'name age', function (err, athletes) {
  if (err) return handleError(err);
  // 'athletes' contains the list of athletes that match the criteria.
})</pre>

<p>If you specify a callback, as shown above, the query will execute immediately. The callback will be invoked when the search completes.</p>

<div class="notecard note">
  <p><strong>Note:</strong> All callbacks in Mongoose use the pattern <code>callback(error, result)</code>. If an error occurs executing the query, the <code>error</code> parameter will contain an error document and <code>result</code> will be null. If the query is successful, the <code>error</code> parameter will be null, and the <code>result</code> will be populated with the results of the query.</p>
</div>

<div class="notecard note">
  <p><strong>Note:</strong> It is important to remember that not finding any results is <strong>not an error</strong> for a search —but it may be a fail-case in the context of your application. If your application expects a search to find a value you can either check the result in the callback (<code>results==null</code>) or daisy chain the <a href="https://mongoosejs.com/docs/api.html#query_Query-orFail">orFail()</a> method on the query. </p>
</div>

<p>If you don't specify a callback then the API will return a variable of type <a href="https://mongoosejs.com/docs/api.html#query-js">Query</a>. You can use this query object to build up your query and then execute it (with a callback) later using the <code>exec()</code> method.</p>

<pre class="brush: js">// find all athletes that play tennis
var query = Athlete.find({ 'sport': 'Tennis' });

// selecting the 'name' and 'age' fields
query.select('name age');

// limit our results to 5 items
query.limit(5);

// sort by age
query.sort({ age: -1 });

// execute the query at a later time
query.exec(function (err, athletes) {
  if (err) return handleError(err);
  // athletes contains an ordered list of 5 athletes who play Tennis
})</pre>

<p>Above we've defined the query conditions in the <code>find()</code> method. We can also do this using a <code>where()</code> function, and we can chain all the parts of our query together using the dot operator (.) rather than adding them separately. The code fragment below is the same as our query above, with an additional condition for the age.</p>

<pre class="brush: js">Athlete.
  find().
  where('sport').equals('Tennis').
  where('age').gt(17).lt(50).  //Additional where query
  limit(5).
  sort({ age: -1 }).
  select('name age').
  exec(callback); // where callback is the name of our callback function.</pre>

<p>The <a href="https://mongoosejs.com/docs/api.html#query_Query-find">find()</a> method gets all matching records, but often you just want to get one match. The following methods query for a single record:</p>

<ul>
 <li><code><a href="https://mongoosejs.com/docs/api.html#model_Model.findById">findById()</a></code>: Finds the document with the specified <code>id</code> (every document has a unique <code>id</code>).</li>
 <li><code><a href="https://mongoosejs.com/docs/api.html#query_Query-findOne">findOne()</a></code>: Finds a single document that matches the specified criteria.</li>
 <li><code><a href="https://mongoosejs.com/docs/api.html#model_Model.findByIdAndRemove">findByIdAndRemove()</a></code>, <code><a href="https://mongoosejs.com/docs/api.html#model_Model.findByIdAndUpdate">findByIdAndUpdate()</a></code>, <code><a href="https://mongoosejs.com/docs/api.html#query_Query-findOneAndRemove">findOneAndRemove()</a></code>, <code><a href="https://mongoosejs.com/docs/api.html#query_Query-findOneAndUpdate">findOneAndUpdate()</a></code>: Finds a single document by <code>id</code> or criteria and either updates or removes it. These are useful convenience functions for updating and removing records.</li>
</ul>

<div class="notecard note">
  <p><strong>Note:</strong> There is also a <code><a href="https://mongoosejs.com/docs/api.html#model_Model.count">count()</a></code> method that you can use to get the number of items that match conditions. This is useful if you want to perform a count without actually fetching the records.</p>
</div>

<p>There is a lot more you can do with queries. For more information see: <a href="https://mongoosejs.com/docs/queries.html">Queries</a> (Mongoose docs).</p>

<h4 id="Working_with_related_documents_—_population">Working with related documents — population</h4>

<p>You can create references from one document/model instance to another using the <code>ObjectId</code> schema field, or from one document to many using an array of <code>ObjectIds</code>. The field stores the id of the related model. If you need the actual content of the associated document, you can use the <code><a href="https://mongoosejs.com/docs/api.html#query_Query-populate">populate()</a></code> method in a query to replace the id with the actual data.</p>

<p>For example, the following schema defines authors and stories.
Each author can have multiple stories, which we represent as an array of <code>ObjectId</code>.
Each story can have a single author.
The <code>ref</code> property tells the schema which model can be assigned to this field.</p>

<pre class="brush: js">var mongoose = require('mongoose')
  , Schema = mongoose.Schema

var authorSchema = Schema({
  name    : String,
  stories : [{ type: Schema.Types.ObjectId, ref: 'Story' }]
});

var storySchema = Schema({
  author : { type: Schema.Types.ObjectId, ref: 'Author' },
  title    : String
});

var Story  = mongoose.model('Story', storySchema);
var Author = mongoose.model('Author', authorSchema);</pre>

<p>We can save our references to the related document by assigning the <code>_id</code> value.
Below we create an author, then a story, and assign the author id to our story's author field.</p>

<pre class="brush: js">var bob = new Author({ name: 'Bob Smith' });

bob.save(function (err) {
  if (err) return handleError(err);

  //Bob now exists, so lets create a story
  var story = new Story({
    title: "Bob goes sledding",
    author: bob._id    // assign the _id from our author Bob. This ID is created by default!
  });

  story.save(function (err) {
    if (err) return handleError(err);
    // Bob now has his story
  });
});</pre>

<p>Our story document now has an author referenced by the author document's ID. In order to get the author information in the story results we use <code>populate()</code>, as shown below.</p>

<pre class="brush: js">Story
.findOne({ title: 'Bob goes sledding' })
.populate('author') //This populates the author id with actual author information!
.exec(function (err, story) {
  if (err) return handleError(err);
  console.log('The author is %s', story.author.name);
  // prints "The author is Bob Smith"
});</pre>

<div class="notecard note">
  <p><strong>Note:</strong> Astute readers will have noted that we added an author to our story, but we didn't do anything to add our story to our author's <code>stories</code> array. How then can we get all stories by a particular author? One way would be to add our story to the stories array, but this would result in us having two places where the information relating authors and stories needs to be maintained.</p>

<p>A better way is to get the <code>_id</code> of our <em>author</em>, then use <code>find()</code> to search for this in the author field across all stories.</p>

<pre class="brush: js">Story
.find({ author : bob._id })
.exec(function (err, stories) {
  if (err) return handleError(err);
  // returns all stories that have Bob's id as their author.
});
</pre>
</div>

<p>This is almost everything you need to know about working with related items <em>for this tutorial</em>. For more detailed information see <a href="https://mongoosejs.com/docs/populate.html">Population</a> (Mongoose docs).</p>

<h3 id="One_schemamodel_per_file">One schema/model per file</h3>

<p>While you can create schemas and models using any file structure you like, we highly recommend defining each model schema in its own module (file), then exporting the method to create the model.
This is shown below:</p>

<pre class="brush: js">// File: ./models/somemodel.js

//Require Mongoose
var mongoose = require('mongoose');

//Define a schema
var Schema = mongoose.Schema;

var SomeModelSchema = new Schema({
  a_string: String,
  a_date: Date,
});

//Export function to create "SomeModel" model class
module.exports = mongoose.model('SomeModel', SomeModelSchema );</pre>

<p>You can then require and use the model immediately in other files. Below we show how you might use it to get all instances of the model.</p>

<pre class="brush: js">//Create a SomeModel model just by requiring the module
var SomeModel = require('../models/somemodel')

// Use the SomeModel object (model) to find all SomeModel records
SomeModel.find(callback_function);</pre>

<h2 id="Setting_up_the_MongoDB_database">Setting up the MongoDB database</h2>

<p>Now that we understand something of what Mongoose can do and how we want to design our models, it's time to start work on the <em>LocalLibrary</em> website. The very first thing we want to do is set up a MongoDB database that we can use to store our library data.</p>

<p>For this tutorial, we're going to use the <a href="https://www.mongodb.com/cloud/atlas">MongoDB Atlas</a> free cloud-hosted <a href="https://www.mongodb.com/cloud/atlas/pricing">sandbox</a> database. This database tier is not considered suitable for production websites because it has no redundancy, but it is great for development and prototyping. We're using it here because it is free and easy to set up, and because MongoDB Atlas is a popular <em>database as a service</em> vendor that you might reasonably choose for your production database (other popular choices at the time of writing include <a href="https://www.compose.com/">Compose</a>, <a href="https://scalegrid.io/pricing.html">ScaleGrid</a> and <a href="https://www.objectrocket.com/">ObjectRocket</a>).</p>

<div class="notecard note">
  <p><strong>Note:</strong> If you prefer you can set up a MongoDb database locally by downloading and installing the <a href="https://www.mongodb.com/download-center/community">appropriate binaries for your system</a>. The rest of the instructions in this article would be similar, except for the database URL you would specify when connecting. Note, however, that the <a href="/en-US/docs/Learn/Server-side/Express_Nodejs/deployment">Express Tutorial Part 7: Deploying to Production</a> tutorial requires some form of remote database, since the free tier of the <a href="https://www.heroku.com/">Heroku</a> service does not provide persistent storage. It is therefore highly recommended to use <a href="https://www.mongodb.com/cloud/atlas">MongoDB Atlas</a>.</p>
</div>

<p>You will first need to <a href="https://www.mongodb.com/cloud/atlas/register">create an account</a> with MongoDB Atlas (this is free, and just requires that you enter basic contact details and acknowledge their terms of service). </p>

<p>After logging in, you'll be taken to the <a href="https://cloud.mongodb.com/v2">home</a> screen:</p>

<ol>
 <li>Click <strong>Build a Cluster</strong> button in the Clusters Overview section.<br>
  <img alt="Create a cluster on MongoDB Atlas." src="mongodb_atlas_-_createcluster.jpg"></li>
 <li>This will open the <em>Create New Cluster</em> screen.<br>
  <img alt="Choose a cloud provider when using MongoDB Atlas." src="mongodb_atlas_-_chooseproviderregion.jpg">
  <ul>
   <li>Select any provider from the <em>Cloud Provider &amp; Region</em> section. Different providers offer different regions.</li>
   <li>Select any region marked "FREE TIER AVAILABLE".</li>
   <li>Click the <strong>Create Cluster</strong> button (creation of the cluster will take some minutes).</li>
  </ul>
 </li>
 <li>
  <p>You will return to the <em>Cluster Overview</em> screen.<br>
   <img alt="Setup a collection on MongoDB Atlas." src="mongodb_atlas_-_createcollection.jpg"></p>

  <ul>
   <li>
    <p>Click the <strong>Collections</strong> button.</p>
   </li>
  </ul>
 </li>
 <li>This will open the <em>Collections</em> section.<br>
  <img alt="Create a database on MongoDB Atlas." src="mongodb_atlas_-_createdatabase2.png">
  <ul>
   <li>Click the <strong>Add My Own Data</strong> button.</li>
  </ul>
 </li>
 <li>This will open the <em>Create Database</em> screen.<br>
  <img alt="Details during database creation on MongoDB Atlas." src="mongodb_atlas_-_databasedetails.jpg">
  <ul>
   <li>Enter the name for the new database as <code>local_library</code>.</li>
   <li>Enter the name of the collection as <code>Collection0</code>.</li>
   <li>Click the <strong>Create</strong> button to create the database.</li>
  </ul>
 </li>
 <li>You will return to the Collection screen with your database created.<br>
  <img alt="Database creation confirmation on MongoDB Atlas." src="mongodb_atlas_-_databasecreated.jpg">
  <ul>
   <li>Click the <em>Overview</em> tab to return the cluster overview.</li>
  </ul>
 </li>
 <li>From the Cluster0 Overview screen click the <strong>Connect</strong> button.<br>
  <img alt="Configure a connection when after setting up a cluster in MongoDB Atlas." src="mongodb_atlas_-_connectbutton.jpg"></li>
 <li>This will open the Connect to Cluster screen.<br>
  <img alt="Choose a connection type when connecting with MongoDB Atlas." src="connect_to_cluster_allowanywhere.png">
  <ul>
   <li>Click the <strong>Allow Access from Anywhere</strong> button.<br>
    This will open a form with <code>0.0.0.0/0</code> pre-seeded for the IP Address. Click the <strong>Add IP Address</strong> button.
    <div class="notecard note">
        <p><strong>Note:</strong> It is a best practice to limit the IP addresses that can connect to your database and other resources. Here we allow a connection from anywhere because we don't know where the request will come from after deployment.</p>
    </div>
   </li>
   <li>Enter a username and password and click <strong>Create MongoDB User</strong> button.
    <div class="notecard note">
      <p><strong>Note:</strong> Avoid using special characters in your MongoDB user password as mongoose may not parse the connection string properly.</p>
    </div>
   </li>
   <li>If you have completed the 2 previous steps, the button <strong>Choose a connection method </strong>will turn green.</li>
   <li>Click the <strong>Choose a connection method</strong> button.</li>
  </ul>
 </li>
 <li>You should now be able to access the <em>Choose a connection</em> method tab.<br>
  <img alt="Choose a connection type when connecting with MongoDB Atlas." src="mongodb_atlas_-_chooseaconnectionmethod.jpg">
  <ul>
   <li>Click the <strong>Connect Your Application</strong> option.</li>
  </ul>
 </li>
 <li>This will open the <em>Connect</em> screen.<br>
  <img alt="Choose the Short SRV connection when setting up a connection on MongoDB Atalas." src="mongodb_atlas_-_connectforshortsrv_2020-03.png">
  <ul>
   <li>Click the <strong>Copy</strong> button to copy the connection string.</li>
   <li>Save this string somewhere safe.</li>
   <li>Update the password with your user's password.</li>
   <li>Replace test with <code>local_library</code>.</li>
  </ul>
 </li>
</ol>

<p>You have now created the database, and have a URL (with username and password) that can be used to access it. This will look something like: <code>mongodb+srv://your_user_name:your_password@cluster0.a9azn.mongodb.net/local_library?retryWrites=true</code></p>

<h2 id="Install_Mongoose">Install Mongoose</h2>

<p>Open a command prompt and navigate to the directory where you created your <a href="/en-US/docs/Learn/Server-side/Express_Nodejs/skeleton_website">skeleton Local Library website</a>. Enter the following command to install Mongoose (and its dependencies) and add it to your <strong>package.json</strong> file, unless you have already done so when reading the <a href="#installing_mongoose_and_mongodb">Mongoose Primer</a> above.</p>

<pre class="brush: bash">npm install mongoose
</pre>

<h2 id="Connect_to_MongoDB">Connect to MongoDB</h2>

<p>Open <strong>/app.js</strong> (in the root of your project) and copy the following text below where you declare the <em>Express application object</em> (after the line <code>var app = express();</code>). Replace the database url string ('<em>insert_your_database_url_here</em>') with the location URL representing your own database (i.e. using the information from <em>mongoDB Atlas</em>).</p>

<pre class="brush: js">//Set up mongoose connection
var mongoose = require('mongoose');
var mongoDB = '<em>insert_your_database_url_here</em>';
mongoose.connect(mongoDB, { useNewUrlParser: true , useUnifiedTopology: true});
var db = mongoose.connection;
db.on('error', console.error.bind(console, 'MongoDB connection error:'));</pre>

<p>As discussed <a href="#connecting_to_mongodb">in the Mongoose primer above</a>, this code creates the default connection to the database and binds to the error event (so that errors will be printed to the console). </p>

<h2 id="Defining_the_LocalLibrary_Schema">Defining the LocalLibrary Schema</h2>

<p>We will define a separate module for each model, as <a href="#one_schemamodel_per_file">discussed above</a>.
Start by creating a folder for our models in the project root (<strong>/models</strong>) and then create separate files for each of the models:</p>

<pre class="brush: plain">/express-locallibrary-tutorial  //the project root
  /models
    author.js
    book.js
    bookinstance.js
    genre.js
</pre>

<h3 id="Author_model">Author model</h3>

<p>Copy the <code>Author</code> schema code shown below and paste it into your <strong>./models/author.js</strong> file. The schema defines an author as having <code>String</code> SchemaTypes for the first and family names (required, with a maximum of 100 characters), and <code>Date</code> fields for the dates of birth and death.</p>

<pre class="brush: js">var mongoose = require('mongoose');

var Schema = mongoose.Schema;

var AuthorSchema = new Schema(
  {
    first_name: {type: String, required: true, maxLength: 100},
    family_name: {type: String, required: true, maxLength: 100},
    date_of_birth: {type: Date},
    date_of_death: {type: Date},
  }
);

// Virtual for author's full name
AuthorSchema
.virtual('name')
.get(function () {
  return this.family_name + ', ' + this.first_name;
});

// Virtual for author's lifespan
AuthorSchema.virtual('lifespan').get(function() {
  var lifetime_string = '';
  if (this.date_of_birth) {
    lifetime_string = DateTime.fromJSDate(this.date_of_birth).toLocaleString(DateTime.DATE_MED);
  }
  lifetime_string += ' - ';
  if (this.date_of_death) {
    lifetime_string += DateTime.fromJSDate(this.date_of_death).toLocaleString(DateTime.DATE_MED)
  }
  return lifetime_string;
});

// Virtual for author's URL
AuthorSchema
.virtual('url')
.get(function () {
  return '/catalog/author/' + this._id;
});

//Export model
module.exports = mongoose.model('Author', AuthorSchema);

</pre>

<p>We've also declared a <a href="#virtual_properties">virtual</a> for the AuthorSchema named "url" that returns the absolute URL required to get a particular instance of the model — we'll use the property in our templates whenever we need to get a link to a particular author.</p>

<div class="notecard note">
  <p><strong>Note:</strong> Declaring our URLs as a virtual in the schema is a good idea because then the URL for an item only ever needs to be changed in one place.<br>
 At this point, a link using this URL wouldn't work, because we haven't got any routes handling code for individual model instances. We'll set those up in a later article!</p>
</div>

<p>At the end of the module, we export the model.</p>

<h3 id="Book_model">Book model</h3>

<p>Copy the <code>Book</code> schema code shown below and paste it into your <strong>./models/book.js</strong> file.
Most of this is similar to the author model — we've declared a schema with a number of string fields and a virtual for getting the URL of specific book records, and we've exported the model.</p>

<pre class="brush: js">var mongoose = require('mongoose');

var Schema = mongoose.Schema;

var BookSchema = new Schema(
  {
    title: {type: String, required: true},
    author: {type: Schema.Types.ObjectId, ref: 'Author', required: true},
    summary: {type: String, required: true},
    isbn: {type: String, required: true},
    genre: [{type: Schema.Types.ObjectId, ref: 'Genre'}]
  }
);

// Virtual for book's URL
BookSchema
.virtual('url')
.get(function () {
  return '/catalog/book/' + this._id;
});

//Export model
module.exports = mongoose.model('Book', BookSchema);
</pre>

<p>The main difference here is that we've created two references to other models:</p>

<ul>
 <li>author is a reference to a single <code>Author</code> model object, and is required.</li>
 <li>genre is a reference to an array of <code>Genre</code> model objects. We haven't declared this object yet!</li>
</ul>

<h3 id="BookInstance_model">BookInstance model</h3>

<p>Finally, copy the <code>BookInstance</code> schema code shown below and paste it into your <strong>./models/bookinstance.js</strong> file.
The <code>BookInstance</code> represents a specific copy of a book that someone might borrow and includes information about whether the copy is available, on what date it is expected back, and "imprint" (or version) details.</p>

<pre class="brush: js">var mongoose = require('mongoose');

var Schema = mongoose.Schema;

var BookInstanceSchema = new Schema(
  {
    book: { type: Schema.Types.ObjectId, ref: 'Book', required: true }, //reference to the associated book
    imprint: {type: String, required: true},
    status: {type: String, required: true, enum: ['Available', 'Maintenance', 'Loaned', 'Reserved'], default: 'Maintenance'},
    due_back: {type: Date, default: Date.now}
  }
);

// Virtual for bookinstance's URL
BookInstanceSchema
.virtual('url')
.get(function () {
  return '/catalog/bookinstance/' + this._id;
});

//Export model
module.exports = mongoose.model('BookInstance', BookInstanceSchema);</pre>

<p>The new things we show here are the field options:</p>

<ul>
 <li><code>enum</code>: This allows us to set the allowed values of a string. In this case, we use it to specify the availability status of our books (using an enum means that we can prevent mis-spellings and arbitrary values for our status).</li>
 <li><code>default</code>: We use default to set the default status for newly created bookinstances to maintenance and the default <code>due_back</code> date to <code>now</code> (note how you can call the Date function when setting the date!).</li>
</ul>

<p>Everything else should be familiar from our previous schema.</p>

<h3 id="Genre_model_-_challenge!">Genre model - challenge!</h3>

<p>Open your <strong>./models/genre.js</strong> file and create a schema for storing genres (the category of book, e.g. whether it is fiction or non-fiction, romance or military history, etc).</p>

<p>The definition will be very similar to the other models:</p>

<ul>
 <li>The model should have a <code>String</code> SchemaType called <code>name</code> to describe the genre.</li>
 <li>This name should be required and have between 3 and 100 characters.</li>
 <li>Declare a <a href="#virtual_properties">virtual</a> for the genre's URL, named <code>url</code>.</li>
 <li>Export the model.</li>
</ul>

<h2 id="Testing_—_create_some_items">Testing — create some items</h2>

<p>That's it. We now have all models for the site set up!</p>

<p>In order to test the models (and to create some example books and other items that we can use in our next articles) we'll now run an <em>independent</em> script to create items of each type:</p>

<ol>
 <li>Download (or otherwise create) the file <a href="https://raw.githubusercontent.com/hamishwillee/express-locallibrary-tutorial/master/populatedb.js">populatedb.js</a> inside your <em>express-locallibrary-tutorial</em> directory (in the same level as <code>package.json</code>).

  <div class="notecard note">
    <p><strong>Note:</strong> You don't need to know how <a href="https://raw.githubusercontent.com/hamishwillee/express-locallibrary-tutorial/master/populatedb.js">populatedb.js</a> works; it just adds sample data into the database.</p>
  </div>
 </li>
 <li>Enter the following commands in the project root to install the <em>async</em> module that is required by the script (we'll discuss this in later tutorials, )
  <pre class="brush: bash">npm install async</pre>
 </li>
 <li>Run the script using node in your command prompt, passing in the URL of your <em>MongoDB</em> database (the same one you replaced the <em>insert_your_database_url_here</em> placeholder with, inside <code>app.js</code> earlier):
  <pre class="brush: bash">node populatedb &lt;your mongodb url&gt;</pre>
  <div class="notecard note">
    <p><strong>Note:</strong> On some operating systems/terminals, you may need to wrap the database URL inside double (") or single (') quotation marks.</p>
  </div>
 </li>
 <li>The script should run through to completion, displaying items as it creates them in the terminal.</li>
</ol>

<div class="notecard note">
  <p><strong>Note:</strong> Go to your database on mongoDB Atlas (in the <em>Collections</em> tab). You should now be able to drill down into individual collections of Books, Authors, Genres and BookInstances, and check out individual documents.</p>
</div>

<h2 id="Summary">Summary</h2>

<p>In this article, we've learned a bit about databases and ORMs on Node/Express, and a lot about how Mongoose schema and models are defined. We then used this information to design and implement <code>Book</code>, <code>BookInstance</code>, <code>Author</code> and <code>Genre</code> models for the <em>LocalLibrary</em> website.</p>

<p>Last of all, we tested our models by creating a number of instances (using a standalone script). In the next article we'll look at creating some pages to display these objects.</p>

<h2 id="See_also">See also</h2>

<ul>
 <li><a href="https://expressjs.com/en/guide/database-integration.html">Database integration</a> (Express docs)</li>
 <li><a href="https://mongoosejs.com/">Mongoose website</a> (Mongoose docs)</li>
 <li><a href="https://mongoosejs.com/docs/guide.html">Mongoose Guide</a> (Mongoose docs)</li>
 <li><a href="https://mongoosejs.com/docs/validation.html">Validation</a> (Mongoose docs)</li>
 <li><a href="https://mongoosejs.com/docs/schematypes.html">Schema Types</a> (Mongoose docs)</li>
 <li><a href="https://mongoosejs.com/docs/models.html">Models</a> (Mongoose docs)</li>
 <li><a href="https://mongoosejs.com/docs/queries.html">Queries</a> (Mongoose docs)</li>
 <li><a href="https://mongoosejs.com/docs/populate.html">Population</a> (Mongoose docs)</li>
</ul>

<p>{{PreviousMenuNext("Learn/Server-side/Express_Nodejs/skeleton_website", "Learn/Server-side/Express_Nodejs/routes", "Learn/Server-side/Express_Nodejs")}}</p>

<h2 id="In_this_module">In this module</h2>

<ul>
 <li><a href="/en-US/docs/Learn/Server-side/Express_Nodejs/Introduction">Express/Node introduction</a></li>
 <li><a href="/en-US/docs/Learn/Server-side/Express_Nodejs/development_environment">Setting up a Node (Express) development environment</a></li>
 <li><a href="/en-US/docs/Learn/Server-side/Express_Nodejs/Tutorial_local_library_website">Express Tutorial: The Local Library website</a></li>
 <li><a href="/en-US/docs/Learn/Server-side/Express_Nodejs/skeleton_website">Express Tutorial Part 2: Creating a skeleton website</a></li>
 <li><strong>Express Tutorial Part 3: Using a Database (with Mongoose)</strong></li>
 <li><a href="/en-US/docs/Learn/Server-side/Express_Nodejs/routes">Express Tutorial Part 4: Routes and controllers</a></li>
 <li><a href="/en-US/docs/Learn/Server-side/Express_Nodejs/Displaying_data">Express Tutorial Part 5: Displaying library data</a></li>
 <li><a href="/en-US/docs/Learn/Server-side/Express_Nodejs/forms">Express Tutorial Part 6: Working with forms</a></li>
 <li><a href="/en-US/docs/Learn/Server-side/Express_Nodejs/deployment">Express Tutorial Part 7: Deploying to production</a></li>
</ul>