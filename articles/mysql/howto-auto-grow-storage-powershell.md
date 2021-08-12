---
title: 자동 증가 스토리지 - Azure PowerShell - Azure Database for MySQL
description: 이 문서에서는 Azure Database for MySQL에서 PowerShell을 사용하여 자동 증가 스토리지를 사용하도록 설정하는 방법을 설명합니다.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 4/28/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e8c9f6f66e484fbd9ebe5c15934936d6e5c59073
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94542034"
---
# <a name="auto-grow-storage-in-azure-database-for-mysql-server-using-powershell"></a>PowerShell을 사용하여 Azure Database for MySQL 서버의 스토리지 자동 증가

이 문서에서는 워크로드에 영향을 주지 않고 확장되도록 Azure Database for MySQL 서버 스토리지를 구성할 수 있는 방법을 설명합니다.

스토리지 자동 증가는 서버가 [스토리지 제한에 도달](./concepts-pricing-tiers.md#reaching-the-storage-limit)하여 읽기 전용이 되지 않도록 합니다. 프로비저닝된 스토리지가 100GB 이하인 서버의 경우 사용 가능한 공간이 10% 미만이면 크기가 5GB씩 증가합니다. 프로비저닝된 스토리지가 100GB를 초과하는 서버의 경우 사용 가능한 공간이 10GB 미만이면 크기가 5%씩 증가합니다. 최대 스토리지 제한은 [ 가격 책정 계층](./concepts-pricing-tiers.md#storage)의 스토리지 섹션에 지정된 대로 적용됩니다.

> [!IMPORTANT]
> 스토리지는 스케일 다운이 아닌 스케일 업만 가능합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 방법 가이드를 완료하려면 다음이 필요합니다.

- 로컬로 설치된 [Az PowerShell 모듈](/powershell/azure/install-az-ps) 또는 브라우저의 [Azure Cloud Shell](https://shell.azure.com/)
- [Azure Database for MySQL 서버](quickstart-create-mysql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Az.MySql PowerShell 모듈이 미리 보기에 있지만 `Install-Module -Name Az.MySql -AllowPrerelease` 명령을 사용하여 Az PowerShell 모듈과 별도로 설치해야 합니다.
> Az.MySql PowerShell 모듈이 일반 공급되면 이후 Az PowerShell 모듈 릴리스에 포함되며 Azure Cloud Shell 내에서 기본적으로 사용할 수 있습니다.

PowerShell을 로컬로 사용하도록 선택한 경우 [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) cmdlet을 사용하여 Azure 계정에 연결합니다.

## <a name="enable-mysql-server-storage-auto-grow"></a>MySQL 서버 스토리지 자동 증가 사용

다음 명령을 사용하여 기존 서버에서 서버 자동 증가 스토리지를 사용하도록 설정합니다.

```azurepowershell-interactive
Update-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -StorageAutogrow Enabled
```

다음 명령을 사용하여 새 서버를 만드는 동안 서버 자동 증가 스토리지를 사용하도록 설정합니다.

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -StorageAutogrow Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [PowerShell을 사용하여 Azure Database for MySQL에서 읽기 복제본을 만들고 관리하는 방법](howto-read-replicas-powershell.md)