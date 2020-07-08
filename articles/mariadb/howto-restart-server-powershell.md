---
title: 서버 다시 시작-Azure PowerShell-Azure Database for MariaDB
description: 이 문서에서는 PowerShell을 사용 하 여 Azure Database for MariaDB 서버를 다시 시작 하는 방법을 설명 합니다.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 5/26/2020
ms.openlocfilehash: ff7a7b7d83089f575df1ac169556c2762dd674a7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84039404"
---
# <a name="restart-azure-database-for-mariadb-server-using-powershell"></a>PowerShell을 사용 하 여 Azure Database for MariaDB 서버 다시 시작

이 항목에서는 Azure Database for MariaDB 서버를 다시 시작하는 방법을 설명합니다. 유지 관리를 위해 서버를 다시 시작 해야 할 수 있으며,이로 인해 작업 중에 잠시 중단 됩니다.

서비스가 사용 중이면 서버 다시 시작이 차단 됩니다. 예를 들어, 서비스가 vCore 크기를 조정하는 것과 같이 이전에 요청된 작업을 처리할 수 있습니다.

다시 시작을 완료 하는 데 필요한 시간은 complete Iadb 복구 프로세스에 따라 달라 집니다. 다시 시작 시간을 줄이려면 다시 시작 하기 전에 서버에서 발생 하는 작업의 양을 최소화 하는 것이 좋습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 방법 가이드를 완료하려면 다음이 필요합니다.

- 로컬에 설치 되거나 브라우저에 [Azure Cloud Shell](https://shell.azure.com/) 된 [Az PowerShell 모듈](https://docs.microsoft.com/powershell/azure/install-az-ps)
- [Azure Database for MariaDB 서버](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Az.MariaDb PowerShell 모듈이 미리 보기에 있지만 `Install-Module -Name Az.MariaDb -AllowPrerelease` 명령을 사용하여 Az PowerShell 모듈과 별도로 설치해야 합니다.
> Az.MariaDb PowerShell 모듈이 일반 공급되면 이후 Az PowerShell 모듈 릴리스에 포함되며 Azure Cloud Shell 내에서 기본적으로 사용할 수 있습니다.

PowerShell을 로컬로 사용 하도록 선택 하는 경우 [AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) cmdlet을 사용 하 여 Azure 계정에 연결 합니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="restart-the-server"></a>서버 다시 시작

다음 명령을 사용 하 여 서버를 다시 시작 합니다.

```azurepowershell-interactive
Restart-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [PowerShell을 사용 하 여 Azure Database for MariaDB 서버 만들기](quickstart-create-mariadb-server-database-using-azure-powershell.md)
