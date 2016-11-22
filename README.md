## Synopsis

zFAM is a cloud enabled distributed NoSQL key/value store (KVS) file systemin the z/OS environment. Very high performing and high available system used to store text or binary content. Single instances can be shared by multiple clients or unique instances can be defined for each individual client. 

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
  - Query/zQL Mode: Functions a little like a DBMS with SELECT/INSERT/UPDATE commands.
- Six Sigma Availablility:
  - Active/Single (High Availability at a single data center)
  - Active/Standby (High Availability across multiple data centers)
  - Active/Active (Continuous Availability across multiple data centers)
  
As part of the product there is a built-in expiration process that runs automatically in the background. Refer to the installation instructions on setting up zFAM instances. Expiration process continually scans the zFAM data looking for keys that have expired and removes them. There are no additional web service calls required to initiate or trigger this component. Based on max time to live values, keys will never live more than 24 hours.

Note: The keys are actually stored in an indexed VSAM file so the keys are stored in sorted ascending order. This allows for the special features to query multiple keys and values in ascending or descending order.


## API Reference

### URL/URI API Calls
- GET - _(Read values from zFAM)_  
    http://hostname:port@path@@key@?@query_string_parms@  
    No body
    
- POST - _(Add values to zFAM)_  
    http://hostname:port@path@@key@?@query_string_parms@  
    Requires a body containing the data to save under specified key.
    
- PUT - _(Update values in zFAM)_  
    http://hostname:port@path@@key@?@query_string_parms@  
    Requires a body containing the data to save under specified key.
    
- DELETE - _(Remove values from zFAM)_  
    http://hostname:port@path@@key@?@query_string_parms@  
    No body

###Basic Mode
- **Query string parameters (Basic Mode):**
    - GET
        - rows=9999
        - keysonly
        - delim=@char@
        **Note:** Each of the GET query string parameters drive up to three different response bodies. See the Examples section for details.
    - POST/PUT
        - ttl=99999

- **HTTP Headers (Basic Mode):**
    - GET
        - Content-Type: @content-type@
        - Authorization: @Basic Auth Mode@
        - zFAM-RangeEnd: @string_value@
        - zFAM-LastKey: @string_value@
    - POST
        - Content-Type: @content-type@
        - Authorization: @Basic Auth Mode@
        - zFAM-UID: yes
        - zFAM-Modulo: 99
    - PUT
        - Content-Type: @content-type@
        - Authorization: @Basic Auth Mode@
        - zFAM-LOB: yes
        - zFAM-Append: yes
    - DELETE
        - zFAM-RangeBegin: @string_value@
        - zFAM-RangeEnd: @string_value@

