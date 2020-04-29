---
title: 저장소 자동 증가-Azure PowerShell-Azure Database for MySQL
description: 이 문서에서는 Azure Database for MySQL에서 PowerShell을 사용 하 여 저장소 자동 증가를 사용 하도록 설정 하는 방법을 설명 합니다.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/28/2020
ms.openlocfilehash: 09431a6690be751324bb796415d308d3b7727dad
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82254097"
---
# <a name="auto-grow-storage-in-azure-database-for-mysql-storage-using-powershell"></a>PowerShell을 사용 하 여 Azure Database for MySQL 저장소의 저장소 자동 증가

이 문서에서는 워크 로드에 영향을 주지 않고 확장 되도록 Azure Database for MySQL server 저장소를 구성 하는 방법을 설명 합니다.

저장소 자동 증가 [는 서버가 저장소 제한에 도달](/azure/mysql/concepts-pricing-tiers#reaching-the-storage-limit) 하 고 읽기 전용이 되지 않도록 합니다. 100 GB 이하의 서버가 프로 비전 된 저장소의 경우 사용 가능한 공간이 10% 미만이 면 크기가 5gb 씩 증가 합니다. 프로 비전 된 저장소 수가 100 GB를 넘는 서버의 경우 사용 가능한 공간이 10gb 미만이 면 크기가 5% 증가 합니다. 최대 저장소 제한은 [Azure Database for MySQL 가격 책정 계층](/azure/mysql/concepts-pricing-tiers#storage)의 저장소 섹션에 지정 된 대로 적용 됩니다.

> [!IMPORTANT]
> 저장소는 다운 되지 않고 확장만 가능 합니다.

## <a name="prerequisites"></a>전제 조건

이 방법 가이드를 완료하려면 다음이 필요합니다.

- 로컬에 설치 되거나 브라우저에 [Azure Cloud Shell](https://shell.azure.com/) 된 [Az PowerShell 모듈](/powershell/azure/install-az-ps)
- [Azure Database for MySQL 서버](quickstart-create-mysql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Az PowerShell module은 preview에 있지만 다음 명령을 사용 하 여 Az PowerShell 모듈에서 별도로 설치 해야 합니다 `Install-Module -Name Az.MySql -AllowPrerelease`.
> Az PowerShell module은 일반적으로 사용할 수 있게 되 면 이후 Az PowerShell 모듈 릴리스에 포함 되며 Azure Cloud Shell 내에서 기본적으로 사용할 수 있습니다.

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

[메트릭에 대 한 경고를 만드는 방법](howto-alert-on-metric.md)에 대해 알아봅니다.