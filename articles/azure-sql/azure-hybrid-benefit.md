---
title: Azure 하이브리드 혜택
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Azure SQL Database 및 SQL Managed Instance 할인에 기존 SQL Server 라이선스를 사용 합니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=4
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake
ms.date: 11/13/2019
ms.openlocfilehash: f43d03f722617e88e7a00e71798351c88ce10e98
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91327598"
---
# <a name="azure-hybrid-benefit---azure-sql-database--sql-managed-instance"></a>Azure 하이브리드 혜택-Azure SQL Database & SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

VCore 기반 구매 모델의 프로 비전 된 계산 계층에서는 [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/)를 사용 하 여 Azure SQL Database 및 Azure SQL Managed Instance에 대 한 할인 된 요금으로 기존 라이선스를 교환할 수 있습니다. 이 Azure 혜택을 통해 소프트웨어 보증이 있는 SQL Server 라이선스를 사용 하 여 SQL Database & SQL Managed Instance에서 최대 30% 또는 훨씬 더 많은 비용을 절감할 수 있습니다. [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/) 페이지에는 절감 액을 결정 하는 데 도움이 되는 계산기가 있습니다.  Azure 하이브리드 혜택는 서버를 사용 하지 않는 Azure SQL Database에는 적용 되지 않습니다.

> [!NOTE]
> Azure 하이브리드 혜택로 변경 하는 경우 가동 중지 시간이 필요 하지 않습니다.

![vcore 가격 책정 구조](./media/azure-hybrid-benefit/pricing.png)

## <a name="choose-a-license-model"></a>라이선스 모델 선택

Azure 하이브리드 혜택를 사용 하면 SQL Server 데이터베이스 엔진 자체 (기본 계산 가격 책정)에 기존 SQL Server 라이선스를 사용 하 여 기본 Azure 인프라에 대해서만 비용을 지불할 수 있습니다. 또는 기본 인프라와 SQL Server 라이선스 (라이선스 포함 가격) 모두에 대 한 비용을 지불할 수 있습니다.

Azure Portal에서 라이선스 모델을 선택 하거나 변경할 수 있습니다. 
- 새 데이터베이스의 경우 만드는 동안 **기본 사항** 탭에서 **데이터베이스 구성** 을 선택 하 고 비용을 절감 하는 옵션을 선택 합니다.
- 기존 데이터베이스의 경우 **설정** 메뉴에서 **구성** 을 선택 하 고 비용을 절감 하는 옵션을 선택 합니다.

다음 Api 중 하나를 사용 하 여 새 데이터베이스 또는 기존 데이터베이스를 구성할 수도 있습니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용 하 여 라이선스 유형을 설정 하거나 업데이트 하려면 다음을 수행 합니다.

- [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)
- [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)
- [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance)
- [Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용 하 여 라이선스 유형을 설정 하거나 업데이트 하려면 다음을 수행 합니다.

- [az sql db create](/cli/azure/sql/db#az-sql-db-create)
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


### <a name="azure-hybrid-benefit-questions"></a>Azure 하이브리드 혜택 질문

#### <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>SQL Server에 대한 Azure 하이브리드 혜택에 이중 사용 권한이 있나요?

마이그레이션이 원활하게 실행되도록 하려면 180일간의 이중 사용 권한 라이선스가 있어야 합니다. 180 일 기간 후에는 SQL Database 클라우드의 SQL Server 라이선스를 사용할 수 있습니다. 온-프레미스와 클라우드에는 더 이상 이중 사용 권한이 없습니다.

#### <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>SQL Server에 대한 Azure 하이브리드 혜택은 라이선스 이동과 어떻게 다른가요?

소프트웨어 보증을 통해 고객에 게 SQL Server에 대 한 라이선스 이동성 혜택을 제공 합니다. 이렇게 하면 파트너의 공유 서버에 대 한 라이선스를 재할당할 수 있습니다. Azure IaaS 및 AWS EC2에서이 혜택을 사용할 수 있습니다.

SQL Server에 대한 Azure 하이브리드 혜택은 두 가지 주요 영역에서 라이선스 이동과 다릅니다.

- 높은 수준으로 가상화된 워크로드를 Azure로 이동할 수 있는 경제적 이점을 제공합니다. SQL Server Enterprise Edition 고객은 매우 가상화 된 응용 프로그램에 대해 온-프레미스에서 소유 하는 모든 코어에 대해 범용 SKU의 Azure에서 4 개의 코어를 가져올 수 있습니다. 라이선스 이동성은 가상화 된 워크 로드를 클라우드로 이동 하는 데 특별 한 비용 혜택을 허용 하지 않습니다.
- SQL Server와 호환성이 높은 Azure (SQL Managed Instance)의 PaaS 대상에 대해 제공 됩니다.

#### <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>SQL Server에 대한 Azure 하이브리드 혜택의 특정 권한은 무엇인가요?

SQL Database 고객에 게는 SQL Server에 대 한 Azure 하이브리드 혜택와 관련 된 다음 권한이 있습니다.

|라이선스 사용 공간|SQL Server Azure 하이브리드 혜택은 무엇 인가요?|
|---|---|
|SA가 있는 SQL Server Enterprise Edition 핵심 고객|<li>범용 또는 중요 비즈니스용 SKU에 대 한 기본 요금을 지불할 수 있습니다.</li><br><li>온-프레미스의 1개 코어 = 범용 SKU의 4개 코어</li><br><li>온-프레미스의 1개 코어 = 중요 비즈니스 SKU의 1개 코어</li>|
|SA가 있는 SQL Server Standard Edition 핵심 고객|<li>범용 SKU에 대해서만 기본 요금을 지불할 수 있습니다.</li><br><li>온-프레미스의 1개 코어 = 범용 SKU의 1개 코어</li>|
|||


## <a name="next-steps"></a>다음 단계

- Azure SQL 배포 옵션 선택에 대 한 도움말 [은 AZURE sql에서 올바른 배포 옵션 선택](azure-sql-iaas-vs-paas-what-is-overview.md)을 참조 하세요.
- SQL Database와 SQL Managed Instance 기능을 비교 하려면 [sql Managed Instance 기능 SQL Database &](database/features-comparison.md)를 참조 하세요.
