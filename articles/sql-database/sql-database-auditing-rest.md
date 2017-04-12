---
title: "REST API: Azure SQL Database 감사 관리 | Microsoft Docs"
description: "데이터베이스 이벤트를 추적하여 Azure Storage 계정의 감사 로그에 기록하도록 REST API를 사용하여 Azure SQL Database 감사를 구성합니다."
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
ms.openlocfilehash: 9516dd1ae6a67fb9402591fe1906ee7554c1495c
ms.lasthandoff: 02/17/2017


---
# <a name="configure-and-manage-sql-database-auditing-using-the-rest-api"></a>REST API를 사용하여 SQL Database 감사 구성 및 관리

이 항목에서는 REST API를 사용하여 감사를 구성 및 관리하는 방법을 설명합니다. Azure Portal을 사용하여 감사를 구성 및 관리하려면 [Azure Portal에서 감사 구성](sql-database-auditing-portal.md)을 참조하세요. PowerShell을 사용하여 감사를 구성 및 관리하려면 [PowerShell에서 감사 구성](sql-database-auditing-powershell.md)을 참조하세요.

감사의 개요는 [SQL Database 감사](sql-database-auditing.md)를 참조하세요.

## <a name="rest-api---blob-auditing"></a>REST API - Blob 감사

   * [데이터베이스 Blob 감사 정책 만들기 또는 업데이트](https://msdn.microsoft.com/library/azure/mt695939.aspx)
   * [서버 Blob 감사 정책 만들기 또는 업데이트](https://msdn.microsoft.com/library/azure/mt771861.aspx)
   * [데이터베이스 Blob 감사 정책 가져오기](https://msdn.microsoft.com/library/azure/mt695938.aspx)
   * [서버 Blob 감사 정책 가져오기](https://msdn.microsoft.com/library/azure/mt771860.aspx)
   * [서버 Blob 감사 작업 결과 가져오기](https://msdn.microsoft.com/library/azure/mt771862.aspx)


## <a name="rest-api---table-auditing"></a>REST API - 테이블 감사

   * [데이터베이스 감사 정책 만들기 또는 업데이트](https://msdn.microsoft.com/library/azure/mt604471.aspx)
   * [서버 감사 정책 만들기 또는 업데이트](https://msdn.microsoft.com/library/azure/mt604383.aspx)
   * [데이터베이스 감사 정책 가져오기](https://msdn.microsoft.com/library/azure/mt604381.aspx)
   * [서버 감사 정책 가져오기](https://msdn.microsoft.com/library/azure/mt604382.aspx)

## <a name="next-steps"></a>다음 단계

* Azure Portal을 사용하여 감사를 구성 및 관리하려면 [Azure Portal에서 데이터베이스 감사 구성](sql-database-auditing-portal.md)을 참조하세요. 
* PowerShell을 사용하여 감사를 구성 및 관리하려면 [PowerShell에서 데이터베이스 감사 구성](sql-database-auditing-powershell.md)을 참조하세요.
* 감사의 개요는 [데이터베이스 감사](sql-database-auditing.md)를 참조하세요.
