## Synopsis

zECS is a cloud enabled distributed NoSQL key/value store (KVS) file systemin the z/OS environment. Very high performing and high available system used to store text or binary content. Single instances can be shared by multiple clients or unique instances can be defined for each individual client. 

- L2 Distributed write thru cache to persistent disk
- Key/Value structure
  - Key can be from 1 to 255 bytes
  - Key cannot contain embedded spaces.
  - Key names are case sensitive, "Rangers" is different than "rangers".
  - Value can be from 1 byte to 3.2 Megabytes with options to store up to 1Gig per key.
  - Both text and binary data values are accepted.
- HTTP/HTTPS transmission depending on if data is needing to be secured in transit
- Transactional based system (geared for high volume I/O)
- Basic authentication access (RACF security) for CRUD operations
- ACID compliant (Atomic, Consistent, Isolation, Durable)
- RESTful service supporting:
  - GET:    Retrieve key/value
  - POST:   Adds key/value pair to instance, fails with 409 on duplicate entries
  - PUT:    Updates key/value pair in instance, fails with 204 when key is not found
  - DELETE: Delete a key/value from the instance
- Built-in expiration process.
- Multiple modes of operation:
  - Basic Mode: Simple key/value pair
  - Query/zQL Mode: Functions a little like a DBMS with SELECT/INSERT/UPDATE/DELETE commands.
- Six Sigma Availablility:
  - Active/Single (High Availability at a single data center)
  - Active/Standby (High Availability across multiple data centers)
  - Active/Active (Continuous Availability across multiple data centers)
  
As part of the product there is a built-in expiration process that runs automatically in the background. Refer to the installation instructions on setting up zFAM instances. Expiration process continually scans the zFAM data looking for keys that have expired and removes them. There are no additional web service calls required to initiate or trigger this component. Based on max time to live values, keys will never live more than 24 hours.

Note: The keys are actually stored in an indexed VSAM file so the keys are stored in sorted ascending order. This allows for the special features to query multiple keys and values in ascending or descending order.


