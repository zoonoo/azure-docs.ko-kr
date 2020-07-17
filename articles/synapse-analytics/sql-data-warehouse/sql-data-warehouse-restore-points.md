---
title: 사용자 정의 복원 지점이
description: SQL 풀의 복원 지점을 만드는 방법
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 07/03/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 18d341db8538af12dbbabab4797459779a553e4e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85205002"
---
# <a name="user-defined-restore-points"></a>사용자 정의 복원 지점이

이 문서에서는 PowerShell 및 Azure Portal를 사용 하 여 Azure Synapse Analytics에서 SQL 풀에 대해 새 사용자 정의 복원 지점을 만드는 방법을 배웁니다.

## <a name="create-user-defined-restore-points-through-powershell"></a>PowerShell을 통해 사용자 정의 복원 시점 만들기

사용자 정의 복원 지점을 만들려면 [AzSqlDatabaseRestorePoint](/powershell/module/az.sql/new-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) PowerShell cmdlet을 사용 합니다.

1. 시작 하기 전에 [Azure PowerShell을 설치](/powershell/azure/overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)해야 합니다.
2. PowerShell을 엽니다.
3. Azure 계정에 연결하고 사용자 계정과 연결된 모든 구독을 나열합니다.
4. 복원할 데이터베이스가 포함된 구독을 선택합니다.
5. 데이터 웨어하우스의 즉각적인 복사본에 대 한 복원 지점을 만듭니다.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$Label = "<YourRestorePointLabel>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Create a restore point of the original database
New-AzSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -RestorePointLabel $Label

```

6. 모든 기존 복원 지점의 목록을 참조 하십시오.

```Powershell
# List all restore points
Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName
```

## <a name="create-user-defined-restore-points-through-the-azure-portal"></a>Azure Portal를 통해 사용자 정의 복원 시점 만들기

Azure Portal를 통해 사용자 정의 복원 지점이 만들어질 수도 있습니다.

1. [Azure Portal](https://portal.azure.com/) 계정에 로그인 합니다.

2. 복원 지점을 만들려는 SQL 풀로 이동 합니다.

3. 왼쪽 창에서 **개요** 를 선택 하 고 **+ 새 복원 지점**을 선택 합니다. 새 복원 지점 단추를 사용할 수 없는 경우 SQL 풀이 일시 중지 되지 않았는지 확인 합니다.

    ![새 복원 지점](./media/sql-data-warehouse-restore-points/creating-restore-point-01.png)

4. 사용자 정의 복원 지점의 이름을 지정 하 고 **적용**을 클릭 합니다. 사용자 정의 복원 지점의 기본 보존 기간은 7 일입니다.

    ![복원 지점에 대한 이름](./media/sql-data-warehouse-restore-points/creating-restore-point-11.png)

## <a name="next-steps"></a>다음 단계

- [기존 SQL 풀 복원](sql-data-warehouse-restore-active-paused-dw.md)
- [삭제 된 SQL 풀 복원](sql-data-warehouse-restore-deleted-dw.md)
- [지역 백업 SQL 풀에서 복원](sql-data-warehouse-restore-from-geo-backup.md)

