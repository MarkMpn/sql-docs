---
title: "Updatable Subscriptions"
description: "Updatable Subscriptions"
author: "MashaMSFT"
ms.author: "mathoma"
ms.date: 09/25/2024
ms.service: sql
ms.subservice: replication
ms.topic: ui-reference
ms.custom:
  - updatefrequency5
f1_keywords:
  - "sql13.rep.newsubwizard.updatablesubscriptions.f1"
---
# Updatable Subscriptions
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  With transactional replication, replicated data should be treated as read-only; however, you can modify replicated data at a [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Subscriber by using updatable subscriptions. If you need to modify data at the Subscriber, choose one of the following options depending on your requirements.  
  
|Updatable Subscription Type|Requirements|  
|---------------------------------|------------------|  
|Immediate Updating|Publisher and Subscriber must be connected to update data at the Subscriber.|  
|Queued Updating|Publisher and Subscriber do not have to be connected to update data at the Subscriber. Updates can be made while offline, and later synchronized between the Publisher and Subscriber.|  
  
## Options  
 **Replicate Subscriber changes**  
 Select the check box in the **Replicate** column for each Subscriber that should be able to make updates. For those Subscribers that can make updates, select the appropriate option from the drop-down list box in the **Commit at Publisher** column:  
  
-   Select **Simultaneously commit changes** for an immediate updating subscription.  
  
-   Select **Queue changes and commit when possible** for a queued updating subscription.  
  
## Related content

- [Create a Pull Subscription](../../relational-databases/replication/create-a-pull-subscription.md)
- [Create a Push Subscription](../../relational-databases/replication/create-a-push-subscription.md)
- [Subscribe to Publications](../../relational-databases/replication/subscribe-to-publications.md)
- [Updatable Subscriptions for Transactional Replication](../../relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication.md)
