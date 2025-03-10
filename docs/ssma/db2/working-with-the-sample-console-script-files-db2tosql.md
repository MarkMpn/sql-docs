---
title: "Working with the Sample Console Script Files (DB2ToSQL)"
description: "Working with the Sample Console Script Files (DB2ToSQL)"
author: cpichuka
ms.author: cpichuka
ms.date: "01/19/2017"
ms.service: sql
ms.subservice: ssma
ms.topic: conceptual
ms.collection:
  - sql-migration-content
---
# Working with the Sample Console Script Files (DB2ToSQL)
A few sample files have been provided along with the product for the user reference and usage. This section describes the way to easily customize these scripts to fit the end user needs.  
  
## Sample Console Script Files  
The following sample console script files covering different scenarios have been provided for user reference:  
  
-   ServersConnectionFileSample.xml  
  
-   VariableValueFileSample.xml  
  
-   AssessmentReportGenerationSample.xml  
  
-   SqlStatementConversionSample.xml  
  
-   ConversionAndDataMigrationSample.xml  
  
1.  **ServersConnectionFileSample.xml:**  
  
    -   This sample gives the different modes of connection available to the source and target database and the user can select any mode as per the requirement. This sample contains the Server definitions.  
  
    -   The user can connect to the required database by simply changing the values to the required source and target server definitions. In the example provided all values have been provided as variable values which are available in the **VariableValueFileSample.xml**.  All other connection parameters can be removed from the user's working server connection file.  
  
    -   For more information on connecting to the source and target server, see [Creating the Server Connection Files &#40;DB2ToSQL&#41;](../../ssma/db2/creating-the-server-connection-files-db2tosql.md) .  
  
2.  **VariableValueFileSample.xml:** All variables that have been used in the sample console script files and `ServersConnectionFileSample.xml` have been collated in this file. To execute the sample console scripts the user has to simply replace the sample variable values with user defined ones and pass this file as an additional command line argument along with the script file.  
  
    For more information on Variable Value File, see [Creating Variable Value Files &#40;DB2ToSQL&#41;](../../ssma/db2/creating-variable-value-files-db2tosql.md).  
  
3.  **AssessmentReportGenerationSample.xml:** This sample enables the user to generate an xml assessment report which can be used by the user for analysis before he begins to convert and migrate data.  
  
    In the `generate-assessment-report` command the user has to mandatorily change the variable value (refer **VariableValueFileSample.xml**) in the `object-name` attribute to the database name being in use by the user. Depending on the kind of object specified, the `object-type` value will also have to be changed.  
  
    If the user has to assess multiple objects / databases he can specify multiple `metabase-object` nodes as illustrated in the `generate-assessment-report` command's Example 4 of the sample console script file.  
  
    For more information on generating reports, see [Generating Reports &#40;DB2ToSQL&#41;](../../ssma/db2/generating-reports-db2tosql.md).  
  
    **Notes:**  
  
    Ensure that the variable value file command line argument is passed to the console application and VariableValueFileSample.xml is updated with the user specified values.  
  
    Ensure that server connection file command line argument is passed to the console application and the ServersConnectionFileSample.xml is updated with correct server parameter values.  
  
4.  **SqlStatementConversionSample.xml:** This sample enables the user to generate the corresponding `t-sql` script for the source database `sql` command provided as input.  
  
    In the `convert-sql-statement` command the user has to mandatorily change the variable value (refer **VariableValueFileSample.xml**) in the `context` attribute to the database name that is being in use by the user. The user will also be required to change the `sql` attribute value to the source database `sql` command that he/she require to be converted.  
  
    The user can also provide sql files to be converted. This has been illustrated in the `convert-sql-statement` command's Example 4 of the sample console script file.  
  
    > [!NOTE]  
    > Ensure that the variable value file command line argument is passed to the console application and VariableValueFileSample.xml is updated with the user specified values.  
  
5.  **ConversionAndDataMigrationSample.xml:** This sample enables the user to perform an end to end migration from conversion to data migration. The list of mandatory attribute values that they will have to change is listed below:  
  
    |Command Name|Description|Attribute|  
    |----------------|---------------|-------------|  
    |`map-schema`|Schema mapping of source database to the target schema.|`source-schema:` Specifies the source database that requires to be converted.<br /><br />`sql-server-schema`: Specifies the target database that is to be migrated to|  
    |`convert-schema`|Performs schema conversion from source to the target schema.<br /><br />If the user has to assess multiple objects / databases he can specify multiple `metabase-object` nodes as illustrated in the `convert-schema` command's Example 4 of the sample console script file.|`object-name`: Specify the source database / object name that requires to be converted. Ensure that the corresponding `object-type` is changed based on the type of object that is specified in the `object-name`|  
    |`synchronize-target`|Synchronizes the target objects with the target database.<br /><br />If the user has to assess multiple objects / databases he can specify multiple `metabase-object` nodes as illustrated in the `synchronize-target` command's Example 3 of the sample console script file.|`object-name:` Specify the sql server database / object name that requires to be created. Ensure that the corresponding `object-type` is changed based on the type of object that is specified in the `object-name`|  
    |`migrate-data`|Migrates the source data to the target.<br /><br />If the user has to assess multiple objects / databases he can specify multiple `metabase-object` nodes as illustrated in the `migrate-data` command's Example 2 of the sample console script file.|`object-name:` Specifies the source database / tables name that requires to be migrated. Ensure that the corresponding `object-type` is changed based on the type of object that is specified in the `object-name`|  
  
## See Also  
[Creating Variable Value Files &#40;DB2ToSQL&#41;](../../ssma/db2/creating-variable-value-files-db2tosql.md)  
[Creating the Server Connection Files &#40;DB2ToSQL&#41;](../../ssma/db2/creating-the-server-connection-files-db2tosql.md)  
[Generating Reports &#40;DB2ToSQL&#41;](../../ssma/db2/generating-reports-db2tosql.md)  
  
