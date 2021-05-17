---
title: 최소 TLS 버전 구성 - 관리되는 인스턴스
description: 관리되는 인스턴스를 위한 최소 TLS 버전을 구성하는 방법
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: ''
ms.date: 05/25/2020
ms.openlocfilehash: 17d430946f3cba1aa4680d1eaf8979fa4338bc22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92788403"
---
# <a name="configure-minimal-tls-version-in-azure-sql-managed-instance"></a>Azure SQL Managed Instance의 최소 TLS 버전 구성
최소 [TLS(전송 계층 보안)](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) 버전 설정을 사용하면 고객이 Azure SQL Managed Instance에서 사용하는 TLS 버전을 제어할 수 있습니다.

현재 TLS 1.0, 1.1 및 1.2를 지원합니다. 최소 TLS 버전을 설정하면 이후 최신 TLS 버전이 지원됩니다. 예를 들어 1.1보다 큰 TLS 버전을 선택하면 TLS 1.1 및 1.2를 사용하는 연결만 수락되고 TLS 1.0은 거부됩니다. 최신 버전은 이전 버전에서 발견된 취약성에 대한 수정을 포함하고 Azure SQL Managed Instance에서 지원되는 최고 TLS 버전이므로 애플리케이션이 해당 버전을 지원하는지 확인하기 위해 테스트한 후 최소 TLS 버전을 1.2로 설정하는 것이 좋습니다.

이전 버전의 TLS가 사용되는 애플리케이션을 사용하는 고객의 경우 애플리케이션의 요구 사항에 따라 최소 TLS 버전을 설정하는 것이 좋습니다. 암호화되지 않은 연결을 사용하여 연결하는 애플리케이션을 사용하는 고객의 경우 최소 TLS 버전을 설정하지 않는 것이 좋습니다. 

자세한 내용은 [SQL Database 연결에 대한 TLS 고려 사항](../database/connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity)을 참조하세요.

최소 TLS 버전을 설정한 후에는 서버의 최소 TLS 버전보다 낮은 TLS 버전을 사용하는 클라이언트에서 로그인 시도가 실패하고 다음 오류가 발생합니다.

```output
Error 47072
Login failed with invalid TLS version
```

## <a name="set-minimal-tls-version-via-powershell"></a>PowerShell을 통해 최소 TLS 버전 설정

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager 모듈은 여전히 Azure SQL Database에서 지원되지만 향후의 모든 개발은 Az.Sql 모듈을 위한 것입니다. 이러한 cmdlet은 [AzureRM.Sql](/powershell/module/AzureRM.Sql/)을 참조하세요. Az 모듈 및 AzureRm 모듈의 명령에 대한 인수는 실질적으로 동일합니다. 다음 스크립트에는 [Azure PowerShell 모듈](/powershell/azure/install-az-ps)이 필요합니다.

다음 PowerShell 스크립트는 인스턴스 수준에서 **최소 TLS 버전** 속성을 `Get`(가져오기) 및 `Set`(설정)하는 방법을 보여 줍니다.

```powershell
#Get the Minimal TLS Version property
(Get-AzSqlInstance -Name sql-instance-name -ResourceGroupName resource-group).MinimalTlsVersion

# Update Minimal TLS Version Property
Set-AzSqlInstance -Name sql-instance-name -ResourceGroupName resource-group -MinimalTlsVersion "1.2"
```

## <a name="set-minimal-tls-version-via-azure-cli"></a>Azure CLI를 통해 최소 TLS 버전 설정

> [!IMPORTANT]
> 이 섹션의 모든 스크립트에는 [Azure CLI](/cli/azure/install-azure-cli)가 필요합니다.

### <a name="azure-cli-in-a-bash-shell"></a>Bash 셸의 Azure CLI

다음 CLI 스크립트는 bash 셸에서 **최소 TLS 버전** 설정을 변경하는 방법을 보여 줍니다.

```azurecli-interactive
# Get current setting for Minimal TLS Version
az sql mi show -n sql-instance-name -g resource-group --query "minimalTlsVersion"

# Update setting for Minimal TLS Version
az sql mi update -n sql-instance-name -g resource-group --set minimalTlsVersion="1.2"
```