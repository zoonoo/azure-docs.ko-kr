---
title: 저장소 자동 증가-Azure PowerShell-Azure Database for PostgreSQL
description: 이 문서에서는 Azure Database for PostgreSQL에서 PowerShell을 사용 하 여 저장소 자동 증가를 사용 하도록 설정 하는 방법을 설명 합니다.
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: how-to
ms.date: 06/08/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a186c6e176cde20474cdf4772b8724ad3f356a3e
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87493621"
---
# <a name="auto-grow-storage-in-azure-database-for-postgresql-server-using-powershell"></a>PowerShell을 사용 하 여 Azure Database for PostgreSQL 서버에서 저장소 자동 증가

이 문서에서는 워크 로드에 영향을 주지 않고 확장 되도록 Azure Database for PostgreSQL server 저장소를 구성 하는 방법을 설명 합니다.

저장소 자동 증가 [는 서버가 저장소 제한에 도달](/azure/postgresql/concepts-pricing-tiers#reaching-the-storage-limit) 하 고 읽기 전용이 되지 않도록 합니다. 100 GB 이하의 서버가 프로 비전 된 저장소의 경우 사용 가능한 공간이 10% 미만이 면 크기가 5gb 씩 증가 합니다. 프로 비전 된 저장소 수가 100 GB를 넘는 서버의 경우 사용 가능한 공간이 10gb 미만이 면 크기가 5% 증가 합니다. 최대 저장소 제한은 [Azure Database for PostgreSQL 가격 책정 계층](/azure/postgresql/concepts-pricing-tiers#storage)의 저장소 섹션에 지정 된 대로 적용 됩니다.

> [!IMPORTANT]
> 저장소는 다운 되지 않고 확장만 가능 합니다.

## <a name="prerequisites"></a>필수 조건

이 방법 가이드를 완료하려면 다음이 필요합니다.

- 로컬에 설치 되거나 브라우저에 [Azure Cloud Shell](https://shell.azure.com/) 된 [Az PowerShell 모듈](https://docs.microsoft.com/powershell/azure/install-az-ps)
- [PostgreSQL용 Azure Database 서버](quickstart-create-postgresql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Az.PostgreSql PowerShell 모듈이 미리 보기에 있지만 `Install-Module -Name Az.PostgreSql -AllowPrerelease` 명령을 사용하여 Az PowerShell 모듈과 별도로 설치해야 합니다.
> Az.PostgreSql PowerShell 모듈이 일반 공급되면 이후 Az PowerShell 모듈 릴리스에 포함되며 Azure Cloud Shell 내에서 기본적으로 사용할 수 있습니다.

PowerShell을 로컬로 사용 하도록 선택 하는 경우 [AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) cmdlet을 사용 하 여 Azure 계정에 연결 합니다.

## <a name="enable-postgresql-server-storage-auto-grow"></a>PostgreSQL 서버 저장소 자동 증가 사용

다음 명령을 사용 하 여 서버에서 기존 서버에 저장소 자동 증가를 사용 하도록 설정 합니다.

```azurepowershell-interactive
Update-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -StorageAutogrow Enabled
```

다음 명령을 사용 하 여 새 서버를 만드는 동안 서버에서 저장소 자동 증가를 사용 하도록 설정 합니다.

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -StorageAutogrow Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [PowerShell을 사용 하 여 Azure Database for PostgreSQL에서 읽기 복제본을 만들고 관리 하는 방법](howto-read-replicas-powershell.md)입니다.
