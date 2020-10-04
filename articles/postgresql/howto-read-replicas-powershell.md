---
title: 읽기 복제본 관리-Azure PowerShell-Azure Database for PostgreSQL
description: PowerShell을 사용 하 여 Azure Database for PostgreSQL에서 읽기 복제본을 설정 하 고 관리 하는 방법을 알아봅니다.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 06/08/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 26c6f70f92e4c372c0ff6afbcbb3c0bb284e2f6c
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2020
ms.locfileid: "91704806"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-postgresql-using-powershell"></a>PowerShell을 사용 하 여 Azure Database for PostgreSQL에서 읽기 복제본을 만들고 관리 하는 방법

이 문서에서는 PowerShell을 사용 하 여 Azure Database for PostgreSQL 서비스에서 읽기 복제본을 만들고 관리 하는 방법에 대해 알아봅니다. 읽기 복제본에 대한 자세한 내용은 [개요](concepts-read-replicas.md)를 참조하세요.

## <a name="azure-powershell"></a>Azure PowerShell

PowerShell을 사용 하 여 읽기 복제본을 만들고 관리할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 방법 가이드를 완료하려면 다음이 필요합니다.

- 로컬에 설치 되거나 브라우저에 [Azure Cloud Shell](https://shell.azure.com/) 된 [Az PowerShell 모듈](https://docs.microsoft.com/powershell/azure/install-az-ps)
- [PostgreSQL용 Azure Database 서버](quickstart-create-postgresql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Az.PostgreSql PowerShell 모듈이 미리 보기에 있지만 `Install-Module -Name Az.PostgreSql -AllowPrerelease` 명령을 사용하여 Az PowerShell 모듈과 별도로 설치해야 합니다.
> Az.PostgreSql PowerShell 모듈이 일반 공급되면 이후 Az PowerShell 모듈 릴리스에 포함되며 Azure Cloud Shell 내에서 기본적으로 사용할 수 있습니다.

PowerShell을 로컬로 사용 하도록 선택 하는 경우 [AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) cmdlet을 사용 하 여 Azure 계정에 연결 합니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> 복제본 읽기 기능은 범용 또는 메모리 액세스에 최적화 된 가격 책정 계층의 Azure Database for PostgreSQL 서버에 대해서만 사용할 수 있습니다. 주 서버가 이러한 가격 책정 계층 중 하나에 있는지 확인 합니다.

### <a name="create-a-read-replica"></a>읽기 복제본 만들기

다음 명령을 사용하여 읽기 복제본 서버를 만들 수 있습니다.

```azurepowershell-interactive
Get-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  New-AzPostgreSqlServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

`New-AzPostgreSqlServerReplica` 명령에는 다음과 같은 매개 변수가 필요합니다.

| 설정 | 예제 값 | Description  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  복제본 서버가 생성 되는 리소스 그룹입니다.  |
| Name | mydemoreplicaserver | 만들어지는 새 복제본 서버의 이름입니다. |

지역 간 읽기 복제본을 만들려면 **Location** 매개 변수를 사용 합니다. 다음 예에서는 **미국 서 부** 지역에 복제본을 만듭니다.

```azurepowershell-interactive
Get-AzPostgreSqlServer -Name mrdemoserver -ResourceGroupName myresourcegroup |
  New-AzMariaDServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup -Location westus
```

복제본을 만들 수 있는 지역에 대해 자세히 알아보려면 [읽기 복제본 개념 문서](concepts-read-replicas.md)를 참조하세요.

기본적으로 **Sku** 매개 변수를 지정 하지 않으면 읽기 복제본이 주 복제본과 동일한 서버 구성으로 만들어집니다.

> [!NOTE]
> 복제본이 마스터를 유지할 수 있도록 복제본 서버의 구성을 주 서버와 같거나 큰 값으로 유지 하는 것이 좋습니다.

### <a name="list-replicas-for-a-primary-server"></a>주 서버에 대 한 복제본 나열

지정 된 주 서버에 대 한 모든 복제본을 보려면 다음 명령을 실행 합니다.

```azurepowershell-interactive
Get-AzMariaDReplica -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

`Get-AzMariaDReplica` 명령에는 다음과 같은 매개 변수가 필요합니다.

| 설정 | 예제 값 | Description  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  복제본 서버가 만들어지는 리소스 그룹입니다.  |
| ServerName | mydemoserver | 주 서버의 이름 또는 ID입니다. |

### <a name="delete-a-replica-server"></a>복제본 서버 삭제

읽기 복제본 서버 삭제는 cmdlet을 실행 하 여 수행할 수 있습니다 `Remove-AzPostgreSqlServer` .

```azurepowershell-interactive
Remove-AzPostgreSqlServer -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

### <a name="delete-a-primary-server"></a>주 서버 삭제

> [!IMPORTANT]
> 주 서버를 삭제 하면 모든 복제본 서버에 대 한 복제를 중지 하 고 주 서버 자체를 삭제 합니다. 그러면 복제본 서버는 읽기와 쓰기를 모두 지원하는 독립 실행형 서버로 설정됩니다.

주 서버를 삭제 하려면 cmdlet을 실행할 수 있습니다 `Remove-AzPostgreSqlServer` .

```azurepowershell-interactive
Remove-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [PowerShell을 사용 하 여 Azure Database for PostgreSQL 서버 다시 시작](howto-restart-server-powershell.md)
