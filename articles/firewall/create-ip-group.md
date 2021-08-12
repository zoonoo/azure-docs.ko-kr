---
title: Azure Firewall에서 IP 그룹 만들기
description: IP 그룹을 사용하면 Azure Firewall 규칙에 대한 IP 주소를 그룹화하고 관리할 수 있습니다.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 06/23/2020
ms.author: victorh
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e19c27c130b59339ec46038007eb82b3fd7ac16b
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110693719"
---
# <a name="create-ip-groups"></a>IP 그룹 만들기

IP 그룹을 사용하면 Azure Firewall 규칙에 대한 IP 주소를 그룹화하고 관리할 수 있습니다. 단일 IP 주소, 여러 IP 주소 또는 하나 이상의 IP 주소 범위를 가질 수 있습니다.

## <a name="create-an-ip-group---azure-portal"></a>IP 그룹 만들기 - Azure Portal

Azure Portal을 사용하여 IP 그룹을 만들려면 다음을 수행합니다.

1. Azure Portal 홈페이지에서 **리소스 만들기** 를 선택합니다.
1. 검색 상자에 **IP 그룹** 을 입력한 다음, **IP 그룹** 을 선택합니다.
1. **만들기** 를 선택합니다.
1. 구독을 선택합니다.
1. 리소스 그룹을 선택하거나 새로 만듭니다.
1. IP 그룹의 고유한 이름을 입력한 다음, 지역을 선택합니다.
1. 완료되면 **다음: IP 주소** 를 선택합니다.
1. IP 주소, 여러 IP 주소 또는 IP 주소 범위를 입력합니다.

   IP 주소를 입력하는 방법에는 두 가지가 있습니다.
   - 수동으로 입력할 수 있습니다.
   - 파일에서 가져올 수 있습니다.

   파일에서 가져오려면 **파일에서 가져오기** 를 선택합니다. 파일을 상자로 끌거나 **파일 찾아보기** 를 선택할 수 있습니다. 필요한 경우 업로드된 IP 주소를 검토하고 편집할 수 있습니다.

   IP 주소를 입력하면 포털에서 IP 주소의 유효성을 검사하여 겹침, 중복 및 서식 지정 문제를 확인합니다.

1. 완료되면 **검토 + 만들기** 를 선택합니다.
1. **만들기** 를 선택합니다.

## <a name="create-an-ip-group---azure-powershell"></a>IP 그룹 만들기 - Azure PowerShell

이 예제에서는 Azure PowerShell을 사용하여 주소 접두사 및 IP 주소가 있는 IP 그룹을 만듭니다.

```azurepowershell
$ipGroup = @{
    Name              = 'ipGroup'
    ResourceGroupName = 'ipGroupRG'
    Location          = 'West US'
    IpAddress         = @('10.0.0.0/24', '192.168.1.10') 
}

New-AzIpGroup @ipGroup
```

## <a name="create-an-ip-group---azure-cli"></a>IP 그룹 만들기 - Azure CLI

이 예제에서는 Azure CLI를 사용하여 주소 접두사 및 IP 주소가 있는 IP 그룹을 만듭니다.

```azurecli-interactive
az network ip-group create \
    --name ipGroup \ 
    --resource-group ipGroupRG \
    --location westus \
    --ip-addresses '10.0.0.0/24' '192.168.1.10'
```

## <a name="next-steps"></a>다음 단계

- [IP 그룹에 대해 자세히 알아보기](ip-groups.md)
