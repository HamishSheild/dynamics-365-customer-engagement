---
title: "Use ExecuteMultiple to improve performance for bulk data load (Developer Guide for Dynamics 365 for Customer Engagement)| MicrosoftDocs"
description: "ExecuteMultipleRequest message supports higher throughput bulk message passing scenarios in Dynamics 365 for Customer Engagement (online) Customer Engagement, particularly in the case of Dynamics 365 for Customer Engagement (online) where Internet latency can be the largest limiting factor"
ms.custom: 
ms.date: 09/18/2018
ms.reviewer: 
ms.service: crm-online
ms.suite: 
ms.tgt_pltfrm: 
ms.topic: article
applies_to: 
  - Dynamics 365 for Customer Engagement (online)
ms.assetid: 8029be0f-0dd6-44d2-b458-94fb40095ec3
author: JimDaly
ms.author: jdaly
manager: amyla
search.audienceType: 
  - developer
search.app: 
  - D365CE
---
# Use ExecuteMultiple to improve performance for bulk data load

[!INCLUDE[](../../includes/cc_applies_to_update_9_0_0.md)]

You can use the <xref:Microsoft.Xrm.Sdk.Messages.ExecuteMultipleRequest> message to support higher throughput bulk message passing scenarios in [!INCLUDE[pn_dynamics_crm_online](../../includes/pn-dynamics-crm-online.md)] , particularly in the case of [!INCLUDE[pn_CRM_Online](../../includes/pn-crm-online.md)] apps where Internet latency can be the largest limiting factor. <xref:Microsoft.Xrm.Sdk.Messages.ExecuteMultipleRequest> accepts an input collection of message <xref:Microsoft.Xrm.Sdk.Messages.ExecuteMultipleRequest.Requests>, executes each of the message requests in the order they appear in the input collection, and optionally returns a collection of <xref:Microsoft.Xrm.Sdk.Messages.ExecuteMultipleResponse.Responses> containing each message’s response or the error that occurred. Each message request in the input collection is processed in a separate database transaction. <xref:Microsoft.Xrm.Sdk.Messages.ExecuteMultipleRequest> is executed by using the <xref:Microsoft.Xrm.Sdk.IOrganizationService>.<xref:Microsoft.Xrm.Sdk.IOrganizationService.Execute*> method.  
  
In general, <xref:Microsoft.Xrm.Sdk.Messages.ExecuteMultipleRequest> behaves the same as if you executed each message request in the input request collection separately, except with better performance. Use of the <xref:Microsoft.Xrm.Sdk.Client.OrganizationServiceProxy.CallerId> parameter of the service proxy is honored and will apply to the execution of every message in the input request collection. Plug-ins and workflow activities are executed as you would expect for each message processed.  

Custom code in the form of plug-ins and custom workflow activities can even execute <xref:Microsoft.Xrm.Sdk.Messages.ExecuteMultipleRequest>. However, there are a few key points to keep in mind. An exception thrown by a synchronous registered plug-in is returned in the response collection item <xref:Microsoft.Xrm.Sdk.ExecuteMultipleResponseItem.Fault> parameter. If a plug-in executes within a database transaction, the plug-in executes <xref:Microsoft.Xrm.Sdk.Messages.ExecuteMultipleRequest>, and a transaction rollback is initiated, the rollback includes any data changes resulting from requests executed by <xref:Microsoft.Xrm.Sdk.Messages.ExecuteMultipleRequest>.

For more detailed information about ExecuteMultiple, see [Execute multiple requests using the Organization service](/powerapps/developer/common-data-service/org-service/execute-multiple-requests).
  
<a name="limitations"></a>

## On-premise run-time limitations

There are several constraints related to the use of the <xref:Microsoft.Xrm.Sdk.Messages.ExecuteMultipleRequest> as described in the following list.  
  
- No recursion is allowed - <xref:Microsoft.Xrm.Sdk.Messages.ExecuteMultipleRequest> cannot invoke <xref:Microsoft.Xrm.Sdk.Messages.ExecuteMultipleRequest>. An <xref:Microsoft.Xrm.Sdk.Messages.ExecuteMultipleRequest> found in the request collection will generate a fault for that request item.  
  
- Maximum batch size – there is a limit to how many requests can be added to a request collection. If that limit is exceeded, a fault is thrown before the first request is ever executed. A limit of 1000 requests is typical though this maximum amount can be set for the [!INCLUDE[pn_dynamics_crm](../../includes/pn-dynamics-crm.md)] apps deployment. The deployment setting for this limit is <xref:Microsoft.Xrm.Sdk.Deployment.ImportSettings.BatchSize>.  
  
- Throttling of concurrent calls – for [!INCLUDE[pn_CRM_Online](../../includes/pn-crm-online.md)] apps there is a limit of 2 concurrent <xref:Microsoft.Xrm.Sdk.Messages.ExecuteMultipleRequest> executions per organization. If that limit is exceeded, a “Server Busy” fault is thrown before the first request is ever executed. For an on-premises deployment, throttling is not enabled by default. The deployment setting for this limit is <xref:Microsoft.Xrm.Sdk.Deployment.ThrottleSettings.ExecuteMultiplePerOrgMaxConnectionsPerServer>.  
  
  > [!TIP]
  >  For any [!INCLUDE[pn_crm_shortest](../../includes/pn-crm-shortest.md)] apps deployment, a deployment administrator can set or change the throttling limit.  
  
### See also  
 [Read and Write Data and Metadata using the IOrganizationService Web Service](use-organization-service-read-write-data-metadata.md)   
 [Microsoft.Xrm.Sdk Messages](xrm-messages-organization-service.md)   
 <xref:Microsoft.Xrm.Sdk.IOrganizationService>.<xref:Microsoft.Xrm.Sdk.IOrganizationService.Execute*>   
 <xref:Microsoft.Xrm.Sdk.OrganizationRequest>   
 <xref:Microsoft.Xrm.Sdk.OrganizationResponse>   
 [Microsoft.Crm.Sdk Messages](organization-service-messages.md)   
 [Import data](../import-data.md)
