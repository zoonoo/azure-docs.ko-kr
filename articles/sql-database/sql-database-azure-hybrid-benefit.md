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
ms.date: 10/08/2019
ms.openlocfilehash: 17252f6544ee56647315dc44ace4db3e3abe9726
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821882"
---
# <a name="azure-hybrid-benefit"></a>Azure 하이브리드 혜택

VCore 기반 구매 모델의 프로 비전 된 계산 계층에서는 [SQL Server에 대 한 Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/)를 사용 하 여 SQL Database에 대 한 할인 된 요금으로 기존 라이선스를 교환할 수 있습니다. 이 Azure 혜택을 통해 소프트웨어 보증이 있는 온-프레미스 SQL Server 라이선스를 사용 하 여 Azure SQL Database 최대 30%까지 절약할 수 있습니다.

![가격 책정](./media/sql-database-service-tiers/pricing.png)


## <a name="choose-a-license-model"></a>라이선스 모델 선택

Azure 하이브리드 혜택를 사용 하면 SQL database 엔진 자체 (기본 계산 가격 책정)에 기존 SQL Server 라이선스를 사용 하 여 기본 Azure 인프라에 대해서만 비용을 지불 하도록 선택 하거나 기본 인프라와 SQL Server에 대해 비용을 지불할 수 있습니다. 라이선스 (라이선스 포함 가격).

Azure Portal 또는 다음 Api 중 하나를 사용 하 여 라이선스 모델을 선택 하거나 변경할 수 있습니다.

- PowerShell을 사용 하 여 라이선스 유형을 설정 하거나 업데이트 하려면 다음을 수행 합니다.

  - [New-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase)
  - [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)
  - [New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)
  - [Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)

- Azure CLI를 사용 하 여 라이선스 유형을 설정 하거나 업데이트 하려면 다음을 수행 합니다.

  - [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create)
  - [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update)
  - [az sql mi create](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create)
  - [az sql mi update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)

- REST API를 사용 하 여 라이선스 유형을 설정 하거나 업데이트 하려면 다음을 수행 합니다.

  - [Databases - Create 또는 Update](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)
  - [데이터베이스 - Update](https://docs.microsoft.com/rest/api/sql/databases/update)
  - [Managed Instances - Create 또는 Update](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)
  - [Managed Instances - Update](https://docs.microsoft.com/rest/api/sql/managedinstances/update)



## <a name="next-steps"></a>다음 단계

- SQL Database 배포 옵션 중에서 선택 하려면 [AZURE SQL에서 올바른 배포 옵션 선택](sql-database-paas-vs-sql-server-iaas.md)을 참조 하세요.
- SQL Database 기능에 대 한 비교는 [Azure SQL Database 기능](sql-database-features.md)을 참조 하세요.
