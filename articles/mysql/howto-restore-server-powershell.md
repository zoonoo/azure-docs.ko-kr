---
title: 백업 및 복원-Azure PowerShell-Azure Database for MySQL
description: Azure PowerShell를 사용 하 여 Azure Database for MySQL에서 서버를 백업 및 복원 하는 방법에 대해 알아봅니다.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurepowershel
ms.topic: conceptual
ms.date: 4/28/2020
ms.openlocfilehash: 1e63a6e57e1dc929c78e5278df6ef0e4ab2a17d7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82230848"
---
# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-mysql-using-powershell"></a>PowerShell을 사용 하 여 Azure Database for MySQL에서 서버를 백업 및 복원 하는 방법

Azure Database for MySQL 서버는 정기적으로 백업 되어 복원 기능을 사용 하도록 설정 합니다. 이 기능을 사용하면 서버 및 모든 데이터베이스를 이전 특정 시점으로 새 서버에 복원할 수 있습니다.

## <a name="prerequisites"></a>전제 조건

이 방법 가이드를 완료하려면 다음이 필요합니다.

- 로컬에 설치 되거나 브라우저에 [Azure Cloud Shell](https://shell.azure.com/) 된 [Az PowerShell 모듈](/powershell/azure/install-az-ps)
- [Azure Database for MySQL 서버](quickstart-create-mysql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Az PowerShell module은 preview에 있지만 다음 명령을 사용 하 여 Az PowerShell 모듈에서 별도로 설치 해야 합니다 `Install-Module -Name Az.MySql -AllowPrerelease`.
> Az PowerShell module은 일반적으로 사용할 수 있게 되 면 이후 Az PowerShell 모듈 릴리스에 포함 되며 Azure Cloud Shell 내에서 기본적으로 사용할 수 있습니다.

PowerShell을 로컬로 사용 하도록 선택 하는 경우 [AzAccount](/powershell/module/az.accounts/Connect-AzAccount) cmdlet을 사용 하 여 Azure 계정에 연결 합니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-backup-configuration"></a>백업 구성 설정

서버를 만들 때 로컬 중복 또는 지리 중복 백업을 위한 서버 구성 중에서 선택할 수 있습니다.

> [!NOTE]
> 서버를 만든 후에는 지리적으로 중복 되거나 로컬에서 중복 된 중복성 종류를 변경할 수 없습니다.

`New-AzMySqlServer` 명령을 통해 서버를 만드는 동안 **GeoRedundantBackup** 매개 변수는 백업 중복성 옵션을 결정 합니다. **사용 하도록 설정**하면 지역 중복 백업이 수행 됩니다. 또는 **사용 하지 않도록 설정**된 경우 로컬 중복 백업이 수행 됩니다.

백업 보존 기간은 **backup보존** 기간 매개 변수를 통해 설정 됩니다.

서버를 만드는 동안 이러한 값을 설정 하는 방법에 대 한 자세한 내용은 [PowerShell을 사용 하 여 Azure Database for MySQL 서버 만들기](quickstart-create-mysql-server-database-using-azure-powershell.md)를 참조 하세요.

서버의 백업 보존 기간은 다음과 같이 변경할 수 있습니다.

```azurepowershell-interactive
Update-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -BackupRetentionDay 10
```

앞의 예제는 mydemoserver의 백업 보존 기간을 10일로 변경합니다.

백업 보존 기간은 사용 가능한 백업을 기반으로 하기 때문에 지정 시간 복원을 검색할 수 있는 정도를 제어 합니다. 특정 시점 복원은 다음 섹션에서 자세히 설명합니다.

## <a name="server-point-in-time-restore"></a>서버 지정 시간 복원

서버를 이전 시점으로 복원할 수 있습니다. 복원 된 데이터는 새 서버에 복사 되 고 기존 서버는 변경 되지 않은 상태로 유지 됩니다. 예를 들어 테이블을 실수로 삭제 한 경우에는 놓기가 발생 한 시간으로 복원할 수 있습니다. 그런 다음 서버의 복원된 복사본에서 누락된 테이블 및 데이터를 검색할 수 있습니다.

서버를 복원 하려면 `Restore-AzMySqlServer` PowerShell cmdlet을 사용 합니다.

### <a name="run-the-restore-command"></a>복원 명령 실행

서버를 복원 하려면 PowerShell에서 다음 예제를 실행 합니다.

```azurepowershell-interactive
$restorePointInTime = (Get-Date).AddMinutes(-10)
Get-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzMySqlServer -Name mydemoserver-restored -ResourceGroupName myresourcegroup -RestorePointInTime $restorePointInTime -UsePointInTimeRestore
```

Cmdlet의 PointInTimeRestore 매개 변수 집합에는 다음 매개 변수가 필요 합니다. **PointInTimeRestore** `Restore-AzMySqlServer`

| 설정 | 제안 값 | 설명  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  원본 서버가 있는 리소스 그룹입니다.  |
| 속성 | mydemoserver-restored | 복원 명령에 의해 만들어진 새 서버의 이름입니다. |
| RestorePointInTime | 2020-03-13T13:59:00Z | 복원할 특정 시점을 선택 합니다. 이 날짜 및 시간은 원본 서버의 백업 보존 기간 내에 있어야 합니다. ISO8601 날자 및 시간 형식을 사용합니다. 예를 들어 **2020-03-13T05:59:00-08:00**과 같은 고유한 현지 표준 시간대를 사용할 수 있습니다. UTC 줄루어 형식을 사용할 수도 있습니다 (예: **2018-03-13T13:59:00Z**). |
| UsePointInTimeRestore | `<SwitchParameter>` | 지정 시간 모드를 사용 하 여 복원 합니다. |

서버를 이전 지정 시간으로 복원 하면 새 서버가 만들어집니다. 지정 된 시점에서 원래 서버와 해당 데이터베이스가 새 서버에 복사 됩니다.

복원된 서버에 대한 위치 및 가격 책정 계층 값은 원본 서버와 같게 유지됩니다.

복원 프로세스가 완료된 후 새 서버를 찾아 데이터가 예상대로 복원되었는지 확인합니다. 새 서버에는 복원이 시작 된 시점에 기존 서버에 유효한 동일한 서버 관리자 로그인 이름과 암호가 있습니다. 암호는 새 서버의 **개요** 페이지에서 변경할 수 있습니다.

복원 중에 만들어진 새 서버에는 원본 서버에 있던 VNet 서비스 끝점이 없습니다. 이러한 규칙은 새 서버에 대해 별도로 설정 해야 합니다. 원본 서버의 방화벽 규칙이 복원 됩니다.

## <a name="geo-restore"></a>지역 복원

서버를 지리적으로 중복 백업 하도록 구성한 경우 기존 서버의 백업에서 새 서버를 만들 수 있습니다. 이 새 서버는 Azure Database for MySQL을 사용할 수 있는 모든 지역에서 만들 수 있습니다.

지역 중복 백업을 사용 하 여 서버를 만들려면 **UseGeoRestore** 매개 변수와 `Restore-AzMySqlServer` 함께 명령을 사용 합니다.

> [!NOTE]
> 서버가 처음 생성될 때는 지역 복원에 즉시 사용 가능하지 않을 수 있습니다. 필요한 메타데이터를 채우는 데 몇 시간 정도 걸릴 수 있습니다.

서버를 지역 복원 하려면 PowerShell에서 다음 예제를 실행 합니다.

```azurepowershell-interactive
Get-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzMySqlServer -Name mydemoserver-georestored -ResourceGroupName myresourcegroup -Location eastus -Sku GP_Gen5_8 -UseGeoRestore
```

이 예에서는 **myresourcegroup**에 속하는 미국 동부 지역에 **mydemoserver-georestored** 라는 새 서버를 만듭니다. 이 서버는 vCore가 8개인 범용 5세대 서버입니다. 서버는 **mydemoserver**의 지역 중복 백업 ( **myresourcegroup**리소스 그룹에도)에서 만들어집니다.

기존 서버와 다른 리소스 그룹에 새 서버를 만들려면 다음 예제와 같이 **ResourceGroupName** 매개 변수를 사용 하 여 새 리소스 그룹 이름을 지정 합니다.

```azurepowershell-interactive
Get-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzMySqlServer -Name mydemoserver-georestored -ResourceGroupName newresourcegroup -Location eastus -Sku GP_Gen5_8 -UseGeoRestore
```

Cmdlet의 GeoRestore 매개 변수 집합에는 다음 매개 변수가 필요 합니다. **GeoRestore** `Restore-AzMySqlServer`

| 설정 | 제안 값 | 설명  |
| --- | --- | --- |
|ResourceGroupName | myresourcegroup | 새 서버가 속한 리소스 그룹의 이름입니다.|
|속성 | mydemoserver-georestored | 새 서버의 이름입니다. |
|위치 | eastus | 새 서버의 위치입니다. |
|UseGeoRestore | `<SwitchParameter>` | 지역 모드를 사용 하 여 복원 합니다. |

지역 복원을 사용 하 여 새 서버를 만들 때 **Sku** 매개 변수를 지정 하지 않으면 원본 서버와 동일한 저장소 크기 및 가격 책정 계층을 상속 합니다.

복원 프로세스가 완료된 후 새 서버를 찾아 데이터가 예상대로 복원되었는지 확인합니다. 새 서버에는 복원이 시작 된 시점에 기존 서버에 유효한 동일한 서버 관리자 로그인 이름과 암호가 있습니다. 암호는 새 서버의 **개요** 페이지에서 변경할 수 있습니다.

복원 중에 만들어진 새 서버에는 원본 서버에 있던 VNet 서비스 끝점이 없습니다. 이러한 규칙은이 새 서버에 대해 별도로 설정 해야 합니다. 원본 서버의 방화벽 규칙이 복원 됩니다.

## <a name="next-steps"></a>다음 단계

- 서비스의 [백업](concepts-backup.md) 에 대 한 자세한 정보
- [복제본](concepts-read-replicas.md) 에 대해 알아보기
- [비즈니스 연속성](concepts-business-continuity.md) 옵션에 대 한 자세한 정보
