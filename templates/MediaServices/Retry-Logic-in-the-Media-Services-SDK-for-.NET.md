---
title: "Retry Logic in the Media Services SDK for .NET"
ms.custom: na
ms.date: 2016-07-13
ms.prod: azure
ms.reviewer: na
ms.service: media-services
ms.suite: na
ms.tgt_pltfrm: na
ms.topic: article
ms.assetid: 0cb3194c-10b1-4edd-8afd-5d711e6a3ea2
caps.latest.revision: 8
author: juliako
manager: erikre
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
# Retry Logic in the Media Services SDK for .NET
When working with Microsoft Azure services, transient faults can occur. If a transient fault occurs, in most cases, after a few retries the operation succeeds. The Media Services SDK for .NET implements the retry logic to handle transient faults associated with exceptions and errors that are caused by web requests, executing queries, saving changes, and storage operations.  By default, the Media Services SDK for .NET executes four retries before re-throwing the exception to your application. The code in your application must then handle this exception properly.  
  
 The following is a brief guideline of Web Request, Storage, Query, and SaveChanges policies:  
  
-   The Storage policy is used for blob storage operations (uploads or download of asset files).  
  
-   The Web Request policy is used for generic web requests (for example, for getting an authentication token and resolving the users cluster endpoint).  
  
-   The Query policy is used for querying entities from REST (for example, mediaContext.Assets.Where(…)).  
  
-   The SaveChanges policy is used for doing anything that changes data within the service (for example, creating an entity updating an entity, calling a service function for an operation).  
  
 This topic lists exception types and error codes that are handled by the Media Services SDK for .NET retry logic.  
  
## Exception types  
 The following table describes exceptions that the Media Services SDK for .NET handles or does not handle for some operations that may cause transient faults.  
  
|Exception|Web Request|Storage|Query|SaveChanges|  
|---------------|-----------------|-------------|-----------|-----------------|  
|`WebException`<br /><br /> For more information, see the [WebException status codes](#WebExceptionStatus) section.|Yes|Yes|Yes|Yes|  
|`DataServiceClientException`<br /><br /> For more information, see [HTTP error status codes](#HTTPStatusCode).|No|Yes|Yes|Yes|  
|`DataServiceQueryException`<br /><br /> For more information, see [HTTP error status codes](#HTTPStatusCode).|No|Yes|Yes|Yes|  
|`DataServiceRequestException`<br /><br /> For more information, see [HTTP error status codes](#HTTPStatusCode).|No|Yes|Yes|Yes|  
|`DataServiceTransportException`|No|No|Yes|Yes|  
|`TimeoutException`|Yes|Yes|Yes|No|  
|`SocketException`|Yes|Yes|Yes|Yes|  
|`StorageException`|No|Yes|No|No|  
|`IOException`|No|Yes|No|No|  
  
###  <a name="WebExceptionStatus "></a> WebException status codes  
 The following table shows for which WebException error codes the retry logic is implemented. The [WebExceptionStatus](http://msdn.microsoft.com/library/system.net.webexceptionstatus.aspx) enumeration defines the status codes.  
  
|Status|Web Request|Storage|Query|SaveChanges|  
|------------|-----------------|-------------|-----------|-----------------|  
|`ConnectFailure`|Yes|Yes|Yes|Yes|  
|`NameResolutionFailure`|Yes|Yes|Yes|Yes|  
|`ProxyNameResolutionFailure`|Yes|Yes|Yes|Yes|  
|`SendFailure`|Yes|Yes|Yes|Yes|  
|`PipelineFailure`|Yes|Yes|Yes|No|  
|`ConnectionClosed`|Yes|Yes|Yes|No|  
|`KeepAliveFailure`|Yes|Yes|Yes|No|  
|`UnknownError`|Yes|Yes|Yes|No|  
|`ReceiveFailure`|Yes|Yes|Yes|No|  
|`RequestCanceled`|Yes|Yes|Yes|No|  
|`Timeout`|Yes|Yes|Yes|No|  
|`ProtocolError`<br /><br /> The retry on `ProtocolError` is controlled by the HTTP status code handling. For more information, see [HTTP error status codes](#HTTPStatusCode).|Yes|Yes|Yes|Yes|  
  
###  <a name="HTTPStatusCode "></a> HTTP error status codes  
 When Query and SaveChanges operations throw `DataServiceClientException`, `DataServiceQueryException`, or `DataServiceQueryException`, the HTTP error status code is returned in the StatusCode property.  The following table shows for which error codes the retry logic is implemented.  
  
||||||  
|-|-|-|-|-|  
|Status|Web Request|Storage|Query|SaveChanges|  
|`401`|No|Yes|No|No|  
|`403`|No|Yes<br /><br /> Handling retries with longer waits.|No|No|  
|`408`|Yes|Yes|Yes|Yes|  
|`429`|Yes|Yes|Yes|Yes|  
|`500`|Yes|Yes|Yes|No|  
|`502`|Yes|Yes|Yes|No|  
|`503`|Yes|Yes|Yes|Yes|  
|`504`|Yes|Yes|Yes|No|  
  
 If you want to take a look at the actual implementation of the Media Services SDK for .NET retry logic, see [azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services/tree/dev/src/net/Client/TransientFaultHandling).