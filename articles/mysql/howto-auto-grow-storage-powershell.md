---
title: 저장소 자동 증가-Azure PowerShell-Azure Database for MySQL
description: 이 문서에서는 Azure Database for MySQL에서 PowerShell을 사용 하 여 저장소 자동 증가를 사용 하도록 설정 하는 방법을 설명 합니다.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 4/28/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e8c9f6f66e484fbd9ebe5c15934936d6e5c59073
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94542034"
---
# <a name="auto-grow-storage-in-azure-database-for-mysql-server-using-powershell"></a>PowerShell을 사용 하 여 Azure Database for MySQL 서버에서 저장소 자동 증가

이 문서에서는 워크 로드에 영향을 주지 않고 확장 되도록 Azure Database for MySQL server 저장소를 구성 하는 방법을 설명 합니다.

저장소 자동 증가 [는 서버가 저장소 제한에 도달](./concepts-pricing-tiers.md#reaching-the-storage-limit) 하 고 읽기 전용이 되지 않도록 합니다. 100 GB 이하의 서버가 프로 비전 된 저장소의 경우 사용 가능한 공간이 10% 미만이 면 크기가 5gb 씩 증가 합니다. 프로 비전 된 저장소 수가 100 GB를 넘는 서버의 경우 사용 가능한 공간이 10gb 미만이 면 크기가 5% 증가 합니다. 최대 저장소 제한은 [Azure Database for MySQL 가격 책정 계층](./concepts-pricing-tiers.md#storage)의 저장소 섹션에 지정 된 대로 적용 됩니다.

> [!IMPORTANT]
> 저장소는 다운 되지 않고 확장만 가능 합니다.

## <a name="prerequisites"></a>필수 구성 요소

이 방법 가이드를 완료하려면 다음이 필요합니다.

- 로컬에 설치 되거나 브라우저에 [Azure Cloud Shell](https://shell.azure.com/) 된 [Az PowerShell 모듈](/powershell/azure/install-az-ps)
- [Azure Database for MySQL 서버](quickstart-create-mysql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Az.MySql PowerShell 모듈이 미리 보기에 있지만 `Install-Module -Name Az.MySql -AllowPrerelease` 명령을 사용하여 Az PowerShell 모듈과 별도로 설치해야 합니다.
> Az.MySql PowerShell 모듈이 일반 공급되면 이후 Az PowerShell 모듈 릴리스에 포함되며 Azure Cloud Shell 내에서 기본적으로 사용할 수 있습니다.

PowerShell을 로컬로 사용 하도록 선택 하는 경우 [AzAccount](/powershell/module/az.accounts/Connect-AzAccount) cmdlet을 사용 하 여 Azure 계정에 연결 합니다.

## <a name="enable-mysql-server-storage-auto-grow"></a>MySQL server storage 자동 증가 사용

다음 명령을 사용 하 여 서버에서 기존 서버에 저장소 자동 증가를 사용 하도록 설정 합니다.

```azurepowershell-interactive
Update-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -StorageAutogrow Enabled
```

다음 명령을 사용 하 여 새 서버를 만드는 동안 서버에서 저장소 자동 증가를 사용 하도록 설정 합니다.

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -StorageAutogrow Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [PowerShell을 사용 하 여 Azure Database for MySQL에서 읽기 복제본을 만들고 관리 하는 방법](howto-read-replicas-powershell.md)입니다.