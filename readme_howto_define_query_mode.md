# Create Query Mode Definition

Shows how to define the columns for the zFAM Query Mode instances.

When zFAM instances are created they're all given a 4 character identifier like FA01. Using the zFAM identifier you will need to create a document template in your CICS region to list the columns for the table. Each line must be the correct length with the exact name and their values in the defined positions. If you're not familiar with document templates please work with your CICS systems group to have one defined.

Rules for the document template definition:
- Document template name must be the 4 character zFAM name followed by "FD", FA01FD. The template is looked up at execution time to get the column attributes.
- Each line must be 66 characters long with the last character being "|".
- There are 6 attributes for each column defined. The labels and values are case sensitive and must be in the proper columns.
    - **ID:** Index ID
    - **Col:** Offset in record
    - **Len:** Data length
    - **Type:** Type of data
    - **Sec:** Currently not used.
    - **Name:** Column name
    
    Example definition: {dots in the name are used for formatting and should be spaces}  
    `----+----1----+----2----+----3----+----4----+----5----+----6----+----7----+----8`
    `ID=001,Col=0000001,Len=000032,Type=C,Sec=01,Name=Id..............|`  
    `ID=002,Col=0000033,Len=000040,Type=C,Sec=01,Name=first_name......|`  
    `ID=000,Col=0000073,Len=000040,Type=C,Sec=01,Name=last_name.......|`  
    
    Explanation of each attribute:
    - **ID:** Defines the primary and secondary indexes. It's a 3 character numeric value and must be padded with leading zeros. The first line must be 001 for the primary index.
        - 000: No secondary index.
        - 001: Primary index column. **Must be the first column in the list.**
        - 002-999: Secondary index columns.
    - **Col:** Offset in the record for the given column. This is a 7 character numeric value that must be padded with leading zeros. You can have overlapping fields.
    - **Len:** Data length. 
        - Secondary indexed character fields can be 1 to 56. ID field > 0.
        - Non indexed character fields can be 1 to 262,144 (256K). ID field must be 000.
        - Numeric fields can be 1 to 31.
    - **Type:** Column type. Must be uppercase.
        - C for character fields.
        - N for numeric fields.
    - **Sec:** Currently not used.
    - **Name:** Column name. 
        - 1 to 16 character name that must be padded with spaces. 
        - Name restrictions are limited to the URL encoding. 
        - Each name must be unique.
