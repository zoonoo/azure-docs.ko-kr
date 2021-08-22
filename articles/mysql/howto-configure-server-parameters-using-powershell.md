---
title: 서버 매개 변수 구성 - Azure PowerShell - Azure Database for MySQL
description: 이 문서에서는 PowerShell을 사용하여 MySQL용 Azure 데이터베이스에서 서비스 매개 변수를 구성하는 방법을 설명합니다.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurepowershell
ms.topic: how-to
ms.date: 10/1/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6ff10590e5f5b8de6ed988e08c3913a18c9235a2
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "122642170"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql-using-powershell"></a>PowerShell을 사용하여 Azure Database for MySQL에서 서버 매개 변수 구성

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

PowerShell을 사용하여 Azure Database for MySQL 서버에 대한 구성 매개 변수를 나열하고, 표시하며, 업데이트할 수 있습니다. 엔진 구성의 하위 집합은 서버 수준에서 노출되고 수정할 수 있습니다.

>[!Note]
> 서버 매개 변수는 서버 수준에서 전역적으로 업데이트될 수 있으며 [Azure CLI](./howto-configure-server-parameters-using-cli.md), [PowerShell](./howto-configure-server-parameters-using-powershell.md) 또는 [Azure Portal](./howto-server-parameters.md)을 사용합니다.

## <a name="prerequisites"></a>필수 구성 요소

이 방법 가이드를 완료하려면 다음이 필요합니다.

- 로컬로 설치된 [Az PowerShell 모듈](/powershell/azure/install-az-ps) 또는 브라우저의 [Azure Cloud Shell](https://shell.azure.com/)
- [Azure Database for MySQL 서버](quickstart-create-mysql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Az.MySql PowerShell 모듈이 미리 보기에 있지만 `Install-Module -Name Az.MySql -AllowPrerelease` 명령을 사용하여 Az PowerShell 모듈과 별도로 설치해야 합니다.
> Az.MySql PowerShell 모듈이 일반 공급되면 이후 Az PowerShell 모듈 릴리스에 포함되며 Azure Cloud Shell 내에서 기본적으로 사용할 수 있습니다.

PowerShell을 로컬로 사용하도록 선택한 경우 [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) cmdlet을 사용하여 Azure 계정에 연결합니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="list-server-configuration-parameters-for-azure-database-for-mysql-server"></a>Azure Database for MySQL에 대한 서버 구성 매개 변수 나열

서버의 수정 가능한 모든 매개 변수와 해당 값을 나열하려면 `Get-AzMySqlConfiguration` cmdlet을 실행합니다.

다음 예제에는 **myresourcegroup** 리소스 그룹의 **mydemoserver** 서버에 대한 서버 구성 매개 변수가 나열되어 있습니다.

```azurepowershell-interactive
Get-AzMySqlConfiguration -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

나열된 각 매개 변수의 정의를 보려면 [서버 시스템 변수](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html)에서 MySQL 참조 섹션을 참조하세요.

## <a name="show-server-configuration-parameter-details"></a>서버 구성 매개 변수 세부 정보 표시

서버의 특정 구성 매개 변수에 대한 세부 정보를 표시하려면 `Get-AzMySqlConfiguration` cmdlet을 실행하고 **Name** 매개 변수를 지정합니다.

이 예에서는 **myresourcegroup** 리소스 그룹에 있는 **mydemoserver** 서버에 대한 **slow\_query\_log** 서버 구성 매개 변수의 세부 정보를 보여줍니다.

```azurepowershell-interactive
Get-AzMySqlConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

## <a name="modify-a-server-configuration-parameter-value"></a>서버 구성 매개 변수 값 수정

특정 서버 구성 매개 변수의 값을 수정할 수 있습니다. 그러면 MySQL 서버 엔진에 대한 기본 구성 값이 업데이트됩니다. 구성을 업데이트하려면 `Update-AzMySqlConfiguration` cmdlet을 사용합니다.

**myresourcegroup** 리소스 그룹에 있는 **mydemoserver** 서버의 **slow\_query\_log** 서버 구성 매개 변수를 업데이트하려면 다음을 실행합니다.

```azurepowershell-interactive
Update-AzMySqlConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver -Value On
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [PowerShell을 사용하는 Azure Database for MySQL 서버의 자동 증가 스토리지](howto-auto-grow-storage-powershell.md)입니다.
