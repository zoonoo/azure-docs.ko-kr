---
title: Azure 하이브리드 혜택
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Azure SQL Database 및 SQL Managed Instance 할인에 기존 SQL Server 라이선스를 사용합니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=4
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake
ms.date: 02/16/2021
ms.openlocfilehash: b5f85e0dcb8ca70d5773b8f1c3b53e0b449ef013
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779204"
---
# <a name="azure-hybrid-benefit---azure-sql-database--sql-managed-instance"></a>Azure 하이브리드 혜택 - Azure SQL Database 및 SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

VCore 기반 구매 모델의 프로비전된 계산 계층에서는 [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/)을 사용하여 Azure SQL Database 및 Azure SQL Managed Instance를 할인된 요금으로 기존 라이선스와 교환할 수 있습니다. 이 Azure 혜택을 통해 소프트웨어 보증이 제공되는 SQL Server 라이선스를 사용하여 SQL Database와 SQL Managed Instance에서 최대 30% 또는 훨씬 더 많은 비용을 절감할 수 있습니다. [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/) 페이지에 절감액을 확인하실 수 있는 계산기가 있습니다.  Azure 하이브리드 혜택은 서버리스 Azure SQL Database에는 적용되지 않습니다.

> [!NOTE]
> Azure 하이브리드 혜택으로 변경하는 경우 가동 중지 시간이 필요하지 않습니다.

![vCore 가격 책정 구조](./media/azure-hybrid-benefit/pricing.png)

## <a name="choose-a-license-model"></a>라이선스 모델 선택

Azure 하이브리드 혜택을 사용하여 SQL Database 엔진 자체에 대해 기존 SQL Server 라이선스(기본 컴퓨팅 가격)를 사용하여 기존 Azure 인프라에 대해서만 비용을 지불하거나, 기본 인프라 및 SQL Server 라이선스(라이선스 포함 가격) 둘 다에 대해 비용을 지불할지를 선택할 수 있습니다.

Azure Portal에서 라이선스 모델을 선택하거나 변경할 수 있습니다. 
- 새 데이터베이스의 경우 만드는 동안 **기본 사항** 탭에서 **데이터베이스 구성** 을 선택하고 옵션을 선택하여 비용을 절감합니다.
- 기존 데이터베이스의 경우 **설정** 메뉴에서 **구성** 을 선택하고 옵션을 선택하여 비용을 절감합니다.

다음 API 중 하나를 사용하여 새 데이터베이스 또는 기존 데이터베이스를 구성할 수도 있습니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용하여 라이선스 형식을 설정하거나 업데이트하려면 다음을 사용합니다.

- [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)
- [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)
- [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance)
- [Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 라이선스 형식을 설정하거나 업데이트하려면 다음을 사용합니다.

- [az sql db create](/cli/azure/sql/db#az_sql_db_create)
- [az sql mi create](/cli/azure/sql/mi#az_sql_mi_create)
- [az sql mi update](/cli/azure/sql/mi#az_sql_mi_update)

# <a name="rest-api"></a>[REST API](#tab/rest)

REST API를 사용하여 라이선스 형식을 설정하거나 업데이트하려면 다음을 사용합니다.

- [데이터베이스 - Create 또는 Update](/rest/api/sql/databases/createorupdate)
- [데이터베이스 - Update](/rest/api/sql/databases/update)
- [Managed Instances - Create 또는 Update](/rest/api/sql/managedinstances/createorupdate)
- [Managed Instances - Update](/rest/api/sql/managedinstances/update)

* * *


### <a name="azure-hybrid-benefit-questions"></a>Azure 하이브리드 혜택 질문

#### <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>SQL Server에 대한 Azure 하이브리드 혜택에 이중 사용 권한이 있나요?

마이그레이션이 원활하게 실행되도록 하려면 180일간의 이중 사용 권한 라이선스가 있어야 합니다. SQL Database 클라우드의 SQL Server 라이선스는 180일의 기간이 지난 후에만 사용할 수 있습니다. 온-프레미스와 클라우드에는 더 이상 이중 사용 권한이 없습니다.

#### <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>SQL Server에 대한 Azure 하이브리드 혜택은 라이선스 이동과 어떻게 다른가요?

Software Assurance를 통해 SQL Server 고객들에게 라이선스 이동 혜택을 제공합니다. 이렇게 하면 파트너의 공유 서버에 대한 라이선스를 재할당할 수 있습니다. 이 혜택은 Azure IaaS 및 AWS EC2에서 사용할 수 있습니다.

SQL Server에 대한 Azure 하이브리드 혜택은 두 가지 주요 영역에서 라이선스 이동과 다릅니다.

- 높은 수준으로 가상화된 워크로드를 Azure로 이동할 수 있는 경제적 이점을 제공합니다. SQL Server Enterprise Edition 고객은 높은 수준으로 가상화된 애플리케이션을 위해 온-프레미스에서 소유하는 모든 코어에 대해 Azure의 범용 SKU에서 4개의 코어를 얻을 수 있습니다. 라이선스 이동은 가상화된 워크로드를 클라우드로 이동하는 데 특별한 비용상의 혜택을 허용하지 않습니다.
- Azure SQL Managed Instance에서 SQL Server와 항상 호환되는 PaaS 대상을 제공합니다.

#### <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>SQL Server에 대한 Azure 하이브리드 혜택의 특정 권한은 무엇인가요?

SQL Server에 대한 Azure 하이브리드 혜택과 관련하여 SQL Database 및 SQL Managed Instance 고객이 보유하는 권한은 다음과 같습니다.

|라이선스 공간|SQL Server에 대한 Azure 하이브리드 혜택의 내용|
|---|---|
|SA가 있는 SQL Server Enterprise Edition 핵심 고객|<li>하이퍼스케일, 범용 또는 중요 비즈니스 SKU 중 하나에 대한 기본 요금을 지불할 수 있음</li><br><li>온-프레미스의 1개 코어 = 하이퍼스케일 SKU의 4개 코어</li><br><li>온-프레미스의 1개 코어 = 범용 SKU의 4개 코어</li><br><li>온-프레미스의 1개 코어 = 중요 비즈니스 SKU의 1개 코어</li>|
|SA가 있는 SQL Server Standard Edition 핵심 고객|<li>하이퍼스케일, 범용 또는 중요 비즈니스 SKU 중 하나에 대한 기본 요금을 지불할 수 있음</li><br><li>온-프레미스의 1개 코어 = 하이퍼스케일 SKU의 1개 코어</li><br><li>온-프레미스의 1개 코어 = 범용 SKU의 1개 코어</li><br><li>온-프레미스의 4개 코어 = 중요 비즈니스 SKU의 1개 코어</li>|
|||


## <a name="next-steps"></a>다음 단계

- Azure SQL 배포 옵션을 선택하는 데 대한 도움말을 확인하려면 [Azure SQL에서 올바른 배포 옵션 선택](azure-sql-iaas-vs-paas-what-is-overview.md)을 참조하세요.
- SQL Database와 SQL Managed Instance 기능을 비교하려면 [SQL Database 및 SQL Managed Instance 기능](database/features-comparison.md)을 참조하세요.
