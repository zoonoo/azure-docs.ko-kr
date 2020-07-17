---
title: 서버 매개 변수 구성-Azure PowerShell-Azure Database for PostgreSQL
description: 이 문서에서는 PowerShell을 사용 하 여 Azure Database for PostgreSQL에서 서비스 매개 변수를 구성 하는 방법을 설명 합니다.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurepowershell
ms.topic: how-to
ms.date: 06/08/2020
ms.openlocfilehash: c4c2e997df77a5dd854c3a1b266f390f1693afea
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86117103"
---
# <a name="customize-azure-database-for-postgresql-server-parameters-using-powershell"></a>PowerShell을 사용 하 여 Azure Database for PostgreSQL 서버 매개 변수 사용자 지정

PowerShell을 사용 하 여 Azure Database for PostgreSQL 서버에 대 한 구성 매개 변수를 나열 하 고, 표시 하 고, 업데이트할 수 있습니다. 엔진 구성의 하위 집합은 서버 수준에서 노출되고 수정할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

이 방법 가이드를 완료하려면 다음이 필요합니다.

- 로컬에 설치 되거나 브라우저에 [Azure Cloud Shell](https://shell.azure.com/) 된 [Az PowerShell 모듈](https://docs.microsoft.com/powershell/azure/install-az-ps)
- [PostgreSQL용 Azure Database 서버](quickstart-create-postgresql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Az.PostgreSql PowerShell 모듈이 미리 보기에 있지만 `Install-Module -Name Az.PostgreSql -AllowPrerelease` 명령을 사용하여 Az PowerShell 모듈과 별도로 설치해야 합니다.
> Az.PostgreSql PowerShell 모듈이 일반 공급되면 이후 Az PowerShell 모듈 릴리스에 포함되며 Azure Cloud Shell 내에서 기본적으로 사용할 수 있습니다.

PowerShell을 로컬로 사용 하도록 선택 하는 경우 [AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) cmdlet을 사용 하 여 Azure 계정에 연결 합니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="list-server-configuration-parameters-for-azure-database-for-postgresql-server"></a>PostgreSQL 서버용 Azure 데이터베이스에 대한 서버 구성 매개 변수 나열

서버에 있는 수정 가능한 모든 매개 변수와 해당 값을 나열 하려면 cmdlet을 실행 `Get-AzPostgreSqlConfiguration` 합니다.

다음 예에서는 **myresourcegroup**리소스 그룹의 **mydemoserver** 서버에 대 한 서버 구성 매개 변수를 나열 합니다.

```azurepowershell-interactive
Get-AzPostgreSqlConfiguration -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

나열 된 각 매개 변수에 대 한 정의는 [환경 변수에](https://www.postgresql.org/docs/12/libpq-envars.html)대 한 PostgreSQL 참조 섹션을 참조 하세요.

## <a name="show-server-configuration-parameter-details"></a>서버 구성 매개 변수 세부 정보 표시

서버에 대 한 특정 구성 매개 변수에 대 한 세부 정보를 표시 하려면 cmdlet을 실행 하 `Get-AzPostgreSqlConfiguration` 고 **Name** 매개 변수를 지정 합니다.

이 예에서는 리소스 그룹 **myresourcegroup**에서 서버 **mydemoserver** 에 대 한 **저속 \_ 쿼리 \_ 로그** 서버 구성 매개 변수의 세부 정보를 보여 줍니다.

```azurepowershell-interactive
Get-AzPostgreSqlConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

## <a name="modify-a-server-configuration-parameter-value"></a>서버 구성 매개 변수 값 수정

특정 서버 구성 매개 변수의 값을 수정할 수 있습니다. 그러면 PostgreSQL 서버 엔진에 대한 기본 구성 값이 업데이트됩니다. 구성을 업데이트 하려면 cmdlet을 사용 `Update-AzPostgreSqlConfiguration` 합니다.

리소스 그룹 **myresourcegroup**에서 서버 **mydemoserver** 의 **저속 \_ 쿼리 \_ 로그** 서버 구성 매개 변수를 업데이트 합니다.

```azurepowershell-interactive
Update-AzPostgreSqlConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver -Value On
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [PowerShell을 사용 하 여 Azure Database for PostgreSQL 서버에서 저장소를 자동으로 확장](howto-auto-grow-storage-powershell.md)합니다.
