---
title: Azure 방화벽에서 IP 그룹 만들기
description: IP 그룹을 사용 하 여 Azure 방화벽 규칙에 대 한 IP 주소를 그룹화 및 관리할 수 있습니다.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 06/23/2020
ms.author: victorh
ms.openlocfilehash: c53b35351108717c7b597e052a66e9902be5ec6c
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93394529"
---
# <a name="create-ip-groups"></a>IP 그룹 만들기

IP 그룹을 사용 하 여 Azure 방화벽 규칙에 대 한 IP 주소를 그룹화 및 관리할 수 있습니다. 단일 IP 주소, 여러 IP 주소 또는 하나 이상의 IP 주소 범위를 가질 수 있습니다.

## <a name="create-an-ip-group---azure-portal"></a>IP 그룹 만들기-Azure Portal

Azure Portal를 사용 하 여 IP 그룹을 만들려면 다음을 수행 합니다.

1. Azure Portal 홈 페이지에서 **리소스 만들기** 를 선택 합니다.
1. 검색 상자에 **Ip 그룹** 을 입력 한 다음, **ip 그룹** 을 선택 합니다.
1. **만들기** 를 선택합니다.
1. 구독을 선택합니다.
1. 리소스 그룹을 선택하거나 새로 만듭니다.
1. IP 그룹의 고유한 이름을 입력 한 다음 지역을 선택 합니다.
1. 완료되면 **다음: IP 주소** 를 선택합니다.
1. IP 주소, 여러 IP 주소 또는 IP 주소 범위를 입력 합니다.

   IP 주소를 입력 하는 방법에는 두 가지가 있습니다.
   - 수동으로 입력할 수 있습니다.
   - 파일에서 가져올 수 있습니다.

   파일에서 가져오려면 **파일에서 가져오기** 를 선택 합니다. 파일을 상자로 끌거나 **파일 찾아보기** 를 선택할 수 있습니다. 필요한 경우 업로드 된 IP 주소를 검토 하 고 편집할 수 있습니다.

   IP 주소를 입력 하면 포털에서 해당 IP 주소에 대 한 유효성을 검사 하 여 겹치는, 중복 및 형식 지정 문제를 확인 합니다.

1. 완료 되 면 **검토 + 만들기** 를 선택 합니다.
1. **만들기** 를 선택합니다.

## <a name="create-an-ip-group---azure-powershell"></a>IP 그룹 만들기-Azure PowerShell

이 예제에서는 Azure PowerShell를 사용 하 여 주소 접두사와 IP 주소를 사용 하 여 IP 그룹을 만듭니다.

```azurepowershell
$ipGroup = @{
    Name              = 'ipGroup'
    ResourceGroupName = 'ipGroupRG'
    Location          = 'West US'
    IpAddress         = @('10.0.0.0/24', '192.168.1.10') 
}

New-AzIpGroup @ipGroup
```

## <a name="create-an-ip-group---azure-cli"></a>IP 그룹 만들기-Azure CLI

이 예제에서는 Azure CLI를 사용 하 여 주소 접두사와 IP 주소를 사용 하 여 IP 그룹을 만듭니다.

```azurecli-interactive
az network ip-group create \
    --name ipGroup \ 
    --resource-group ipGroupRG \
    --location westus \
    --ip-addresses '10.0.0.0/24' '192.168.1.10'
```

## <a name="next-steps"></a>다음 단계

- [IP 그룹에 대 한 자세한 정보](ip-groups.md)
