---
title: "getURL Method (SQLServerDataSource)"
description: "getURL Method (SQLServerDataSource)"
author: David-Engel
ms.author: davidengel
ms.date: "01/19/2017"
ms.service: sql
ms.subservice: connectivity
ms.topic: reference
apilocation: "sqljdbc.jar"
apiname: "SQLServerDataSource.getURL"
apitype: "Assembly"
---
# getURL Method (SQLServerDataSource)
[!INCLUDE[Driver_JDBC_Download](../../../includes/driver_jdbc_download.md)]

  Returns the URL that is used to connect to the data source.  
  
## Syntax  
  
```  
  
public java.lang.String getURL()  
```  
  
## Return Value  
 A **String** that contains the URL.  
  
## Remarks  
 For security reasons, you should not include the password in the URL that is supplied to the [setURL](../../../connect/jdbc/reference/seturl-method-sqlserverdatasource.md) method. The reason for this is that third-party Java Application Servers will very often display the value set for the URL property in their data source configuration user interface. Instead, use the [setPassword](../../../connect/jdbc/reference/setpassword-method-sqlserverdatasource.md) method to set the password value. Java Application Servers will not display a password that is set in their data source in the configuration user interface.  
  
> [!NOTE]  
>  If the setURL method is not called before calling the getURL method, getURL returns the default value of "jdbc:sqlserver://".  
  
## See Also  
 [SQLServerDataSource Members](../../../connect/jdbc/reference/sqlserverdatasource-members.md)   
 [SQLServerDataSource Class](../../../connect/jdbc/reference/sqlserverdatasource-class.md)  
  
  
