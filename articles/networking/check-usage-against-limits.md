---
title: 제한에 대해 Azure 리소스 사용량 확인 | Microsoft Docs
description: Azure 구독 제한을 기준으로 Azure 리소스 사용량을 확인하는 방법을 알아봅니다.
services: networking
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: networking
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/05/2018
ms.author: jdial
ms.openlocfilehash: 124653f95dedbb1606e85cbfcd8229c026cf7341
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46960654"
---
# <a name="check-resource-usage-against-limits"></a>제한에 대해 리소스 사용량 확인

이 문서에서는 구독에서 배포한 각 네트워크 리소스 종류의 수를 확인하는 방법과 [구독 제한](../azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits)이란 무엇인지에 대해 알아봅니다. 제한에 대한 리소스 사용량을 볼 수 있는 기능은 현재 사용량을 추적하고 향후 사용량을 계획하는 데 유용합니다. [Azure Portal](#azure-portal), [PowerShell](#powershell) 또는 [Azure CLI](#azure-cli)을 사용하여 사용량을 추적할 수 있습니다.

## <a name="azure-portal"></a>Azure Portal

1. Azure [Portal](https://portal.azure.com)에 로그인합니다.
2. Azure Portal의 왼쪽 위 모서리에서 **모든 서비스**를 선택합니다.
3. **필터** 상자에 *구독*을 입력합니다. 검색 결과에 **구독**이 나타나면 이를 선택합니다.
4. 사용량 정보를 보려는 구독의 이름을 선택합니다.
5. **설정**에서 **사용량 + 할당량**을 선택합니다.
6. 다음 옵션을 선택할 수 있습니다.
    - **리소스 종류**: 모든 리소스 종류를 선택하거나 확인하려는 특정 유형의 리소스를 선택할 수 있습니다.
    - **공급자**: 모든 리소스 공급자를 선택하거나 **계산**, **네트워크** 또는 **저장소**를 선택할 수 있습니다.
    - **위치**: 모든 Azure 위치를 선택하거나 특정 위치를 선택할 수 있습니다.
    - 모든 리소스 또는 하나 이상 배포되는 리소스만 표시하도록 선택할 수 있습니다.

    다음 그림의 예제에서는 미국 동부에 배포된 리소스가 하나 이상 있는 모든 네트워크 리소스를 보여 줍니다.

        ![View usage data](./media/check-usage-against-limits/view-usage.png)

    열 머리글을 선택하여 열을 정렬할 수 있습니다. 표시된 제한은 구독에 대한 제한입니다. 기본 제한을 늘려야 할 경우 **요청 증가**를 선택한 다음, 지원 요청을 완료하고 제출합니다. 모든 리소스에는 Azure [제한](../azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits)에 나열된 최대 한도가 있습니다. 현재 제한이 이미 최대 수인 경우에는 제한을 늘릴 수 없습니다.

## <a name="powershell"></a>PowerShell

[Azure Cloud Shell](https://shell.azure.com/powershell) 뒤에 오는 명령을 실행하거나 또는 컴퓨터에서 PowerShell을 실행합니다. Azure Cloud Shell은 무료 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 컴퓨터에서 PowerShell을 실행하는 경우 *AzureRM* PowerShell 모듈 버전 6.0.1 이상이 필요합니다. 컴퓨터에서 `Get-Module -ListAvailable AzureRM`을 실행하여 설치된 버전을 확인합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요. PowerShell을 로컬로 실행하는 경우 `Login-AzureRmAccount`를 실행하여 Azure에 로그인해야 합니다.

[Get-AzureRmNetworkUsage](https://docs.microsoft.com/en-us/powershell/module/azurerm.network/get-azurermnetworkusage?view=azurermps-6.8.0)를 사용하여 제한에 대한 사용량을 봅니다. 다음 예제에서는 미국 동부 위치에 배포된 리소스가 하나 이상 있는 리소스에 대한 사용량을 가져옵니다.

```azurepowershell-interactive
Get-AzureRmNetworkUsage `
  -Location eastus `
  | Where-Object {$_.CurrentValue -gt 0} `
  | Format-Table ResourceType, CurrentValue, Limit
```

다음 예제 출력과 유사한 출력이 표시됩니다.

```powershell
ResourceType            CurrentValue Limit
------------            ------------ -----
Virtual Networks                   1    50
Network Security Groups            2   100
Public IP Addresses                1    60
Network Interfaces                 1 24000
Network Watchers                   1     1
```

## <a name="azure-cli"></a>Azure CLI

이 문서의 작업을 완료하기 위해 Azure CLI(명령줄 인터페이스)를 사용하는 경우 [Azure Cloud Shell](https://shell.azure.com/bash)에서 명령을 실행하거나 컴퓨터에서 CLI를 실행합니다. 이 아티클에서는 Azure CLI 버전 2.0.32 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요. Azure CLI를 로컬로 실행하는 경우 `az login`을 실행하여 Azure에 로그인해야 합니다.

[az network list-usages](/cli/azure/network?view=azure-cli-latest#az-network-list-usages)를 사용하여 제한에 대한 사용량을 봅니다. 다음 예제에서는 미국 동부 위치에 있는 리소스에 대한 사용량을 가져옵니다.

```azurecli-interactive
az network list-usages \
  --location eastus \
  --out table
```

다음 예제 출력과 유사한 출력이 표시됩니다.

```azurecli
Name                    CurrentValue Limit
------------            ------------ -----
Virtual Networks                   1    50
Network Security Groups            2   100
Public IP Addresses                1    60
Network Interfaces                 1 24000
Network Watchers                   1     1
Load Balancers                     0   100
Application Gateways               0    50
```