## HTTP Status Codes
- 204 01-002 xxxxxxxx GET Primary key record not found
- 204 01-003 RangeBegin Key provided for DELETE not found.
- 204 01-004 xxxxxxxx Record not found on zFAM table.
- 204 01-020 xxxxxxxx Record not found on Primary Column Index
- 204 01-021 xxxxxxxx Record not found on Secondary Column Index
- 204 02-002 xxxxxxxx DELETE Primary key record not found
- 204 03-002 xxxxxxxx PUT Primary key record not found
- 204 03-020 xxxxxxxx Record not found while processing spanned segments
- 204 04-020 xxxxxxxx Error on segment array scan.
- 204 05-020 xxxxxxxx Record not found while accessing zQL xxxxxxx fields
- 204 07-021 xxxxxxxx Error on Segment Array Reset.
- 206 01-003 Maximum delete of 1000 records completed successfully.
- 206 01-007 Partial results due to buffer full condition.
- 400 01-001 Invalid HTTP method detected during security authorization
- 400 01-002 WEB RECEIVE error
- 400 01-010 Primary Key is not provided and zFAM-UID header is not present.
- 400 01-020 Primary key not present in WHERE statement on SELECT command.
- 400 01-030 Primary key not provided on zQL UPDATE (PUT).
- 400 01-040 Primary key not provided on zQL DELETE.
- 400 02-001 Invalid HTTP method detected during Basic Mode FAxxFD process
- 400 02-002 Invalid URI format. Must have seven slashes.
- 400 02-010 Primary Key and zFAM-UID are present, but zFAM-Concat is omitted.
- 400 03-002 URI Key exceeds 255 byte maximum
- 400 03-010 Primary Key and zFAM-Concat are present, but zFAM-UID is omitted.
- 400 04-001 Invalid HTTP method detected during Query Mode process.
- 400 04-002 URI Key missing on GET request
- 400 04-010 zFAM-UID and zFAM-Concat are present, but Primary key is omitted.
- 400 05-001 Invalid HTTP method detected during Query Mode security process.
- 400 05-002 URI Key missing on PUT request
- 400 05-010 Primary key and zUID length exceeds FAxxFD defined key length
- 400 06-001 Query Mode syntax error while parsing INSERT command.
- 400 06-002 URI Key missing on DELETE request
- 400 06-010 Primary key length exceeds FAxxFD defined key length
- 400 07-001 Query Mode syntax error while parsing SELECT command.
- 400 07-002 URI Key missing on POST request
- 400 07-010 zUID exceeds FAxxFD defined key length
- 400 08-001 Query Mode syntax error while parsing UPDATE command.
- 400 08-002 DEFINE Named Counter for Modulo failed.
- 400 09-001 Query Mode syntax error while parsing DELETE command.
- 400 09-002 zFAM task abended during I/O processing
- 400 10-001 Invalid query string on POST/PUT request DFHCOMMAREA parser.
- 400 10-002 zFAM-RangeBegin or zFAM-RangeEnd specified for DELETE request.
- 400 11-002 zFAM-RangeBegin is less than zFAM-RangeEnd for DELETE request.
- 401 01-001 Basic Authentication credentials not present in HTTPS request
- 401 02-001 Basic Authentication credentials invalid in HTTPS request
- 401 03-001 RACF rejected UserID/Password provided in the HTTPS header
- 401 04-001 HTTP/GET request received.  Basic Mode Read Only is disabled
- 401 05-001 HTTP/PUT, POST, DELETE not allowed in Basic Mode
- 401 06-001 HTTPS Basic Mode. UserID not found in FAxxSD security table
- 401 07-001 HTTPS Basic Mode xxxxxx UserID not allowed xxxxxxx access
- 401 11-001 HTTP/GET request received.  Query Mode Read Only is disabled
- 401 12-001 HTTP/PUT, POST, DELETE not allowed in Query Mode
- 401 13-001 HTTPS Query Mode.  UserID not found in FAxxSD security table
- 401 14-001 HTTPS Query Mode.  UserID not authorized for requested field
- 403 01-001 Query Mode request failed due to FAxxFD table not defined.
- 405 01-001 Invalid Query Mode command on xxxxxx method. Must be an xxxxxx.
- 409 01-002 xxxxxxxx GET row-level lock failed.  Record already locked
- 409 01-010 xxxxxxxx Duplicate record when inserting a record.
- 409 02-002 xxxxxxxx POST/WRITE duplicate record for primary key
- 409 02-010 xxxxxxxx Duplicate record when inserting a record.
- 409 03-002 xxxxxxxx PUT rejected.  Request Lock ID not active
- 411 01-001 Zero length on WEB RECEIVE command during PUT/POST process.
- 412 01-001 xxxxxxxxxxxxxxxx field not found while parsing xxxxxxx request.
- 412 01-010 xxxxxxxxxxxxxxxx field defined with invalid field type.
- 413 01-001 Maximum RECEIVE length exceeded during PUT/POST process.
- 413 01-002 WEB RECEIVE length exceeded 3.2MB limit
- 413 02-002 LOB Append exceeds the 2GB limit
- 414 01-001 Query string maximum length exceeded.
- 414 02-001 Path maximum length exceeded.
- 414 03-001 Maximum fields (256) exceeded during xxxxxx (xxxxxx) process.
- 500 01-001 pppppppp Error when attempting to XCTL for Basic Mode request.
- 503 01-004 xxxxxxxx DFHCOMMAREA is zero
- 507 01-002 xxxxxxxx GET/READ Primary key error
- 507 01-004 xxxxxxxx Error accessing zFAM table.
- 507 01-010 xxxxxxxx File is disabled.  INSERT request failed.
- 507 01-021 xxxxxxxx STARTBR error on Secondary Column Index
- 507 02-002 xxxxxxxx GET/READ references an invalid key on FAxxFILE
- 507 02-010 xxxxxxxx File is not defined/found. INSERT request failed.
- 507 02-021 xxxxxxxx READNEXT error on Secondary Column Index
- 507 03-002 xxxxxxxx GET/READ internal key error
- 507 03-021 xxxxxxxx error on Primary file store
- 507 04-010 xxxxxxxx NOTOPEN condition. INSERT request failed.
- 507 04-021 xxxxxxxx Column Index file not found in Parser Array
- 507 05-002 xxxxxxxx POST/WRITE primary key error
- 507 05-010 xxxxxxxx INSERT request failed
- 507 05-021 xxxxxxxx Secondary Column Index not found in CI Array
- 507 06-002 xxxxxxxx POST/WRITE internal key error
- 507 07-002 xxxxxxxx PUT/READ UPDATE Primary key error
- 507 08-002 xxxxxxxx PUT/REWRITE Primary key error
- 507 09-002 xxxxxxxx PUT/WRITE internal key error
- 507 10-002 xxxxxxxx GET/READ  internal key error
- 507 11-002 xxxxxxxx PUT/READ Primary key error during LOB Append.
- 507 13-002 xxxxxxxx PUT/WRITE Primary key error during LOB Append.
- 507 03-010 xxxxxxxx NOSPACE in file.  INSERT request failed.
    

