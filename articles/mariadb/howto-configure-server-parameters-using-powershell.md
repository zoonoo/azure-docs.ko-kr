---
title: 서버 매개 변수 구성-Azure PowerShell-Azure Database for MariaDB
description: 이 문서에서는 PowerShell을 사용 하 여 Azure Database for MariaDB에서 서비스 매개 변수를 구성 하는 방법을 설명 합니다.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurepowershell
ms.topic: conceptual
ms.date: 5/26/2020
ms.openlocfilehash: 53ef9f66034d40e221a9ae4c8647cecdfe709107
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84726891"
---
# <a name="configure-server-parameters-in-azure-database-for-mariadb-using-powershell"></a>PowerShell을 사용 하 여 Azure Database for MariaDB에서 서버 매개 변수 구성

PowerShell을 사용 하 여 Azure Database for MariaDB 서버에 대 한 구성 매개 변수를 나열 하 고, 표시 하 고, 업데이트할 수 있습니다. 엔진 구성의 하위 집합은 서버 수준에서 노출되고 수정할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

이 방법 가이드를 완료하려면 다음이 필요합니다.

- 로컬에 설치 되거나 브라우저에 [Azure Cloud Shell](https://shell.azure.com/) 된 [Az PowerShell 모듈](https://docs.microsoft.com/powershell/azure/install-az-ps)
- [Azure Database for MariaDB 서버](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Az.MariaDb PowerShell 모듈이 미리 보기에 있지만 `Install-Module -Name Az.MariaDb -AllowPrerelease` 명령을 사용하여 Az PowerShell 모듈과 별도로 설치해야 합니다.
> Az.MariaDb PowerShell 모듈이 일반 공급되면 이후 Az PowerShell 모듈 릴리스에 포함되며 Azure Cloud Shell 내에서 기본적으로 사용할 수 있습니다.

PowerShell을 로컬로 사용 하도록 선택 하는 경우 [AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) cmdlet을 사용 하 여 Azure 계정에 연결 합니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="list-server-configuration-parameters-for-azure-database-for-mariadb-server"></a>Azure Database for MariaDB에 대한 서버 구성 매개 변수 나열

서버에 있는 수정 가능한 모든 매개 변수와 해당 값을 나열 하려면 cmdlet을 실행 `Get-AzMariaDbConfiguration` 합니다.

다음 예에서는 **myresourcegroup**리소스 그룹의 **mydemoserver** 서버에 대 한 서버 구성 매개 변수를 나열 합니다.

```azurepowershell-interactive
Get-AzMariaDbConfiguration -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

나열된 각 매개 변수의 정의를 보려면 [서버 시스템 변수](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html)에서 MariaDB 참조 섹션을 확인하세요.

## <a name="show-server-configuration-parameter-details"></a>서버 구성 매개 변수 세부 정보 표시

서버에 대 한 특정 구성 매개 변수에 대 한 세부 정보를 표시 하려면 cmdlet을 실행 하 `Get-AzMariaDbConfiguration` 고 **Name** 매개 변수를 지정 합니다.

이 예에서는 리소스 그룹 **myresourcegroup**에서 서버 **mydemoserver** 에 대 한 **저속 \_ 쿼리 \_ 로그** 서버 구성 매개 변수의 세부 정보를 보여 줍니다.

```azurepowershell-interactive
Get-AzMariaDbConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

## <a name="modify-a-server-configuration-parameter-value"></a>서버 구성 매개 변수 값 수정

또한 특정 서버 구성 매개 변수의 값을 수정할 수 있습니다. 그러면 MariaDB 서버 엔진에 대한 기본 구성 값이 업데이트됩니다. 구성을 업데이트 하려면 cmdlet을 사용 `Update-AzMariaDbConfiguration` 합니다.

리소스 그룹 **myresourcegroup**에서 서버 **mydemoserver** 의 **저속 \_ 쿼리 \_ 로그** 서버 구성 매개 변수를 업데이트 합니다.

```azurepowershell-interactive
Update-AzMariaDbConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver -Value On
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [PowerShell을 사용 하 여 Azure Database for MariaDB 서버에서 저장소를 자동으로 확장](howto-auto-grow-storage-powershell.md)합니다.
