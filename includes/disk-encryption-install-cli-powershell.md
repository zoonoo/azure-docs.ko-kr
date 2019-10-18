---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 10/06/2019
ms.author: mbaldwin
ms.custom: include file
ms.openlocfilehash: 05794a046fdcb15a91145a75717a6a454d15a8da
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72511499"
---
Azure Disk Encryption [Azure CLI](/cli/azure) 및 [Azure PowerShell](/powershell/azure/new-azureps-module-az)를 통해 사용 하도록 설정 하 고 관리할 수 있습니다. 이렇게 하려면 도구를 로컬로 설치 하 고 Azure 구독에 연결 해야 합니다.

### <a name="azure-cli"></a>Azure CLI

[Azure CLI 2.0](/cli/azure)은 Azure 리소스를 관리하기 위한 명령줄 도구입니다. CLI는 데이터를 유연하게 쿼리하고, 장기 실행 작업을 비차단 프로세스로 지원하고, 쉽게 스크립팅할 수 있도록 설계되었습니다. [Azure CLI 설치](/cli/azure/install-azure-cli?view=azure-cli-latest)의 단계에 따라 로컬로 설치할 수 있습니다.

Azure CLI를 사용 하 여 [Azure 계정에 로그인](/cli/azure/authenticate-azure-cli)하려면 [az login](/cli/azure/reference-index?view=azure-cli-latest#az-login) 명령을 사용 합니다.

```azurecli
az login
```

로그인할 테넌트를 선택하려면 다음을 사용합니다.
    
```azurecli
az login --tenant <tenant>
```

구독이 여러 개이고 특정 구독을 지정하려는 경우 [az account list](/cli/azure/account#az-account-list)를 사용하여 구독 목록을 가져오고 [az account set](/cli/azure/account#az-account-set)으로 지정합니다.
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

자세한 내용은 [Azure CLI 2.0 시작](/cli/azure/get-started-with-azure-cli)을 참조하세요. 

### <a name="azure-powershell"></a>Azure PowerShell
[Azure PowerShell az module](/powershell/azure/new-azureps-module-az) 은 [Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview) 모델을 사용 하 여 Azure 리소스를 관리 하는 cmdlet 집합을 제공 합니다. [Azure Cloud Shell](/azure/cloud-shell/overview)를 사용 하 여 브라우저에서 사용 하거나 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)의 지침을 사용 하 여 로컬 컴퓨터에 설치할 수 있습니다. 

이미 로컬에 설치되어있는 경우 최신 버전의 Azure PowerShell SDK 버전을 사용하여 Azure Disk Encryption을 구성해야 합니다. 최신 버전의 [Azure PowerShell 릴리스](https://github.com/Azure/azure-powershell/releases)를 다운로드합니다.

Azure PowerShell를 사용 하 여 [Azure 계정에 로그인](/powershell/azure/authenticate-azureps?view=azps-2.5.0)하려면 [AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) cmdlet을 사용 합니다.

```powershell
Connect-AzAccount
```

여러 구독이 있는 경우 구독을 지정 하려면 [AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) cmdlet을 사용 하 여 해당 구독을 나열한 다음 [AzContext](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0) cmdlet을 사용 합니다.

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

[AzContext](/powershell/module/Az.Accounts/Get-AzContext) cmdlet을 실행 하면 올바른 구독이 선택 되었는지 확인 합니다.

Azure Disk Encryption cmdlet이 설치 되었는지 확인 하려면 다음과 같이 [get-help](/powershell/module/microsoft.powershell.core/get-command?view=powershell-6) cmdlet을 사용 합니다.
     
```powershell
Get-command *diskencryption*
```
자세한 내용은 [Azure PowerShell 시작](/powershell/azure/get-started-azureps)을 참조 하세요. 