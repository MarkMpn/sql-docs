---
title: "Set a No Data Message for a Data Region (Report Builder)"
description: Learn how to set a no data message to show in a rendered report in place of a data region that has no data.
author: maggiesMSFT
ms.author: maggies
ms.date: 09/25/2024
ms.service: reporting-services
ms.subservice: report-data
ms.topic: conceptual
ms.custom:
  - updatefrequency5
---
# Set a No Data Message for a Data Region (Report Builder and SSRS)
  When you want to specify text to show in the rendered report in place of a data region that has no data, set the NoRowsMessage property for a table, matrix, or list data region, the NoDataMessage for a chart data region, and the NoDataText for the color scale for a map. At run time, the report processor runs the query for each dataset in a report and the dataset query may produce no result set. For a data region bound to an empty dataset, you can specify text to display instead of displaying an empty data region. You can also set the NoRowsMessage property for a subreport when no datasets in the subreport have data at run time.  
  
> [!NOTE]  
>  [!INCLUDE[ssRBRDDup](../../includes/ssrbrddup-md.md)]  
  
### To set the NoRowsMessage property for a table, matrix, or list  
  
1.  In Design view, click the table, matrix, or list data region or subreport on the design surface to select it. The Properties pane displays the properties for the selected item.  
  
2.  In the Properties pane, type the text that you want to display as a message in **NoRowsMessage** property field.  
  
     Alternatively, from the drop-down list, click **Expression** to open the **Expression** dialog box and create an expression.  
  
### To set the NoDataMessage property for a chart  
  
1.  In Design view, click the chart on the design surface to select it. The Properties pane displays the properties for the selected item.  
  
2.  In the Properties pane, expand the node for **NoDataMessage**.  
  
3.  In **Caption**, type the text that you want to display as a message in **NoDataMessage** property field.  
  
     Alternatively, from the drop-down list, click **Expression** to open the **Expression** dialog box and create an expression.  
  
### To set the NoRowsMessage for a subreport  
  
1.  In Design view, click the subreport on the design surface to select it. The Properties pane displays the properties for the selected item.  
  
2.  In the Properties pane, type the text that you want to display as a message in **NoRowsMessage** property field.  
  
     Alternatively, from the drop-down list, click **Expression** to open the **Expression** dialog box and create an expression.  
  
### To set the NoDataText property for a color scale for a map  
  
1.  In Design view, click the color scale on the map to select it. The Properties pane displays the properties for the selected item.  
  
2.  In the Properties pane, in **NoDataText**, type the text that you want to display as a label for colors with no data value.  
  
     Alternatively, from the drop-down list, click **Expression** to open the **Expression** dialog box and create an expression.  
  
## Related content

- [Subreports &#40;Report Builder and SSRS&#41;](../../reporting-services/report-design/subreports-report-builder-and-ssrs.md)
- [Tables, Matrices, and Lists &#40;Report Builder and SSRS&#41;](../../reporting-services/report-design/tables-matrices-and-lists-report-builder-and-ssrs.md)
- [Charts &#40;Report Builder and SSRS&#41;](../../reporting-services/report-design/charts-report-builder-and-ssrs.md)
- [Maps &#40;Report Builder and SSRS&#41;](../../reporting-services/report-design/maps-report-builder-and-ssrs.md)
