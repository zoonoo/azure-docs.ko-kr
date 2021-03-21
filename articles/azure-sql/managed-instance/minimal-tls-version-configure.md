---
title: 최소 TLS 버전 관리 되는 인스턴스 구성
description: 관리 되는 인스턴스에 대해 최소 TLS 버전을 구성 하는 방법 알아보기
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
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92788403"
---
# <a name="configure-minimal-tls-version-in-azure-sql-managed-instance"></a>Azure SQL Managed Instance의 최소 TLS 버전 구성
최소 [tls (전송 계층 보안)](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) 버전 설정을 사용 하면 고객이 Azure SQL Managed Instance에서 사용 하는 tls 버전을 제어할 수 있습니다.

현재 TLS 1.0, 1.1 및 1.2를 지원합니다. 최소 TLS 버전을 설정하면 이후 최신 TLS 버전이 지원됩니다. 예를 들어 1.1 보다 큰 TLS 버전을 선택 합니다. TLS 1.1 및 1.2를 사용하는 연결만 수락되고 TLS 1.0은 거부됩니다. 최신 버전은 이전 버전에서 발견된 취약성에 대한 수정을 포함하고 Azure SQL Managed Instance에서 지원되는 최고 TLS 버전이므로 애플리케이션이 해당 버전을 지원하는지 확인하기 위해 테스트한 후 최소 TLS 버전을 1.2로 설정하는 것이 좋습니다.

이전 버전의 TLS를 사용 하는 응용 프로그램을 사용 하는 고객의 경우 응용 프로그램의 요구 사항에 따라 최소 TLS 버전을 설정 하는 것이 좋습니다. 암호화 되지 않은 연결을 사용 하 여 연결 하는 응용 프로그램을 사용 하는 고객의 경우 최소 TLS 버전을 설정 하지 않는 것이 좋습니다. 

자세한 내용은 [SQL Database 연결에 대 한 TLS 고려 사항](../database/connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity)을 참조 하세요.

최소 TLS 버전을 설정한 후에는 서버에 대 한 최소 TLS 버전 보다 낮은 TLS 버전을 사용 하는 클라이언트의 로그인 시도가 실패 하 고 다음과 같은 오류가 발생 합니다.

```output
Error 47072
Login failed with invalid TLS version
```

## <a name="set-minimal-tls-version-via-powershell"></a>PowerShell을 통해 최소 TLS 버전 설정

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager 모듈은 여전히 Azure SQL Database에서 지원되지만 향후의 모든 개발은 Az.Sql 모듈을 위한 것입니다. 이러한 cmdlet은 [AzureRM.Sql](/powershell/module/AzureRM.Sql/)을 참조하세요. Az 모듈 및 AzureRm 모듈의 명령에 대한 인수는 실질적으로 동일합니다. 다음 스크립트에는 [Azure PowerShell 모듈이](/powershell/azure/install-az-ps)필요 합니다.

다음 PowerShell 스크립트는 `Get` `Set` 인스턴스 수준에서 및 **최소 TLS 버전** 속성을 보여 줍니다.

```powershell
#Get the Minimal TLS Version property
(Get-AzSqlInstance -Name sql-instance-name -ResourceGroupName resource-group).MinimalTlsVersion

# Update Minimal TLS Version Property
Set-AzSqlInstance -Name sql-instance-name -ResourceGroupName resource-group -MinimalTlsVersion "1.2"
```

## <a name="set-minimal-tls-version-via-azure-cli"></a>Azure CLI를 통해 최소 TLS 버전 설정

> [!IMPORTANT]
> 이 섹션의 모든 스크립트에는 [Azure CLI](/cli/azure/install-azure-cli)필요 합니다.

### <a name="azure-cli-in-a-bash-shell"></a>Bash 셸에서 Azure CLI

다음 CLI 스크립트는 bash 셸에서 **최소 TLS 버전** 설정을 변경 하는 방법을 보여 줍니다.

```azurecli-interactive
# Get current setting for Minimal TLS Version
az sql mi show -n sql-instance-name -g resource-group --query "minimalTlsVersion"

# Update setting for Minimal TLS Version
az sql mi update -n sql-instance-name -g resource-group --set minimalTlsVersion="1.2"
```