- **Definitions for each of the query strings and headers (Basic Mode):**
    - rows=9999  
        Optional  
        Method: GET  
        Valid Values: Numeric value between 1 and 9999  
        Requests from 1 to 9999 key/values be returned in a single request. This return body is heavily impacted by the **_keysonly_** and **_delim_** query parameters. See examples below how the body changes using the different parameters.  
        Its possible not to receive the full number of rows as requested. This parameter can be stopped early with the custom **zFAM-RangeEnd** header which sets the end terminating string value for the key to stop at. Or if the zFAM instance reached the end of the key list.  

    - keysonly  
        Optional  
        Method: GET  
        There is no value associated with this parameter and it requests to only retrieve the key values. Primarily used with the **rows** parameter to retrieve a list of keys in the zFAM instance.

    - delim=@char@  
        Optional  
        Method: GET  
        Valid Values: Single byte character, can be a URL encoded value like %047  
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

    - ttl=99999  
        Optional  
        Method: POST/PUT  
        Valid Values: Numeric value between 1 (1 day) and 36500 (100 years)  
        Specifies time to live in days for each key. It's a numeric value between 1 and 36500 (100 years) days. If not specified it defaults 2555 days (7 year). The built-in background expiration process automatically cleans up expired keys. 

    - Content-Type: {content-type}  
        Optional  
        Method: GET/POST/PUT  
        Mode: Basic and Query  
        Request and Response  
        Valid Values: Standard content type values. "text/\*" prefixed values control data conversion  
        This is a standard HTTP header but drives some functionality by the service.  
        When a record being stored or retrieved is not to be translated between EBCDIC and ASCII, set the content type to anything other than text/\*. When a record being stored or retrieved is to be translated between EBCDIC and ASCII (text information accessible to all platforms), set the content type to text/\* (text/anything). All content type of text/\* will be translated between EBCDIC and ASCII on GET, POST, and PUT requests.
	
    - Authorization: @Basic Auth Mode@  
        Optional  
        Method: GET/POST/PUT/DELETE  
        Mode: Basic and Query  
        Request Only  
        Valid Values: Standard basic authorization string, base 64 encoded user:password  
        This is a standard HTTP header and used with the HTTPS requests. Standard basic mode authorization syntax where the RACF user and password are encoded base64 values.  
        `Authorization: Basic BASE64ENCODEDUSERANDPASS==`

    - zFAM-Modulo: 99  
        Optional  
        Method: POST/PUT  
        Mode: Basic and Query  
        Request Only  
        Valid Values: Numeric value between 1 and 99  
        Used with the **zFAM-UID** header on both basic and query mode to automatically generate a 37 character UID on inserts. It automatically creates a 4 byte numeric starting key when storing data into the zFAM system. The value sent can be from 01 to 99 and will auto-increment the key data when posted, so if 99 is always sent, the first modulo will be 0001/, the second will be 0002/, and so on until 0099/, then it rolls back to 0001/ on the next POST/PUT.  
        The new key will also be returned in the HTTP status text.  
        Example of new key generated is {4_char_modulo_nbr/32_char_UID}  
        0001/2e7d0f1f00d1aa9261969bc90d306f23  
        0002/2e7d0f2f00d1aa926b0d309a08ad1dea
	
    - zFAM-UID: yes  
        Optional  
        Method: POST/PUT  
        Mode: Basic and Query  
        Request Only  
        This feature only works with the **zFAM-Modulo** header and instructs the service to generate a 32 character UID as part of the key. The only valid value is "yes".

    - zFAM-RangeBegin: @string_value@  
        Optional  
        Method: DELETE  
        Mode: Basic and Query  
        Request Only  
        Valid Values: Key string 1 to 255 characters, no embedded spaces  
        Used with the DELETE method to control a range of values to be deleted. Represents the 1 to 255 character key value to start deleting at. The maximum number of records deleted in a single request is 1,000.

    - zFAM-RangeEnd: @string_value@  
        Optional  
        Method: GET/DELETE  
        Mode: Basic only  
        Request Only  
        Valid Values: Key string 1 to 255 characters, no embedded spaces  
        Used with the GET and DELETE methods to stop processing of keys when it reaches this key value. This defines the ending key name to stop processing at. The maximum number of keys deleted in a single request is 1,000 and the max keys read depend on the buffer size and **rows** parameter.

    - zFAM-LastKey: @string_value@  
        Optional  
        Method: GET  
        Mode: Basic only  
        Response Only  
        Valid Values: Key string 1 to 255 characters  
        This is a returned header value indicating the last key value returned by the service. This is primarily used with the **rows** query string so you can use this key to issue a GET with **_gt_** option to read next set of keys.

    - zFAM-LOB: yes  
        Optional  
        Method: POST/PUT  
        Mode: Basic only
        Request Only  
        Valid Values: yes  
        This header works with the **zFAM-Append** header to extend an existing key value up to 2GB in size. The max payload size for Large Binary Objects is 200MB but you can send multiple PUT requests to append data.
        Say you want to put 550MB file in your zFAM instance:  
        - POST ... zFAM-LOB: yes   {200MB of data}  
        - PUT ... zFAM-LOB: yes, zFAM-Append: yes   {200MB of data}  
        - PUT ... zFAM-LOB: yes, zFAM-Append: yes   {150MB of data}

    - zFAM-Append: yes  
        Optional  
        Method: POST/PUT
        Mode: Basic only
        Request Only  
        Valid Values: yes  
        This header works solely with the **zFAM-LOB** header to append data to Large Binary Objects.

###Query Mode
Query mode functions more like a NoSQL system with the ability to query by column names and apply where predicates. Columns are defined in the installation documentation and can be updated after the zFAM instance has been created. To query the data you use the SELECT query mode command and the INSERT and UPDATE functions are posted in the body of POST/PUT requests.

- **Query string parameters (Query Mode):**  
    - zQL  
        Required  
        Method: GET/POST/PUT/DELETE  
        This parameter specifies Query Mode and required on all query mode requests.
    - SELECT  
        Optional  
        Method: GET
        The select option functions much like an SQL statement where you can request specific columns and a where predicate. It's limited to a single zFAM instance with no joins to other instances and limited operator functions.  
        Refer to the "Query Mode command syntax" section below for syntax.

- **HTTP Headers (Query Mode):**
    - GET
        - Content-Type: @content-type@
        - Authorization: @Basic Auth Mode@
    - POST
        - Content-Type: @content-type@
        - Authorization: @Basic Auth Mode@
    - PUT
        - Content-Type: @content-type@
        - Authorization: @Basic Auth Mode@
    - DELETE
        - Authorization: @Basic Auth Mode@

- **Query Mode Command Syntax:**

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
    
    - INSERT,|**_fields_**|  
        Inserts a single record into a zFAM instance. Insert command is only valid with the POST method for the service.  
        - **fields** construct  
        (FIELDS(**_col\_name=value_**),(**_col\_name=value_**),...{repeat}...)  
        
    - UPDATE,|**_fields_**|,|**_where_**|  
        Updates a single record in a zFAM instance. Update command is only valid with the PUT method for the service.  
        - **fields** construct  
        (FIELDS(**_col_name=value_**),(**_col_name=value_**),...)  
        - **where** construct  
        (WHERE(**_col_name=value_**),(**_col_name=value_**),...)  
        Column names must match existing zFAM definition. The first column **_must_** be either the primary key or one of the secondary indexed columns otherwise it will post status code 400.  
        Quotes are not needed around string values and are considered part of the value. Embedded spaces are allowed.
    
## Code Examples
- [Basic Mode](./readme_basic_example.md) Examples  
- [Query Mode](./readme_query_example.md) Examples

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
