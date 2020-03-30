---
title: 사용자 정의 복원 지점
description: SQL 풀에 대한 복원 지점을 만드는 방법.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 07/03/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: d056b30c44ced5f3e8ce9041e2366290bee485da
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350139"
---
# <a name="user-defined-restore-points"></a>사용자 정의 복원 지점

이 문서에서는 PowerShell 및 Azure 포털을 사용하여 Azure Synapse Analytics의 SQL 풀에 대한 새 사용자 정의 복원 지점을 만드는 방법을 배웁니다.

## <a name="create-user-defined-restore-points-through-powershell"></a>PowerShell을 통해 사용자 정의 복원 지점 만들기

사용자 정의 복원 지점을 만들려면 [New-AzSqlDatabaseRestorePoint](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaserestorepoint?view=azps-2.4.0) PowerShell cmdlet을 사용합니다.

1. 시작하기 전에 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)을 설치해야 합니다.
2. PowerShell을 엽니다.
3. Azure 계정에 연결하고 사용자 계정과 연결된 모든 구독을 나열합니다.
4. 복원할 데이터베이스가 포함된 구독을 선택합니다.
5. 데이터 웨어하우스의 즉각적인 복사본을 위한 복원 지점을 만듭니다.

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

6. 모든 기존 복원 지점 목록을 참조하십시오.

```Powershell
# List all restore points
Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName
```

## <a name="create-user-defined-restore-points-through-the-azure-portal"></a>Azure 포털을 통해 사용자 정의 복원 지점 만들기

Azure 포털을 통해 사용자 정의 복원 지점을 만들 수도 있습니다.

1. [Azure 포털](https://portal.azure.com/) 계정에 로그인합니다.

2. 복원 지점을 만들려는 SQL 풀로 이동합니다.

3. 왼쪽 창에서 **개요를** 선택하고 **+ 새 복원 지점을**선택합니다. 새 복원 지점 단추가 활성화되어 있지 않으면 SQL 풀이 일시 중지되지 않았는지 확인합니다.

    ![새 복원 지점](./media/sql-data-warehouse-restore-points/creating-restore-point-01.png)

4. 사용자 정의 복원 지점의 이름을 지정하고 **적용을**클릭합니다. 사용자 정의 복원 지점의 기본 보존 기간은 7일입니다.

    ![복원 지점에 대한 이름](./media/sql-data-warehouse-restore-points/creating-restore-point-11.png)

## <a name="next-steps"></a>다음 단계

- [기존 SQL 풀 복원](sql-data-warehouse-restore-active-paused-dw.md)
- [삭제된 SQL 풀 복원](sql-data-warehouse-restore-deleted-dw.md)
- [지리적 백업 SQL 풀에서 복원](sql-data-warehouse-restore-from-geo-backup.md)

