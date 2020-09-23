---
title: Azure SQL Managed Instance에 대 한 관리 API 참조
description: Azure SQL Managed Instance의 관리 되는 인스턴스를 만들고 구성 하는 방법에 대해 알아봅니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: devx-track-azurecli
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 4627c094c3913d01f06c237b133e1ed0ea4ed2e0
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90969803"
---
# <a name="managed-api-reference-for-azure-sql-managed-instance"></a>Azure SQL Managed Instance에 대 한 관리 되는 API 참조
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure Portal, PowerShell, Azure CLI, REST API 및 Transact-sql을 사용 하 여 Azure SQL Managed Instance의 관리 되는 인스턴스를 만들고 구성할 수 있습니다. 이 문서에서는 관리 되는 인스턴스를 만들고 구성 하는 데 사용할 수 있는 함수 및 API에 대 한 개요를 찾을 수 있습니다.

## <a name="azure-portal-create-a-managed-instance"></a>Azure Portal: 관리 되는 인스턴스 만들기

관리 되는 인스턴스를 만드는 방법을 보여 주는 빠른 시작은 [빠른 시작: 관리 되는 인스턴스 만들기](instance-create-quickstart.md)를 참조 하세요.

## <a name="powershell-create-and-configure-managed-instances"></a>PowerShell: 관리 되는 인스턴스 만들기 및 구성

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager 모듈은 여전히 Azure SQL Database에서 지원되지만 향후의 모든 개발은 Az.Sql 모듈을 위한 것입니다. 이러한 cmdlet은 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)을 참조하세요. Az module 및 AzureRM 모듈의 명령에 대 한 인수는 실질적으로 동일 합니다.

