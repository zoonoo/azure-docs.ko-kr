---
title: 자동 증가 스토리지 - Azure PowerShell - Azure Database for PostgreSQL
description: 이 문서에서는 Azure Database for PostgreSQL에서 PowerShell을 사용하여 자동 증가 스토리지를 사용하도록 설정하는 방법을 설명합니다.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 06/08/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 00292d3f45554908b3a6e5c3477ad1c5031f5176
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107228047"
---
# <a name="auto-grow-storage-in-azure-database-for-postgresql-server-using-powershell"></a>PowerShell을 사용하는 Azure Database for PostgreSQL 서버의 자동 증가 스토리지

이 문서에서는 워크로드에 영향을 주지 않고 확장되도록 Azure Database for PostgreSQL 서버 스토리지를 구성하는 방법을 설명합니다.

스토리지 자동 증가는 서버가 [스토리지 제한에 도달](./concepts-pricing-tiers.md#reaching-the-storage-limit)하여 읽기 전용이 되지 않도록 합니다. 프로비전된 스토리지가 100GB 이하인 서버의 경우 사용 가능한 공간이 10% 미만이면 크기가 5GB씩 증가합니다. 프로비전된 스토리지가 100GB를 초과하는 서버의 경우 사용 가능한 공간이 10GB 미만이면 크기가 5%씩 증가합니다. 최대 스토리지 제한은 [Azure Database for PostgreSQL 가격 책정 계층](./concepts-pricing-tiers.md#storage)의 스토리지 섹션에 지정된 대로 적용됩니다.

> [!IMPORTANT]
> 스토리지는 스케일 다운이 아닌 스케일 업만 가능합니다.

## <a name="prerequisites"></a>필수 구성 요소

이 방법 가이드를 완료하려면 다음이 필요합니다.

- 로컬로 설치된 [Az PowerShell 모듈](/powershell/azure/install-az-ps) 또는 브라우저의 [Azure Cloud Shell](https://shell.azure.com/)
- [PostgreSQL용 Azure Database 서버](quickstart-create-postgresql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Az.PostgreSql PowerShell 모듈이 미리 보기에 있지만 `Install-Module -Name Az.PostgreSql -AllowPrerelease` 명령을 사용하여 Az PowerShell 모듈과 별도로 설치해야 합니다.
> Az.PostgreSql PowerShell 모듈이 일반 공급되면 이후 Az PowerShell 모듈 릴리스에 포함되며 Azure Cloud Shell 내에서 기본적으로 사용할 수 있습니다.

PowerShell을 로컬로 사용하도록 선택한 경우 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet을 사용하여 Azure 계정에 로그인합니다.

## <a name="enable-postgresql-server-storage-auto-grow"></a>PostgreSQL 서버 스토리지 자동 증가 사용

다음 명령을 사용하여 기존 서버에서 서버 자동 증가 스토리지를 사용하도록 설정합니다.

```azurepowershell-interactive
Update-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -StorageAutogrow Enabled
```

다음 명령을 사용하여 새 서버를 만드는 동안 서버 자동 증가 스토리지를 사용하도록 설정합니다.

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -StorageAutogrow Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [PowerShell을 사용하여 Azure Database for PostgreSQL에서 읽기 복제본을 만들고 관리하는 방법](howto-read-replicas-powershell.md).