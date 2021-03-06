---
title: "Diagnostics and Error Recovery for Import-Export Jobs"
ms.custom: na
ms.date: 05/25/2015
ms.prod: azure
ms.reviewer: na
ms.service: storage
ms.suite: na
ms.tgt_pltfrm: na
ms.topic: reference
ms.assetid: 096cc795-9af6-4335-9fe8-fffa9f239a17
caps.latest.revision: 7
author: tamram
manager: adinah
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
# Diagnostics and Error Recovery for Import-Export Jobs
For each drive processed, the Azure Import/Export service creates an error log in the associated storage account. You can also enable verbose logging by setting the `EnableVerboseLog` property to `true` when calling the [Put Job](../StorageImportExportREST/Put-Job.md) or [Update Job Properties](../StorageImportExportREST/Update-Job-Properties.md) operations.  
  
 By default, logs are written to a container named `waimportexport`. You can specify a different name by setting the `ImportExportStatesPath` property when calling the `Put Job` or `Update Job Properties` operations. The logs are stored as block blobs with the following naming convention: `waies/jobname_driveid_timestamp_logtype.xml`.  
  
 You can retrieve the URI of the logs for a job by calling the [Get Job](../StorageImportExportREST/Get-Job3.md) operation. The URI for the verbose log is returned in the `VerboseLogUri` property for each drive, while the URI for the error log is returned in the `ErrorLogUri` property.  
  
 You can use the logging data to identify the following issues:  
  
 **Drive Errors**  
  
-   Errors in accessing or reading the manifest file  
  
-   Incorrect BitLocker keys  
  
-   Drive read/write errors  
  
 **Blob Errors**  
  
-   Incorrect or conflicting blob or names  
  
-   Missing files  
  
-   Blob not found  
  
-   Truncated files (the files on the disk are smaller than specified in the manifest)  
  
-   Corrupted file content (for import jobs, detected with an MD5 checksum mismatch)  
  
-   Corrupted blob metadata and property files (detected with an MD5 checksum mismatch)  
  
-   Incorrect schema for the blob properties and/or metadata files  
  
 There may be cases where some parts of an import or export job do not complete successfully, while the overall job still completes. In this case, you can either upload or download the missing pieces of the data over network, or you can create a new job to transfer the data. See the [Azure Import-Export Tool Reference](../StorageImportExportREST/Azure-Import-Export-Tool-Reference.md) to learn how to repair the data over network.  
  
## See Also  
 [Using the Import/Export Service REST API](../StorageImportExportREST/Using-the-Azure-Import-Export-Service-REST-API.md)