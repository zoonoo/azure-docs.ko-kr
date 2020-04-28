---
title: Azure 하이브리드 혜택
description: SQL Database 할인에 기존 SQL Server 라이선스를 사용 합니다.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 11/13/2019
ms.openlocfilehash: d1a59e7ad86191bcc30b7d898d00f327c20fbc5e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "75945612"
---
# <a name="azure-hybrid-benefit"></a>Azure 하이브리드 혜택

VCore 기반 구매 모델의 프로 비전 된 계산 계층에서는 [SQL Server에 대 한 Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/)를 사용 하 여 SQL Database에 대 한 할인 된 요금으로 기존 라이선스를 교환할 수 있습니다. 이 Azure 혜택을 통해 소프트웨어 보증이 포함 된 온-프레미스 SQL Server 라이선스를 사용 하 여 Azure SQL Database에 대해 최대 30% 또는 훨씬 더 많은 비용을 절감할 수 있습니다. 올바른 값에 대해 앞에서 설명한 링크를 사용 하 여 Azure 하이브리드 혜택 계산기를 사용 하십시오. 

> [!NOTE]
> Azure 하이브리드 혜택로 변경 하는 경우 가동 중지 시간이 필요 하지 않습니다.

![가격 책정](./media/sql-database-service-tiers/pricing.png)

## <a name="choose-a-license-model"></a>라이선스 모델 선택

Azure 하이브리드 혜택를 사용 하면 SQL database 엔진 자체 (기본 계산 가격 책정)에 기존 SQL Server 라이선스를 사용 하 여 기본 Azure 인프라에 대해서만 비용을 지불할 수 있습니다. 또는 기본 인프라 및 SQL Server 라이선스 (라이선스 포함 가격)에 대해 비용을 지불할 수 있습니다.

Azure Portal 또는 다음 Api 중 하나를 사용 하 여 라이선스 모델을 선택 하거나 변경할 수 있습니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용 하 여 라이선스 유형을 설정 하거나 업데이트 하려면 다음을 수행 합니다.

- [AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)
- [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)
- [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance)
- [Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용 하 여 라이선스 유형을 설정 하거나 업데이트 하려면 다음을 수행 합니다.

- [az sql db 만들기](/cli/azure/sql/db#az-sql-db-create)
- [az sql db update](/cli/azure/sql/db#az-sql-db-update)
- [az sql mi create](/cli/azure/sql/mi#az-sql-mi-create)
- [az sql mi update](/cli/azure/sql/mi#az-sql-mi-update)

# <a name="rest-api"></a>[REST API](#tab/rest)

REST API를 사용 하 여 라이선스 유형을 설정 하거나 업데이트 하려면 다음을 수행 합니다.

- [데이터베이스 - Create 또는 Update](/rest/api/sql/databases/createorupdate)
- [데이터베이스 - Update](/rest/api/sql/databases/update)
- [Managed Instances - Create 또는 Update](/rest/api/sql/managedinstances/createorupdate)
- [Managed Instances - Update](/rest/api/sql/managedinstances/update)

* * *

## <a name="next-steps"></a>다음 단계

- SQL Database 배포 옵션 중에서 선택 하려면 [AZURE SQL에서 올바른 배포 옵션 선택](sql-database-paas-vs-sql-server-iaas.md)을 참조 하세요.
- SQL Database 기능에 대 한 비교는 [Azure SQL Database 기능](sql-database-features.md)을 참조 하세요.
