---
title: "getObject Method (int)"
description: "getObject Method (int)"
author: David-Engel
ms.author: davidengel
ms.date: "01/19/2017"
ms.service: sql
ms.subservice: connectivity
ms.topic: reference
apilocation: "sqljdbc.jar"
apiname: "SQLServerCallableStatement.getObject (jnt)"
apitype: "Assembly"
---
# getObject Method (int)
[!INCLUDE[Driver_JDBC_Download](../../../includes/driver_jdbc_download.md)]

  Retrieves the value of the designated parameter as an object in the Java programming language given the parameter index.  
  
## Syntax  
  
```  
  
public java.lang.Object getObject(int index)  
```  
  
#### Parameters  
 *index*  
  
 An **int** that indicates the parameter index.  
  
## Return Value  
 An **Object** value.  
  
## Exceptions  
 [SQLServerException](../../../connect/jdbc/reference/sqlserverexception-class.md)  
  
## Remarks  
 This getObject method is specified by the getObject method in the java.sql.CallableStatement interface.  
  
 This method will return the value of the given column as a Java object. The type of the Java object will be the default Java object type corresponding to the SQL type of the column, following the mapping for built-in types that is specified in the JDBC specification. If the value is an SQL NULL, the driver returns a Java null.  
  
 This method can also be used to read database-specific abstract data types. In JDBC 2.0, the behavior of the getObject method was extended to materialize data of SQL user-defined types. When a column contains a structured or distinct value, the behavior of this method is as if it were a call to `getObject(columnIndex, this.getStatement().getConnection().getTypeMap())`.  
  
 Beginning in the [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] JDBC Driver 3.0:  
  
-   A value of type **date** will be returned as a java.sql.Date object.  
  
-   A value of type **time** will be returned as a java.sql.Time object.  
  
-   A value of type **datetime2** will be returned as a java.sql.Timestamp object.  
  
-   A value of type **datetimeoffset** will be returned as a microsoft.sql.DateTimeOffset object.  
  
## See Also  
 [getObject Method &#40;SQLServerCallableStatement&#41;](../../../connect/jdbc/reference/getobject-method-sqlservercallablestatement.md)   
 [SQLServerCallableStatement Members](../../../connect/jdbc/reference/sqlservercallablestatement-members.md)   
 [SQLServerCallableStatement Class](../../../connect/jdbc/reference/sqlservercallablestatement-class.md)  
  
  
