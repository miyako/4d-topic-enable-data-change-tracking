![version](https://img.shields.io/badge/version-20%20R6%2B-E23089)

|feature|availability|blog|project mode required|
|-|:-:|-|:-:|
|ORDA|17|||
|remote datastore|18|[Multiple 4D data sources, interested?](https://blog.4d.com/multiple-4d-data-sources-interested/)||
|classes|18 R3|[An intro to object-oriented programming in 4D: Classes](https://blog.4d.com/an-intro-to-object-oriented-programming-in-4d-classes/)|yes|
|data model classes|18 R4|[Welcome to the world of ORDA classes](https://blog.4d.com/welcome-to-the-world-of-orda-classes/)||
|extend data model classes|18 R4|[ORDA Classes to handle your data model](https://blog.4d.com/orda-classes-to-handle-your-data-model/)|yes|
|REST API scope|18 R5|[ORDA – Improve your API with function scope](https://blog.4d.com/orda-improve-your-api-with-function-scope/)|yes|
|passwords stored in bcrypt|19 R3|[Bcrypt support for passwords](https://blog.4d.com/bcrypt-support-for-passwords/)||
|session permission|19 R8|[Filter Access to your Data with a Complete System of Permissions](https://blog.4d.com/filter-access-to-your-data-with-a-complete-system-of-permissions/)|yes|
|data change tracking|20 R3|[Track data changes in your database](https://blog.4d.com/track-data-changes-in-your-database/)||
|open datastore behaviour change|20 R4|[ACI0104515](https://developer.4d.com/docs/Notes/updates#4d-20-r4) also LTS 20.3 and later||
|force login mode|20 R5|[Improved 4D Client Licenses Usage with Qodly Studio for 4D](https://blog.4d.com/improved-4d-client-licenses-usage-with-qodly-studio-for-4d/)|yes|
|force login mode by default|20 R6|[Force Login Becomes Default for all REST Auth](https://blog.4d.com/force-login-becomes-default-for-all-rest-auth/)|yes|

#### DCT server must be...
* 4D 20 R5 or above
* running on 4D Server
* project mode
  	
#### DCT synchronisation client must be...
* 4D 20 R3 or above

#### DCT replication client must be...
* 4D 18 or above
 
# Enable Data Change Tracking
presentation material for 4D Method User Group Meeting

 # Structure

<img src="https://github.com/user-attachments/assets/0e9f6cdf-28e3-494f-952b-06e1830df8bf" width=800 height=auto />

# Phase 1

1. open structure file with 4D 20
2. check products, check clients
3. in structure editor, check contextual menu; no "enable data change tracking"
4. reopen with 20 R3+
5. in structure editor, check contextual menu; new item "enable data change tracking"
6. enable DCT for all 4 tables

<img src="https://github.com/user-attachments/assets/71a7d2cc-f399-4792-8a9d-afe3f10a6fdf" width=260 height=auto />

# Phase 2

1. export to project
2. rename *.4DProject*
3. remove "(binary mode)" in *.4DCatalog* and settings > client-server > publication name
4. reopen with 20 R6+
5. check settings > web > access > "activate REST authentication through ds.authentify() function"

<img src="https://github.com/user-attachments/assets/3b77eca6-ce16-45cd-8943-d867b76bd637" width=800 height=auto />

<img src="https://github.com/user-attachments/assets/8b842b78-49d2-4b1e-9892-e9fa4771b73e" width=480 height=auto />

# Phase 3

1. intentionally braak *roles.json*
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
		$status.success:=Session.setPrivileges($params.privileges)
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

7. add `ds.authentify()` call to the REST client.

```4d
var $status : Object
//%W-550.2
$status:=$ds.authentify({secret: "demo-demo-2025-0123"; privileges: ["DCT"]})
//%W+550.2
```

> the REST client must also be restarted because `ds("local")` is cached from the previous connection.

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