## Installation

Refer to the [installation instructions](https://github.com/wamasoz/ECS/blob/master/readme_install.md) for complete setup in the z/OS environment.



## API Reference

### Basic Mode Query string parameters:
The query parameters are part of the URI and used to request additional features of the zFAM product. They are defined as name=value pairs separated by comma's.
    
- GET - http://hostname:port@path@@key@
    - rows=9999
    Optional
    Method: GET
    Requests from 1 to 9999 key/values be returned in a single request. This return body is heavily impacted by the **_keysonly_** and **_delim_** query parameters. See examples below how the body changes using the different parameters.
    Its possible not to receive the full number of rows as requested. This parameter can be stopped early with the custom **zFAM-RangeEnd** header which sets the end terminating string value for the key to stop at. Or if the zFAM instance reached the end of the key list.
    
    - keysonly
    Optional
    Method: GET
    There is no value associated with this parameter and it requests to only retrieve the key values. Primarily used with the **rows** parameter to retrieve a list of keys in the zFAM instance.

    - delim={char}
    Optional
    Method: GET
    This parameter works with the **rows** parameter to specify a single byte delimiter character to be placed between each of the key/value sets returned with multiple rows. See the examples below on how the body format changes when using this option.
   
    - eq/ge/gt/le/lt
    Optional
    Method: GET
    Special read options to drive the flow of the request. The default value is **eq**. Examples below show how to use these parameters to read multiple key/value pairs with a single request.
    - eq: Read "equal to", mutually exclusive with **rows** parameter.
    - ge: Read "greater than equal to"
    - gt: Read "greater than"
    - le: Read "less than equal to"
    - lt: Read "less than"
    
**Note:** Each of the GET query string parameters drive up to three different response bodies. See the Examples section for details.


- POST - http://hostname:port@path@@key@
	Requires a body containing the data to save under specified key.
    - ttl=99999
        Optional
        Method: POST/PUT
        Specifies time to live in days for each key. It's a numeric value between 1 and 36500 (100 years) days. If not specified it defaults 2555 days (7 year). The built-in background expiration process automatically cleans up expired keys. 
	
- PUT - http://hostname:port@path@@key@
	Requires a body containing the data to save under specified key.

- DELETE - http://hostname:port@path@@key@
	No body. The key is removed from the instance.



### Query Mode Query string parameters:
Query mode parameters are limited to two values, one to turn on query mode and the other are the selection criteria. The INSERT and UPDATE functions are posted in the body of the requests.

- zQL
    Required
    Method: GET/POST/PUT/DELETE
    This parameter specifies Query Mode!
    
- SELECT
    The select option functions much like an SQL statement where you can request specific columns and a where predicate. It's limited to a single zFAM instance with no joins to other instances and limited operator functions.
    Refer to the "Query Mode command syntax" section below for syntax.
  

### HTTP Headers
- Content-Type: {content-type}
	This is a standard HTTP header but drives some functionality by the service.
	When a record being stored or retrieved is not to be translated between EBCDIC and ASCII, set the content type to anything other than text/*. When a record being stored or retrieved is to be translated between EBCDIC and ASCII (text information accessible to all platforms), set the content type to text/* (text/anything). All content type of text/* will be translated between EBCDIC and ASCII on GET, POST, and PUT requests.
	
- Authorization: {Basic Auth Mode}
	This is a standard HTTP header and used with the HTTPS requests. Standard basic mode authorization syntax where the RACF user and password are encoded base64 values.
	`Authorization: Basic BASE64ENCODEDUSERANDPASS==`

- zFAM-Modulo: 99
	Optional
	Valid with POST & PUT methods
	Mode: Basic and Query
	Used with the zFAM-UID header on both basic and query mode to automatically generate a 37 character UID on inserts. It automatically creates a 4 byte numeric starting key when storing data into the zFAM system. The value sent can be from 01 to 99 and will auto-increment the key data when posted, so if 99 is always sent, the first modulo will be 0001/, the second will be 0002/, and so on until 0099/, then it rolls back to 0001/ on the next POST/PUT.
	New key generated and returned in the HTTP status field is {4_char_modulo_nbr/32_char_UID}
	0001/2e7d0f1f00d1aa9261969bc90d306f23
	0002/2e7d0f2f00d1aa926b0d309a08ad1dea
	
- zFAM-UID: yes
	Optional
	Valid with POST/PUT methods
	Mode: Basic and Query
	This feature only works with the zFAM-Modulo header and instructs the service to generate a 32 character UID as part of the key. The only valid value is "yes".

- zFAM-RangeBegin: {string_value}
	Optional
	Valid with DELETE method
	Mode: Basic and Query
	Used with the DELETE method to control a range of values to be deleted. This sets the starting string value for the range of keys to be deleted. The maximum number of records deleted in a single request is 1,000. The maximum string length is 255 characters with no embedded spaces.

- zFAM-RangeEnd: {string_value}
	Optional
	Valid with GET/DELETE methods
	Mode: Basic only
	Used with the GET and DELETE methods to stop processing of keys when it reaches this key value. This defines the ending string value to stop processing at. The maximum number of records deleted in a single request is 1,000. The maximum string length is 255 characters with no embedded spaces.

- zFAM-LastKey: {string_value}
	Optional
	Valid with GET method
	Mode: Basic only
	This is a returned header value indicating the last key value returned by the service. This is primarily used with the **rows** query string so you can use this key to issue a GET with **_gt_** option to read next set of keys.

- zFAM-LOB: yes
	Optional
	Valid with POST & PUT methods
	Mode: Basic only

- zFAM-Append: yes
	Optional
	Valid with POST & PUT methods
	Mode: Basic only

## Query Mode command syntax:

- SELECT,|**_fields_**|,|**_where_**|,|**_options_**|
    The select option functions much like an SQL statement where you can request specific columns and a where predicate. It's limited to a single zFAM instance and limited operator functions.
  
  - **fields** construct
    (FIELDS(**_col_name_**),(**_col_name_**),...)
    This requests the column names to retrieve.
  - **where** construct
    (WHERE(**_col_name_{oper}_value_**),(**_col_name_oper_value_**),...)
    **_{oper}_** value can be = (equal), > (greater than) or + (greater than equal to).
    Column names must match existing zFAM definition. The first column **_must_** be either the primary key or one of the secondary indexed columns otherwise it will post status code 400.
    Quotes are not needed around string values and are considered part of the value. Embedded spaces are allowed.
  - **options** construct
    (OPTIONS(ROWS=9999))
    This is optional and provides a way to limit the number of records returned by the service. The valid values are 1 to 9999.
    
    ####Examples:
    Small zFAM query mode instance that contains the following attributes:
    - org_type: team originization type, "mlb", "nfl", "nba"
    - team_name: name of the team
    - players: number of players on the team
    - salaries: total team salary
    
    List all teams for the "mlb":
    ?zQL,SELECT,(FIELDS(org_type),(team)),(WHERE(org_type=mlb))
    
    List all teams, number of players and team salaries for the "nfl":
    ?zQL,SELECT,(FIELDS(team),(players),(salaries)),(WHERE((org_type=nfl))
    
    List 10 of the "nba" teams:
    ?zQL,SELECT,(FIELDS(team),(players),(salaries)),(OPTIONS(ROWS=10))
    
    
- INSERT,|**_fields_**|
    Inserts a single record into a zFAM instance. Insert command is only valid with the POST method for the service.
    
  - **fields** construct
    (FIELDS(**_col_name=value_**),(**_col_name=value_**),...)
    
- UPDATE,|**_fields_**|,|**_where_**|
    Updates a single record in a zFAM instance. Update command is only valid with the PUT method for the service.
    
  - **fields** construct
    (FIELDS(**_col_name=value_**),(**_col_name=value_**),...)

  - **where** construct
    (WHERE(**_col_name=value_**),(**_col_name=value_**),...)
    Column names must match existing zFAM definition. The first column **_must_** be either the primary key or one of the secondary indexed columns otherwise it will post status code 400.
    Quotes are not needed around string values and are considered part of the value. Embedded spaces are allowed.


## Code Examples
Variables used in these examples:
- **_hostname:port_** values are all site specific
- **_@path@_** is defined by the team that sets up the zUID service
- **_@key@_** is 1 to 255 character value that represents the key

### Example URL calls:
In the following examples we'll assume there are the following keys in the zFAM instance for the Basic Mode.

| Key | Value |
| --- | --- |
| mlb_angels | { "name" : "Los Angeles Angles", "players" : 26 } |
| mlb_astros | { "name" : "Houston Astros", "players" : 26 } |
| mlb_athletics | { "name" : "Oakland Athletics", "players" : 26 } |
| mlb_blue_jays | { "name" : "Toronto Blue Jays", "players" : 26 } |
| mlb_braves | { "name" : "Atlanta Braves", "players" : 26 } |
| mlb_brewers | { "name" : "Milwaukee Brewers", "players" : 26 } |
| mlb_cardinals | { "name" : "St.Louis Cardinals", "players" : 26 } |
| mlb_cubs | { "name" : "Chicago Cubs", "players" : 26 } |
| mlb_diamondbacks | { "name" : "Arizona Diamondbacks", "players" : 26 } |
| mlb_dodgers | { "name" : "Los Angeles Dodgers", "players" : 26 } |
| mlb_giants | { "name" : "San Francisco Giants", "players" : 26 } |
| mlb_indians | { "name" : "Cleveland Indians", "players" : 26 } |
| mlb_mariners | { "name" : "Seattle Mariners", "players" : 26 } |
| mlb_marlins | { "name" : "Florida Marlins", "players" : 26 } |
| mlb_mets | { "name" : "New York Mets", "players" : 26 } |
| mlb_nationals | { "name" : "Washington Nationals", "players" : 26 } |
| mlb_orioles | { "name" : "Baltimore Orioles", "players" : 26 } |
| mlb_padres | { "name" : "San Diego Padres", "players" : 26 } |
| mlb_phillies | { "name" : "Philadelphia Phillies", "players" : 26 } |
| mlb_pirates | { "name" : "Pittsburgh Pirates", "players" : 26 } |
| mlb_rangers | { "name" : "Texas Rangers", "players" : 26 } |
| mlb_rays | { "name" : "Tampa Bay Rays", "players" : 26 } |
| mlb_red_sox | { "name" : "Boston Red Sox", "players" : 26 } |
| mlb_reds | { "name" : "Cincinnati Reds", "players" : 26 } |
| mlb_rockies | { "name" : "Colorado Rockies", "players" : 26 } |
| mlb_royals | { "name" : "Kansas City Royals", "players" : 26 } |
| mlb_tigers | { "name" : "Detroit Tigers", "players" : 26 } |
| mlb_twins | { "name" : "Minnesota Twins", "players" : 26 } |
| mlb_white_sox | { "name" : "Chicago White Sox", "players" : 26 } |
| mlb_yankees | { "name" : "New York Yankees", "players" : 26 } |
| nfl_bengals | { "name" : "Cincinnati Bengals", "players" : 53 } |
| nfl_bills | { "name" : "Buffalo Bills", "players" : 53 } |


- GET - http://hostname:port@path@@key@
	No body.

	1. Retrieve the value for a single key, "mlb_rangers".
	http://hostname:port**_@path@mlb\_rangers_**
	
	HTTP Code: 200
	HTTP Text: Ok
	Body: { "name" : "Texas Rangers", "players" : 26 }
	
	2. Retrieve a list of the first 3 "mlb" teams. Use the **_ge_** and **_rows_** query parameters. The **ge** query parameter asks the service to return rows "greater than or equal to" the key value and **rows** indicates how many rows to return with this request.
	Two header values will be returned showing the number of rows retrieved and the last key in the list. The HTTP status is also set to the last key in the list.
	The body format changes to reflect all the keys and values returned by the request. Four values are repeated for each record. There are no quotes around the key or value strings. I prefer this method since it reports the true length of each key and value so it's easily parsed by lengths and don't have to worry about the data values conflicting with the delimiter character.
	- 3 character numeric value representing length of the key.
	- key, 1 to 255 byte value.
	- 7 character numeric value representing length of the value.
	- value, 1 to 3.2MB value.
	http://hostname:port**_@path@mlb?ge,rows=3_**
	
	HTTP Code: 200
	HTTP Text: mlb\_dodgers
	Body:	**010**mlb\_angels**0000049**{ "name" : "Los Angeles Angles", "players" : 26 }**010**mlb\_astros**0000045**{ "name" : "Houston Astros", "players" : 26 }**013**mlb\_athletics**0000048**{ "name" : "Oakland Athletics", "players" : 26 }
	Response Headers:
		zFAM-Rows: 3
		zFAM-LastKey: mlb\_dodgers
	
	3. Issue the same request as item #2 except use the **delim** query parameter. This additional parameter will change the body format and use the single byte delimiter character to delimit the data.
	Two header values will be returned showing the number of rows retrieved and the last key in the list. The HTTP status is also set to the last key in the list.
	The body format changes to reflect all the keys and values delimited by the **delim** character. Again, there are no quotes around the key or value strings.
	http://hostname:port**_@path@mlb?ge,rows=3,delim=|_**
	
	HTTP Code: 200
	HTTP Text: mlb\_dodgers
	Body:	mlb\_angels|{ "name" : "Los Angeles Angles", "players" : 26 }|mlb\_astros|{ "name" : "Houston Astros", "players" : 26 }|mlb\_athletics|{ "name" : "Oakland Athletics", "players" : 26 }
	Returned Headers:
		zFAM-Rows: 3
		zFAM-LastKey: mlb\_dodgers
		
	4. Let's query all the **mlb** teams and use the **zFAM-RangeEnd** custom header to stop the query when the key changes from **mlb**. We will increase the **rows** value cause we're not sure how many are in the list and let the **zFAM-RangeEnd** header terminate the list. The **zFAM-Rows** response header will return the number of key/values returned in the body.
	http://hostname:port**_@path@mlb?ge,rows=999,delim=|_**
	Request Headers:
		zFAM-RangeEnd: mlb
	
	HTTP Code: 200
	HTTP Text: mlb\_yankees
	Body:	mlb\_angels|{ "name" : "Los Angeles Angles", "players" : 26 }|mlb\_astros|{ "name" : "Houston Astros", "players" : 26 }|mlb\_athletics|{ "name" : "Oakland Athletics", "players" : 26 }|..._(continues for all 30 teams)_
	Response Headers:
		zFAM-Rows: 30
		zFAM-LastKey: mlb\_yankees
		
	5. There will be times you just want to see the keys in your zFAM instance. We can do this with the **keysonly** query string parameter. This example will retrieve all the **mlb** keys only. Very similar to #4.
	http://hostname:port**_@path@mlb?ge,rows=999,keysonly_**
	Request Headers:
		zFAM-RangeEnd: mlb
	
	HTTP Code: 200
	HTTP Text: mlb\_yankees
	Body:	**010**mlb\_angels**010**mlb\_astros**013**mlb\_athletics..._(continues for all 30 teams)_
	Response Headers:
		zFAM-Rows: 30
		zFAM-LastKey: mlb\_yankees

- POST - http://hostname:port@path@@key@
	Requires a body containing the data to save under specified key.
	
- PUT - http://hostname:port@path@@key@
	Requires a body containing the data to save under specified key.

- DELETE - http://hostname:port@path@@key@
	No body. The key is removed from the instance.

### curl:
- Retrieve a value from the zECS instance.
```curl -X GET "http://hostname:port@path@@key@"```
	
- Put a new key value onto the zECS instance.
```curl -X POST "http://hostname:port@path@@key@" (value...)```
	
- Delete a key from the instance.
```curl -X DELETE "http://hostname:port@path@@key@"```
	
### COBOL CICS:
- Refer to [readme_COBOL_ex1](https://github.com/wamasoz/ECS/blob/master/readme_COBOL_ex1.md). Shows an insert, retrieve and delete for a key.
	
### standard browser (Chrome, IE, Safari):
- Can only submit GET requests from the browser. Will need other tools like ARC (Advanced REST Client), DCH REST Client, SOAP-UI or Postman to execute additional requests with POST/PUT/DELETE.
	
	Retrieve the value for specified key:
	http://hostname:port@path@/@key@
	
### Javascript:
- This small snippet of code will show how to write, read and delete a key/value pair to the zECS instance.
	
    ```javascript
	var svc = new XMLHttpRequest();
	var url = "http://hostname:port@path@";
	var key_name = "mlb_dodgers"
	var ecs_value = "{ \"name\":\"Los Angeles Dodgers\", \"players\":26, \"salaries\":248606156, \"won_world_series\" : true }";
	// Put a key value onto the zECS instance
	svc.open( "POST", url + key_name, false );
	svc.setRequestHeader("Content-type", "text/plain");
	svc.send( ecs_value );
	alert("POST Dodgers to zECS: " + "Status=" + svc.status + ":" + svc.statusText + "\nResponse=" + svc.responseText );
	// On return we get HTTP status:200 with status text:Ok
	
	// Retrieve the mlb_dodgers key from the zECS instance
	svc.open( "GET", url + key_name, false );
	svc.send( null );
	alert("GET Dodgers from zECS: " + "Status=" + svc.status + ":" + svc.statusText + "\nResponse=" + svc.responseText );
	// On return we get HTTP status:200 with status text:Ok
	// Return key value:{ "name":"Los Angeles Dodgers", "players":26, "salaries":248606156, "won_world_series" : true }
	
	// Delete the dodgers from the zECS instance.
	svc.open( "DELETE", url + key_name, false );
	svc.send( ecs_value );
	alert("DELETE Dodgers from zECS: " + "Status=" + svc.status + ":" + svc.statusText + "\nResponse=" + svc.responseText );
	// On return we get HTTP status:200 with status text:Ok
```


## Contributors

- **_Randy Frerking_**,	Walmart Stores Inc.
- **_Rich Jackson_**, Walmart Stores Inc.
- **_Michael Karagines_**, Walmart Stores Inc.

## License

A short snippet describing the license (MIT, Apache, etc.)


## Additional Info

Most of these configuration considerations are covered in a bit more detail in a couple of Redbooks published by IBM in collaboration with the 
Walmart SMEs. Please refer to these publications for additional information:

#### Creating IBM z/OS Cloud Services
http://www.redbooks.ibm.com/redbooks/pdfs/sg248324.pdf

#### How Walmart Became a Cloud Services Provider with IBM CICS 
http://www.redbooks.ibm.com/redbooks/pdfs/sg248347.pdf
