---
title: "PowerShell: Azure SQL Database 감사 관리 | Microsoft Docs"
description: "데이터베이스 이벤트를 추적하여 Azure Storage 계정의 감사 로그에 기록하도록 PowerShell에서 Azure SQL Database 감사를 구성합니다."
services: sql-database
documentationcenter: 
author: ronitr
manager: jhubbard
editor: giladm
ms.assetid: 89c2a155-c2fb-4b67-bc19-9b4e03c6d3bc
ms.service: sql-database
ms.custom: secure and protect
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2016
ms.author: ronitr; giladm
translationtype: Human Translation
ms.sourcegitcommit: 5d51a5ef3387b4c00079547b0f44ffe1f96bd77c
ms.openlocfilehash: 9839dfb02478593de9a6db59d2a462d2b64f957e
ms.lasthandoff: 02/17/2017


---
# <a name="configure-and-manage-sql-database-auditing-using-powershell"></a>PowerShell을 사용하여 SQL Database 감사 구성 및 관리

다음 섹션에서는 PowerShell을 사용하여 감사를 구성 및 관리하는 방법을 설명합니다. Azure Portal을 사용하여 감사를 구성 및 관리하려면 [Azure Portal에서 감사 구성](sql-database-auditing-portal.md)을 참조하세요. REST API를 사용하여 감사를 구성 및 관리하려면 [REST API에서 감사 구성](sql-database-auditing-rest.md)을 참조하세요.

감사의 개요는 [SQL Database 감사](sql-database-auditing.md)를 참조하세요.

## <a name="powershell-cmdlets"></a>PowerShell cmdlet

   * [Get-AzureRMSqlDatabaseAuditingPolicy][101]
   * [Get-AzureRMSqlServerAuditingPolicy][102]
   * [Remove-AzureRMSqlDatabaseAuditing][103]
   * [Remove-AzureRMSqlServerAuditing][104]
   * [Set-AzureRMSqlDatabaseAuditingPolicy][105]
   * [Set-AzureRMSqlServerAuditingPolicy][106]
   * [Use-AzureRMSqlServerAuditingPolicy][107]

## <a name="next-steps"></a>다음 단계

* Azure Portal을 사용하여 감사를 구성 및 관리하려면 [Azure Portal에서 데이터베이스 감사 구성](sql-database-auditing-portal.md)을 참조하세요. 
* PowerShell을 사용하여 감사를 구성 및 관리하려면 [REST API에서 데이터베이스 감사 구성](sql-database-auditing-rest.md)을 참조하세요.
* 감사의 개요는 [데이터베이스 감사](sql-database-auditing.md)를 참조하세요.


[101]: https://msdn.microsoft.com/en-us/library/azure/mt603731(v=azure.200).aspx
[102]: https://msdn.microsoft.com/en-us/library/azure/mt619329(v=azure.200).aspx
[103]: https://msdn.microsoft.com/en-us/library/azure/mt603796(v=azure.200).aspx
[104]: https://msdn.microsoft.com/en-us/library/azure/mt603574(v=azure.200).aspx
[105]: https://msdn.microsoft.com/en-us/library/azure/mt603531(v=azure.200).aspx
[106]: https://msdn.microsoft.com/en-us/library/azure/mt603794(v=azure.200).aspx
[107]: https://msdn.microsoft.com/en-us/library/azure/mt619353(v=azure.200).aspx

