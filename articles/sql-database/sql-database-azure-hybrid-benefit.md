---
title: Azure 하이브리드 혜택
description: SQL 데이터베이스 할인에 기존 SQL Server 라이선스를 사용합니다.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 11/13/2019
ms.openlocfilehash: d1a59e7ad86191bcc30b7d898d00f327c20fbc5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75945612"
---
# <a name="azure-hybrid-benefit"></a>Azure 하이브리드 혜택

vCore 기반 구매 모델의 프로비저닝된 계산 계층에서 [SQL Server에 Azure 하이브리드 혜택을](https://azure.microsoft.com/pricing/hybrid-benefit/)사용하여 기존 라이선스를 SQL Database에서 할인된 요금으로 교환할 수 있습니다. 이 Azure 혜택을 사용하면 소프트웨어 보증을 사용하여 온-프레미스 SQL Server 라이선스를 사용하여 Azure SQL Database에서 최대 30% 또는 그 이상의 비용을 절약할 수 있습니다. 올바른 값을 위해 앞에서 언급한 링크를 사용하여 Azure 하이브리드 혜택 계산기를 사용하십시오. 

> [!NOTE]
> Azure 하이브리드 혜택으로 변경해도 가동 중지 시간이 필요하지 않습니다.

![가격 책정](./media/sql-database-service-tiers/pricing.png)

## <a name="choose-a-license-model"></a>라이센스 모델 선택

Azure 하이브리드 이점을 사용하면 SQL 데이터베이스 엔진 자체에 대한 기존 SQL Server 라이선스(기본 계산 가격 책정)를 사용하여 기본 Azure 인프라에 대해서만 비용을 지불하도록 선택하거나 기본 인프라와 SQL Server 모두에 대해 비용을 지불할 수 있습니다. 라이선스(라이선스 포함 가격)를 참조하십시오.

Azure 포털을 사용하거나 다음 API 중 하나를 사용하여 라이선스 모델을 선택하거나 변경할 수 있습니다.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

PowerShell을 사용하여 라이센스 유형을 설정하거나 업데이트하려면 다음을 수행하십시오.

- [뉴 아즈Sql데이터베이스](/powershell/module/az.sql/new-azsqldatabase)
- [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)
- [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance)
- [Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 라이선스 유형을 설정하거나 업데이트하려면 다음을 수행하십시오.

- [az sql db 만들기](/cli/azure/sql/db#az-sql-db-create)
- [az sql db update](/cli/azure/sql/db#az-sql-db-update)
- [az sql mi create](/cli/azure/sql/mi#az-sql-mi-create)
- [az sql mi update](/cli/azure/sql/mi#az-sql-mi-update)

# <a name="rest-api"></a>[나머지 API](#tab/rest)

REST API를 사용하여 라이센스 유형을 설정하거나 업데이트하려면 다음을 수행하십시오.

- [데이터베이스 - Create 또는 Update](/rest/api/sql/databases/createorupdate)
- [데이터베이스 - Update](/rest/api/sql/databases/update)
- [Managed Instances - Create 또는 Update](/rest/api/sql/managedinstances/createorupdate)
- [Managed Instances - Update](/rest/api/sql/managedinstances/update)

* * *

## <a name="next-steps"></a>다음 단계

- SQL Database 배포 옵션 중에서 선택시 [Azure SQL에서 올바른 배포 옵션 선택을](sql-database-paas-vs-sql-server-iaas.md)참조하십시오.
- SQL 데이터베이스 기능을 비교하면 [Azure SQL 데이터베이스 기능을](sql-database-features.md)참조하십시오.
