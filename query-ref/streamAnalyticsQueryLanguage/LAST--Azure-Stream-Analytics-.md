---
title: "LAST (Azure Stream Analytics)"
ms.custom: na
ms.date: 2016-05-02
ms.prod: azure
ms.reviewer: na
ms.service: stream-analytics
ms.suite: na
ms.tgt_pltfrm: na
ms.topic: reference
applies_to: 
  - Azure
ms.assetid: 6f8fabcb-4af9-4c8f-b30d-a6b676717353
caps.latest.revision: 17
author: jeffstokes72
manager: jhubbard
translation.priority.mt: 
  - de-de
  - es-es
  - fr-fr
  - it-it
  - ja-jp
  - ko-kr
  - pt-br
  - ru-ru
  - zh-cn
  - zh-tw
---
# LAST (Azure Stream Analytics)
  The LAST analytic operator allows one to look up the most recent event in an event stream within defined constraints.  It is useful in the scenarios like computing last known good value (e.g. not null), finding last time when event matched certain criteria, etc.  
  
 In Stream Analytics, the scope of LAST (that is, how far back in history from the current event it needs to look) is always limited to a finite time interval, using the LIMIT DURATION clause.  LAST can optionally be limited to only consider events that match the current event on a certain property or condition using the PARTITION BY and WHEN clauses.  
  
 **Remark:** LAST(<expression\>, <default\>) is equivalent of LAG(<expression\>, 0, <default\>) and is introduced for convenience and better readability.  
  
 **Syntax**  
  
```  
LAST(<scalar_expression >, [<default>])    
       OVER ( [PARTITION BY <partition key>] LIMIT DURATION(<unit>, <length>) [WHEN boolean_expression])  
  
```  
  
## Arguments  
 **scalar_expression**  
  
 The value to be returned. It is either an expression of any type that returns a single (scalar) value or the wildcard expression ‘*’. For ‘\*’ the entire event will be returned and will be contained in the result event (nested record). scalar_expression cannot contain other analytic functions or external functions.  
  
 **default**  
  
 The value to return when there is no event matching criteria. If a default value is not specified, NULL is returned. ‘No event’ can be the case if there are no prior events within the time interval specified in the limit_duration_clause  or the event exists but does not match condition specified in the when_clause. If the event exists and the value of scalar_expression is NULL then NULL is returned. default can be a column, subquery, or other expression, but it cannot contain other analytic functions or external functions. default must have the exact same type as scalar_expression.  
  
 **OVER ( [ partition_by_clause ] limit_duration_clause [when_clause])**  
  
 **partition_by_clause** PARTITION BY <partition key\> clause requests that only events whose value of   
<partition key\> is the same as that of the current event be considered.  
  
 **limit_duration_clause** DURATION(<unit\>, <length\>):   
Specifies how much of the history from the current event must be considered.  See DATEDIFF for a detailed description of supported units and their abbreviations.  If not enough matching events are found within the DURATION interval, the <default\> value is returned.  
  
 **when_clause** Specifies boolean condition for the events to be considered in LAST computation. If no matching events are found within the DURATION interval, the <default\> value is returned. The when_clause is optional.  
  
## Return Types  
 The data type of the specified scalar_expression. NULL is returned if scalar_expression  
  
## Examples  
 Find most recent non-null sensor reading:  
  
```  
SELECT  
       sensorId,   
       LAST(reading) OVER (PARTITION BY sensorId LIMIT DURATION(hour, 1) WHEN reading IS NOT NULL)  
FROM input   
```  
  
 Find last time when reading was greater than 50:  
  
```  
LAST(System.Timestamp) OVER (PARTITION BY sensorId LIMIT DURATION(hour, 1) WHEN reading > 50 )  
       FROM input  
  
```  
  
## See Also  
 [ISFIRST &#40;Azure Stream Analytics&#41;](../streamAnalyticsQueryLanguage/ISFIRST--Azure-Stream-Analytics-.md)   
 [LAG &#40;Azure Stream Analytics&#41;](../streamAnalyticsQueryLanguage/LAG--Azure-Stream-Analytics-.md)  
  
  