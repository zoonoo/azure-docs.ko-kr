---
title: Azure CLI 스크립트 예제
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Azure SQL Database 및 Azure SQL Managed Instance를 만들고 관리하는 Azure CLI 스크립트 예제
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: overview-samples, mvc, sqldbrb=2, devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/03/2019
ms.openlocfilehash: 893ea607b322c2f9b2d8a4e8b5b087e21c476d98
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87497174"
---
# <a name="azure-cli-samples-for-azure-sql-database-and-sql-managed-instance"></a>Azure SQL Database 및 SQL Managed Instance의 Azure CLI 샘플 
 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

<a href="/cli/azure">Azure CLI</a>를 사용하여 Azure SQL Database와 SQL Managed Instance를 구성할 수 있습니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 항목에서 Azure CLI 버전 2.0 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

# <a name="azure-sql-database"></a>[Azure SQL Database](#tab/single-database)

다음 표에서는 Azure SQL Database의 단일 및 풀링된 데이터베이스를 관리하는 Azure CLI 스크립트 예제에 대한 링크를 포함합니다. 

|영역|Description|
|---|---|
|**Azure SQL Database에서 데이터베이스 만들기**||
| [단일 데이터베이스 만들기 및 방화벽 규칙 구성](scripts/create-and-configure-database-cli.md) | SQL Database를 만들고 서버 수준 방화벽 규칙을 구성합니다. |
| [탄력적 풀 만들기 및 풀링된 데이터베이스 이동](scripts/move-database-between-elastic-pools-cli.md) | 탄력적 풀을 만들고, 풀링된 데이터베이스를 이동하고, 컴퓨팅 크기를 변경합니다. |
|**Azure SQL Database에서 데이터베이스 크기 조정**||
| [단일 데이터베이스 크기 조정](scripts/monitor-and-scale-database-cli.md) | 데이터베이스의 크기 정보를 쿼리한 후 SQL Database의 데이터베이스를 다른 컴퓨팅 크기로 조정합니다. |
| [탄력적 풀 크기 조정](scripts/scale-pool-cli.md) | SQL 탄력적 풀을 다른 컴퓨팅 크기로 조정합니다. |
|**지역에서 복제 및 장애 조치(failover) 구성**||
| [장애 조치(failover) 그룹에 단일 데이터베이스 추가](scripts/add-database-to-failover-group-cli.md)| 데이터베이스 및 장애 조치(failover) 그룹을 만들고, 장애 조치(failover) 그룹에 데이터베이스를 추가한 다음, 보조 서버에 장애 조치(failover)를 테스트합니다. |
| [탄력적 풀에 대한 장애 조치(failover) 그룹 구성](../../sql-database/scripts/sql-database-add-elastic-pool-to-failover-group-cli.md) | 데이터베이스를 만들어서 탄력적 풀에 추가하고, 탄력적 풀을 장애 조치(failover) 그룹에 추가한 다음, 보조 서버에 장애 조치(failover)를 테스트합니다. |
| [활성 지역 복제를 사용하여 단일 데이터베이스 구성 및 장애 조치(failover)](../../sql-database/scripts/sql-database-setup-geodr-and-failover-database-cli.md)| Azure SQL Database의 데이터베이스에 대해 활성 지역 복제를 구성하고 보조 복제본으로 장애 조치(failover)합니다. |
| [활성 지역 복제를 사용하여 풀링된 데이터베이스 구성 및 장애 조치(Failover)](../../sql-database/scripts/sql-database-setup-geodr-and-failover-pool-cli.md)| 탄력적 풀의 데이터베이스에 대해 활성 지역 복제를 구성한 다음, 보조 복제본으로 장애 조치(failover)합니다. |
| **감사 및 위협 감지** |
| [감사 및 위협 감지 구성](../../sql-database/scripts/sql-database-auditing-and-threat-detection-cli.md)| Azure SQL Database의 데이터베이스에 대한 감사 및 위협 감지 정책을 구성합니다. |
| **데이터베이스 백업, 복원, 복사 및 가져오기**||
| [데이터베이스 백업](../../sql-database/scripts/sql-database-backup-database-cli.md)| SQL Database의 데이터베이스를 Azure 스토리지 백업에 백업합니다. |
| [데이터베이스 복원](../../sql-database/scripts/sql-database-restore-database-cli.md)| 지역 중복 백업에 있는 SQL Database의 데이터베이스를 복원하고 삭제된 데이터베이스를 최신 백업으로 복원합니다. |
| [새 서버에 데이터베이스 복사](../../sql-database/scripts/sql-database-copy-database-to-new-server-cli.md) | 새 서버의 SQL Database에 기존 데이터베이스의 복사본을 만듭니다. |
| [BACPAC 파일에서 데이터베이스 가져오기](../../sql-database/scripts/sql-database-import-from-bacpac-cli.md)| BACPAC 파일에서 SQL Database로 데이터베이스를 가져옵니다. |
|||

[단일 데이터베이스 Azure CLI API](single-database-manage.md#the-azure-cli)에 대해 자세히 알아봅니다.

# <a name="azure-sql-managed-instance"></a>[Azure SQL Managed Instance](#tab/managed-instance)

다음 표에서는 Azure SQL Managed Instance의 Azure CLI 스크립트 예제에 대한 링크를 포함합니다.

|영역|Description|
|---|---|
| **SQL Managed Instance 만들기**||
| [SQL Managed Instance 만들기](../../sql-database/scripts/sql-database-create-configure-managed-instance-cli.md)| SQL Managed Instance를 만듭니다. |
| **TDE(투명한 데이터 암호화) 구성**||
| [Azure Key Vault를 사용하여 SQL Managed Instance에서 투명한 데이터 암호화 관리](../../sql-database/scripts/transparent-data-encryption-byok-sql-managed-instance-cli.md)| 다양한 주요 시나리오에서 Azure Key Vault를 사용하여 SQL Managed Instance에서 TDE(투명한 데이터 암호화)를 구성합니다. |
|**장애 조치(failover) 그룹 구성**||
| [SQL Managed Instance에 대한 장애 조치(failover) 그룹 구성](../../sql-database/scripts/sql-database-add-managed-instance-to-failover-group-cli.md) | SQL Managed Instance 인스턴스 2개를 만들고, 장애 조치(failover) 그룹에 이를 추가한 다음, 기본 SQL Managed Instance에서 보조 SQL Managed Instance로의 장애 조치(failover)를 테스트합니다. |
|||

SQL Managed Instance 예제를 더 보려면 [만들기](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../create-azure-sql-managed-instance-using-azure-cli/), [업데이트](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../modify-azure-sql-database-managed-instance-using-azure-cli/), [데이터베이스 이동](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/), 스크립트 [작업](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)을 참조하세요.

[SQL Managed Instance Azure CLI API](../managed-instance/api-references-create-manage-instance.md#azure-cli-create-and-configure-managed-instances)에 대해 자세히 알아봅니다.

---