Azure PowerShell을 사용하여 Managed Instance를 만들고 관리하려면 다음 PowerShell cmdlet을 사용합니다. PowerShell을 설치 하거나 업그레이드 해야 하 [는 경우 Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조 하세요.

> [!TIP]
> PowerShell 예제 스크립트에 대해서는 [빠른 시작 스크립트: PowerShell 라이브러리를 사용 하 여 관리 되는 인스턴스 만들기](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../quick-start-script-create-azure-sql-managed-instance-using-powershell/)를 참조 하세요.

| cmdlet | 설명 |
| --- | --- |
|[New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)|관리형 인스턴스를 만듭니다. |
|[Get-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstance)|관리 되는 인스턴스에 대 한 정보를 반환 합니다.|
|[Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)|관리 되는 인스턴스의 속성을 설정 합니다.|
|[AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstance)|관리 되는 인스턴스를 제거 합니다.|
|[AzSqlInstanceOperation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstanceoperation)|관리 되는 인스턴스 또는 특정 작업에 대해 수행 되는 관리 작업의 목록을 가져옵니다.|
|[AzSqlInstanceOperation](https://docs.microsoft.com/powershell/module/az.sql/stop-azsqlinstanceoperation)|관리 되는 인스턴스에서 수행 된 특정 관리 작업을 취소 합니다.|
|[AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstancedatabase)|SQL Managed Instance 데이터베이스를 만듭니다.|
|[AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabase)|SQL Managed Instance 데이터베이스에 대 한 정보를 반환 합니다.|
|[AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabase)|SQL Managed Instance 데이터베이스를 제거 합니다.|
|[Restore-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase)|SQL Managed Instance 데이터베이스를 복원 합니다.|

## <a name="azure-cli-create-and-configure-managed-instances"></a>Azure CLI: 관리 되는 인스턴스 만들기 및 구성

[Azure CLI](/cli/azure)를 사용 하 여 관리 되는 인스턴스를 만들고 구성 하려면 [SQL Managed Instance에 대해 다음 Azure CLI 명령을](/cli/azure/sql/mi)사용 합니다. [Azure Cloud Shell](/azure/cloud-shell/overview) 를 사용 하 여 브라우저에서 CLI를 실행 하거나 macos, Linux 또는 Windows에 [설치](/cli/azure/install-azure-cli) 합니다.

> [!TIP]
> Azure CLI 빠른 시작을 보려면 [Azure CLI를 사용하여 SQL Managed Instance 작업](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)을 참조하세요.

| cmdlet | 설명 |
| --- | --- |
|[az sql mi create](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create) |관리형 인스턴스를 만듭니다.|
|[az sql mi list](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-list)|사용 가능한 관리 되는 인스턴스를 나열 합니다.|
|[az sql mi show](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-show)|관리 되는 인스턴스의 세부 정보를 가져옵니다.|
|[az sql mi update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)|관리 되는 인스턴스를 업데이트 합니다.|
|[az sql mi delete](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-delete)|관리 되는 인스턴스를 제거 합니다.|
|[az sql mi op list](https://docs.microsoft.com/cli/azure/sql/mi/op#az_sql_mi_op_list)|관리 되는 인스턴스에서 수행 되는 관리 작업의 목록을 가져옵니다.|
|[az sql mi op show](https://docs.microsoft.com/cli/azure/sql/mi/op#az_sql_mi_op_show)|관리 되는 인스턴스에서 수행 된 특정 관리 작업을 가져옵니다.|
|[az sql mi op cancel](https://docs.microsoft.com/cli/azure/sql/mi/op#az_sql_mi_op_cancel)|관리 되는 인스턴스에서 수행 된 특정 관리 작업을 취소 합니다.|
|[az sql midb create](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-create) |관리 되는 데이터베이스를 만듭니다.|
|[az sql midb list](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-list)|사용 가능한 관리 되는 데이터베이스를 나열 합니다.|
|[az sql midb restore](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-restore)|관리 되는 데이터베이스를 복원 합니다.|
|[az sql midb delete](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-delete)|관리 되는 데이터베이스를 제거 합니다.|

## <a name="transact-sql-create-and-configure-instance-databases"></a>Transact-sql: 인스턴스 데이터베이스 만들기 및 구성

관리 되는 인스턴스를 만든 후에 인스턴스 데이터베이스를 만들고 구성 하려면 다음 T-sql 명령을 사용 합니다. Azure Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is), [Visual Studio Code](https://code.visualstudio.com/docs)또는 서버에 연결 하 고 transact-sql 명령을 전달할 수 있는 다른 프로그램을 사용 하 여 이러한 명령을 실행할 수 있습니다.

> [!TIP]
> Microsoft Windows에서 SQL Server Management Studio를 사용 하 여 관리 되는 인스턴스를 구성 하 고 연결 하는 방법을 보여 주는 빠른 시작은 [빠른 시작: AZURE sql Managed Instance에 연결 하도록 AZURE VM 구성](connect-vm-instance-configure.md) 및 [빠른 시작: 온-프레미스에서 azure sql Managed Instance에 지점 및 사이트 간 연결 구성](point-to-site-p2s-configure.md)을 참조 하세요.

> [!IMPORTANT]
> Transact-sql을 사용 하 여 관리 되는 인스턴스를 만들거나 삭제할 수 없습니다.

| 명령 | 설명 |
| --- | --- |
|[CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current&preserve-view=true)|SQL Managed Instance에서 새 인스턴스 데이터베이스를 만듭니다. 새 데이터베이스를 만들려면 master 데이터베이스에 연결해야 합니다.|
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current&preserve-view=true) |SQL Managed Instance의 인스턴스 데이터베이스를 수정 합니다.|

## <a name="rest-api-create-and-configure-managed-instances"></a>REST API: 관리 되는 인스턴스 만들기 및 구성

관리 되는 인스턴스를 만들고 구성 하려면 이러한 REST API 요청을 사용 합니다.

| 명령 | 설명 |
| --- | --- |
|[Managed Instances - Create 또는 Update](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)|관리 되는 인스턴스를 만들거나 업데이트 합니다.|
|[Managed Instances - Delete](https://docs.microsoft.com/rest/api/sql/managedinstances/delete)|관리 되는 인스턴스를 삭제 합니다.|
|[Managed Instances - Get](https://docs.microsoft.com/rest/api/sql/managedinstances/get)|관리 되는 인스턴스를 가져옵니다.|
|[Managed Instances - List](https://docs.microsoft.com/rest/api/sql/managedinstances/list)|구독에서 관리 되는 인스턴스 목록을 반환 합니다.|
|[Managed Instances - List By Resource Group](https://docs.microsoft.com/rest/api/sql/managedinstances/listbyresourcegroup)|리소스 그룹의 관리 되는 인스턴스 목록을 반환 합니다.|
|[Managed Instances - Update](https://docs.microsoft.com/rest/api/sql/managedinstances/update)|관리 되는 인스턴스를 업데이트 합니다.|
|[Managed Instance 작업-Managed Instance 별로 나열](https://docs.microsoft.com/rest/api/sql/managedinstanceoperations/listbymanagedinstance)|관리 되는 인스턴스에서 수행 되는 관리 작업의 목록을 가져옵니다.|
|[Managed Instance 작업-가져오기](https://docs.microsoft.com/rest/api/sql/managedinstanceoperations/get)|관리 되는 인스턴스에서 수행 된 특정 관리 작업을 가져옵니다.|
|[Managed Instance 작업-취소](https://docs.microsoft.com/rest/api/sql/managedinstanceoperations/cancel)|관리 되는 인스턴스에서 수행 된 특정 관리 작업을 취소 합니다.|

## <a name="next-steps"></a>다음 단계

- SQL Server 데이터베이스를 Azure로 마이그레이션하는 방법에 대한 자세한 내용은 [Azure SQL Database로 마이그레이션](../database/migrate-to-database-from-sql-server.md)을 참조하세요.
- 지원되는 기능에 대한 자세한 내용은 [기능](../database/features-comparison.md)을 참조하세요.
