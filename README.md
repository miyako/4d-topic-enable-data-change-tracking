![version](https://img.shields.io/badge/version-20%20R6%2B-E23089)
![platform](https://img.shields.io/static/v1?label=platform&message=mac-intel%20|%20mac-arm%20|%20win-64&color=blue)
[![license](https://img.shields.io/github/license/miyako/4d-topic-enable-data-change-tracking)](LICENSE)
![downloads](https://img.shields.io/github/downloads/miyako/4d-topic-enable-data-change-tracking/total)

## SQL vs mirroring vs DCT

there are several mechanisms built into 4D that allows the replication of records between databases.

* **Replication via SQL** is based on a proprietary SQL command that allows data to be replicated or synchronised between 4D databases. the feature can be activate per table. when activated, 3 virtual fields are added to the table which are only accessible via SQL. their values are stored outside the main database, in the files *.4DSyncHeader* and *.4DSyncData*. the delta between local and remote databases are managed by comparing the `__ROW_STAMP` virtual field for each table.
	* the remote database must be **4D Server**
	* the local database can be 4D Desktop or 4D Server 
	* object fields are not supported    
* **4DSYNC** is a URL reserved by the 4D Web Server that triggers a mechanism similar to SQL replication over HTTP. the behaviour on the server side is completely automatic. the data is sent to the server in XML format.
	* this feature is **deprecated**
	* the remote database must be **4D Web Server**
	* the local database can be any HTTP agent
	* object fields are not supported
	* this feature was especially designed for the iSort for iOS app
* **Mirroring** is an extension of the standard backup mechanism based on journal files that allows data to be replicated between 4D databases. unlike SQL replication or synchronisation, the complete change history is reproduced, not just the final record data. only 4D Server can create or integrate journal segment files. the delta between local and remote databases are managed by comparing the journal operation number of each database.
	* both databases must be **4D Server**
	* one must developer their own system for sending and receiving journal segment files
* Data Change Tracking is a sysmtem similar to SQL repliation or 4DSYNC but with the following advantages:
	* ORDA, not SQL
 	* native objects, not XML 
	* supports object fields
	* no external files such as *.4DSyncHeader* or *.4DSyncData*
  	* better access control

## Comparison

|commands|availability|documentation|
|-|:-:|-|
|[`REPLICATE`](https://doc.4d.com/4Dv20/4D/20/REPLICATE.300-6342149.en.html)<br />[`SYNCHRONIZE`](https://doc.4d.com/4Dv20/4D/20/SYNCHRONIZE.300-6342131.en.html)|12|[Replication via SQL](https://doc.4d.com/4Dv20/4D/20/Replication-via-SQL.300-6342092.en.html)|
|[`4DSYNC`](https://doc.4d.com/4Dv18/4D/18.4/Connection-Security.300-5232834.en.html)|12|[Allow database access through 4DSYNC URLs](https://developer.4d.com/docs/settings/web#allow-database-access-through-4dsync-urls)|
|[`New log file`](https://developer.4d.com/docs/commands/new-log-file)<br />[`INTEGRATE MIRROR LOG FILE`](https://developer.4d.com/docs/commands/integrate-mirror-log-file)|14|[Setting up a logical mirror](https://doc.4d.com/4Dv20/4D/20/Setting-up-a-logical-mirror.300-6330536.en.html)|
|[`ds.getGlobalStamp()`](https://developer.4d.com/docs/API/DataStoreClass#getglobalstamp)<br />[`ds.setGlobalStamp()`](https://developer.4d.com/docs/API/DataStoreClass#setglobalstamp)|20 R3|[Using the Global Stamp](https://developer.4d.com/docs/ORDA/global-stamp)|

## Evolution

|feature|availability|blog|project mode|
|-|:-:|-|:-:|
|ORDA|17|[How ORDA will change the way you work](https://blog.4d.com/how-orda-will-change-the-way-you-work/)||
|remote datastore|18|[Multiple 4D data sources, interested?](https://blog.4d.com/multiple-4d-data-sources-interested/)||
|classes|18 R3|[An intro to object-oriented programming in 4D: Classes](https://blog.4d.com/an-intro-to-object-oriented-programming-in-4d-classes/)|required|
|data model classes|18 R4|[Welcome to the world of ORDA classes](https://blog.4d.com/welcome-to-the-world-of-orda-classes/)||
|extend data model classes|18 R4|[ORDA Classes to handle your data model](https://blog.4d.com/orda-classes-to-handle-your-data-model/)|required|
|REST API scope|18 R5|[ORDA – Improve your API with function scope](https://blog.4d.com/orda-improve-your-api-with-function-scope/)|required|
|passwords stored in bcrypt|19 R3|[Bcrypt support for passwords](https://blog.4d.com/bcrypt-support-for-passwords/)||
|session permission|19 R8|[Filter Access to your Data with a Complete System of Permissions](https://blog.4d.com/filter-access-to-your-data-with-a-complete-system-of-permissions/)|required|
|data change tracking|20 R3|[Track data changes in your database](https://blog.4d.com/track-data-changes-in-your-database/)||
|open datastore behaviour change|20 R4|[ACI0104515](https://developer.4d.com/docs/Notes/updates#4d-20-r4) also LTS 20.3 and later||
|force login mode|20 R5|[Improved 4D Client Licenses Usage with Qodly Studio for 4D](https://blog.4d.com/improved-4d-client-licenses-usage-with-qodly-studio-for-4d/)|required|
|force login mode by default|20 R6|[Force Login Becomes Default for all REST Auth](https://blog.4d.com/force-login-becomes-default-for-all-rest-auth/)|required|

#### DCT server must be...
* 4D 20 R5 or above
* running on 4D Server
* project mode
  	
#### DCT synchronisation client must be...
* 4D 20 R3 or above

#### DCT replication client must be...
* 4D 18 or above

## References

* [Using the Global Stamp](https://developer.4d.com/docs/20-R7/ORDA/global-stamp)
* [Open datastore](https://developer.4d.com/docs/commands/open-datastore)
 
# Enable Data Change Tracking
presentation material for 4D Method User Group Meeting

 # Structure

<img src="https://github.com/user-attachments/assets/0e9f6cdf-28e3-494f-952b-06e1830df8bf" width=800 height=auto />

# [Phase 1](https://github.com/miyako/4d-topic-enable-data-change-tracking/releases/tag/Phase-1)

1. open structure file with 4D 20
2. check products, check clients
3. in structure editor, check contextual menu; no "enable data change tracking"
4. reopen with 20 R3+
5. in structure editor, check contextual menu; new item "enable data change tracking"
6. enable DCT for all 4 tables

<img src="https://github.com/user-attachments/assets/71a7d2cc-f399-4792-8a9d-afe3f10a6fdf" width=260 height=auto />

# [Phase 2](https://github.com/miyako/4d-topic-enable-data-change-tracking/releases/tag/Phase-2)

1. export to project
2. rename *.4DProject*
3. remove "(binary mode)" in *.4DCatalog* and settings > client-server > publication name
4. reopen with 20 R6+
5. check settings > web > access > "activate REST authentication through ds.authentify() function"

<img src="https://github.com/user-attachments/assets/3b77eca6-ce16-45cd-8943-d867b76bd637" width=800 height=auto />

<img src="https://github.com/user-attachments/assets/8b842b78-49d2-4b1e-9892-e9fa4771b73e" width=480 height=auto />

# Phase 3

1. intentionally break *roles.json*
2. restart interpreted
3. check *Roles_Errors.json*
4. restore *roles.json*
5. restart interpreted; *Roles_Errors.json* goes away
6. expose as REST server
7. create *On Server Startup*

```4d
ASSERT(Not(Folder(fk logs folder).file("Roles_Errors.json").exists))

var $server : 4D.WebServer
$server:=WEB Server

If (Not($server.isRunning))
	$status:=$server.start({HTTPPort: 80; HTTPEnabled: True})
	ASSERT($status.success)
End if 
```

# Phase 4

1. open with 20 R6+ server
2. connect with local client
3. create REST client with 2nd instance of 4D 20
4. connect to local datastore via REST

```4d
var $options : Object
$options:={hostname: "127.0.0.1:80"}

var $ds : 4D.DataStoreImplementation
$ds:=Open datastore($options; "local")
```

> notice no user name or password is required to connect. this is [force login mode](https://blog.4d.com/force-login-becomes-default-for-all-rest-auth/) which replaces *On REST Authentication*.

```4d
var $clients : 4D.EntitySelection
$clients:=$ds.Clients.all()
```

> an error is raised bacause a call to `ds.authentify()` is mandatory (force login mode).

5. create `ds.authentify` on the server.

```4d
Class extends DataStoreImplementation

exposed Function authentify($params : Object) : Object
	
	var $status : Object
	$status:={success: False}
	
	If ($params.secret="demo-demo-2025-0123")
		$status.success:=Session.setPrivileges(["DCT"])
	End if 
	
	return $status
```

> remember to add the `exposed` keyword. also the REST server must be restarted for the change to take effect.

6. add new `privilege` to *roles.json*

```json
"privileges": [
	{
		"privilege": "none"
	},
        {
		"privilege": "DCT"
        }
]
```

> "none" is a special [locking privilege](https://developer.4d.com/docs/ORDA/privileges#default-file) that has no permissions.

7. add `ds.authentify()` call to the REST client.

```4d
var $status : Object
//%W-550.2
$status:=$ds.authentify({secret: "demo-demo-2025-0123"})
//%W+550.2
```

> the `%W` compiler directive is used to [disable specific warnings locally](https://developer.4d.com/docs/Project/compiler#disabling-and-enabling-warnings-locally). this is because `.authentify()` is not a native member function of 4D.DataStoreImplementation. see also the blog post [Customize Global Warnings Generation](https://blog.4d.com/customize-global-warnings-generation/) 

> the REST client must also be restarted because the remote datastore proxy object `ds("local")` would be cached from the previous connection.

8. attempt to access data.

```4d
var $clients : 4D.EntitySelection
$clients:=$ds.Clients.all()
```

> an error is raised because access is not explicitly given in *roles.json*.

9. edit *roles.json*

```json
"allowed": [
	{
		"applyTo":"ds",
		"type": "datastore",
		"read": ["none"],
		"create": ["none"],
		"update": ["none"],
		"drop": ["none"],
		"describe": ["none"],
		"execute": ["none"],
		"promote": ["none"]
	},
	{
		"applyTo":"Clients",
		"type": "dataClass",
		"read": ["DCT"],
		"create": ["DCT"],
		"update": ["DCT"],
		"drop": ["DCT"],
		"describe": ["none"],
		"execute": ["none"],
		"promote": ["none"]
	},
	{
		"applyTo":"Invoices",
		"type": "dataClass",
		"read": ["DCT"],
		"create": ["DCT"],
		"update": ["DCT"],
		"drop": ["DCT"],
		"describe": ["none"],
		"execute": ["none"],
		"promote": ["none"]
	},
	{
		"applyTo":"Products",
		"type": "dataClass",
		"read": ["DCT"],
		"create": ["DCT"],
		"update": ["DCT"],
		"drop": ["DCT"],
		"describe": ["none"],
		"execute": ["none"],
		"promote": ["none"]
	},
	{
		"applyTo":"Invoice_Lines",
		"type": "dataClass",
		"read": ["DCT"],
		"create": ["DCT"],
		"update": ["DCT"],
		"drop": ["DCT"],
		"describe": ["none"],
		"execute": ["none"],
		"promote": ["none"]
	},
	{
		"applyTo":"__DeletedRecords",
		"type": "dataClass",
		"read": ["DCT"],
		"create": ["DCT"],
		"update": ["DCT"],
		"drop": ["DCT"],
		"describe": ["none"],
		"execute": ["none"],
		"promote": ["none"]
	}
]
```

10. test access to all tables

```4d
var $clients : 4D.EntitySelection
$clients:=$ds.Clients.all()

var $invoices : 4D.EntitySelection
$invoices:=$ds.Invoices.all()

var $products : 4D.EntitySelection
$products:=$ds.Products.all()

var $invoice_lines : 4D.EntitySelection
$invoice_lines:=$ds.Invoice_Lines.all()

var $__deletedrecords : 4D.EntitySelection
$__deletedrecords:=$ds.__DeletedRecords.all()
```

> this time it is not necessary to restart the REST client because the change is applied on the server side. only the REST server needs to be restarted.

## permissions, privileges, roles

* **permission** is the authority to perform specific actions on specific resources. basic CRUD actions (create, read, update, drop) each require specific permissions. there are 2 types of permissions (execute, promote) for code execution. there used to be a 7th type of permission (describe) for reading only the catalog information but this was deprecated in 20 R7.

* both a **privilege** and a **role** represents the possession of 0 or more permissions. the difference is that a privilege is the technical ability to exercise a a set of permissions whereas a role describes the typical user profile who needs the privileges to exercise a a set of permissions. 

* it is not mandatory to define roles; you can work only with privileges. a privilege can includes a group of other privileges. the [`Session.setPrivileges()`](https://developer.4d.com/docs/API/SessionClass#setprivileges) function accepts:

1. a single privilege name
2. a collection of privilege names
3. a single role
4. a collection of roles
5. any combination of the above

you may defines roles as user-friendly abstractions instead of referring to privileges directly. whether you use roles or not, a session is internally assigned a collection of privileges, not roles. 

# Phase 5

1. download [DCT](https://github.com/miyako/DCT) project and install as component
2. trace the `cs.DCT.DCT` class

```4d
var $DCT : cs.DCT.DCT

$DCT:=cs.DCT.DCT.new()

If ($DCT.connect({hostname: "localhost"; idleTimeout: 100}))
	
	var $ds : 4D.DataStoreImplementation
	$ds:=$DCT.ds
	
	var $status : Object
	//%W-550.2
	$status:=$ds.authentify({secret: "demo-demo-2025-0123"})
	//%W+550.2
	
	If ($status.success)
		
		$DCT.sync()
		
	End if 
	
End if
```

3. modify records locally
4. trace the `cs.DCT.DCT` class
5. modify records remotely
