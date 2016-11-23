# Create Query Mode Definition

When zFAM instances are created they're all given a 4 character identifier like FA01. Using this you will need to create a document template in your CICS region to list the columns for the table. Each line each line must be the correct length with the exact name and value positions. If you're not familiar with document templates please work with your CICS systems group to have one defined.

Rules for the document template definition:
- Document template name must be the 4 character zFAM name followed by "FD", FA01FD.
- Each line must be 66 characters long with the last character being "|".
- There are 6 attributes for each column that need to be defined.
    - ID: 3 character numeric value, must be padded with leading zeros. This value defines the primary and secondary index fields. The first record must always have a value of "001". 
    - Col: 7 character numeric value, must be padded with leading zeros. Value ranges from 1 to 256K.
