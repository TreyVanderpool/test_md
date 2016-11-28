# Create Query Mode Definition

When zFAM instances are created they're all given a 4 character identifier like FA01. Using this you will need to create a document template in your CICS region to list the columns for the table. Each line each line must be the correct length with the exact name and value positions. If you're not familiar with document templates please work with your CICS systems group to have one defined.

Rules for the document template definition:
- Document template name must be the 4 character zFAM name followed by "FD", FA01FD.
- Each line must be 66 characters long with the last character being "|".
- There are 6 attributes for each column that need to be defined.
    - ID: Index ID
    - Col: Offset in record
    - Len: Data length
    - Type: Type of data
    - Sec: Security option for the column
    - Name: Column name
    
    Example definition:  
    `ID=001,Col=0000001,Len=000032,Type=C,Sec=01,Name=Id&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|`  
    `ID=002,Col=0000033,Len=000015,Type=C,Sec=01,Name=certID&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|`  
    `ID=000,Col=0000047,Len=000008,Type=C,Sec=01,Name=certTypeID&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|`  
    
    - ID: Index ID. 3 character numeric value and must be padded with leading zeros. This value defines the primary and secondary index fields. The first record must always have a value of "001". 
    - Col: Column offset. 7 character numeric value and must be padded with leading zeros.
    - Len: Column length. 6 character numeric value and must be padded with leading zeros. Depending on the type of field, character or numeric, the maximum value is 256K and 31 respectively.
    - Type: Column type. Single character 
