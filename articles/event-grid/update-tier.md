---
title: Azure 이벤트 그리드 토픽 또는 도메인의 가격 책정 계층 업데이트
description: 이 문서에서는 Azure 포털, Azure CLI 및 Azure PowerShell을 사용하여 Azure Event Grid 토픽 또는 도메인(기본에서 프리미엄으로, 프리미엄에서 기본으로)의 가격 책정 계층을 업데이트하는 방법에 대해 설명합니다.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: spelluru
ms.openlocfilehash: 1e92a9f0b9ba007b289c634995b04f4f1575310a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79300142"
---
# <a name="update-pricing-tier"></a>가격 책정 계층 업데이트 
이 문서에서는 Azure 포털, Azure CLI 및 Azure PowerShell을 사용하여 Azure Event Grid 항목 또는 도메인의 가격 책정 계층을 업데이트하는 방법을 보여 주며, 이 문서에서는 

## <a name="use-azure-portal"></a>Azure Portal 사용
이 섹션에서는 Azure Portal에서 토픽 또는 도메인의 가격 책정 계층을 변경하는 방법을 보여 주며 있습니다. 

### <a name="overview-page"></a>개요 페이지
**개요** 페이지에서 주제 또는 도메인의 가격 책정 계층을 변경할 수 있습니다. 다음 예제에서는 주제를 기본 계층에서 프리미엄 계층으로 업그레이드하는 방법을 보여 주며, 이 예제에서는 주제를 업그레이드하는 방법을 보여 주어 도있습니다. 프리미엄 계층에서 기본 계층으로 다운그레이드하는 단계는 비슷합니다.

1. Azure [포털에서](https://portal.azure.com)토픽 또는 도메인 페이지로 이동합니다. 
2. **개요** 페이지에서 현재 가격 책정 계층을 선택합니다(다음 예제에서는 **기본입니다.)**
    
    ![현재 가격 책정 계층 선택](./media/update-tier/select-tier.png)
3. 가격 **책정 계층** 페이지에서 계층을 변경하고 **확인을**선택합니다. 

    ![가격 책정 계층 업데이트](./media/update-tier/change-tier.png)
4. **알림** 페이지에서 작업 상태를 확인합니다.

    ![업데이트 상태](./media/update-tier/status.png)    
5. **개요** 페이지에 업데이트된 계층이 표시되는지 확인합니다. 

    ![업데이트 상태](./media/update-tier/changed-tier.png)

### <a name="networking-page"></a>네트워킹 페이지
**네트워킹** 페이지에서 기본 계층에서 프리미엄 계층으로 **업그레이드할** 수 있습니다. 하지만 이 페이지의 프리미엄 계층에서 기본 계층으로 다운그레이드할 수는 없습니다. 

1. Azure [포털에서](https://portal.azure.com)토픽 또는 도메인 페이지로 이동합니다. 
2. **네트워킹** 페이지에서 **개인 끝점 연결(미리 보기)** 탭으로 전환합니다. 
3. 현재 가격 책정 계층이 **기본인**경우 다음 메시지가 표시됩니다. 이를 선택합니다. 

    ![개인 엔드포인트 연결 페이지에서 계층 업데이트](./media/update-tier/private-endpoint-connections-page.png)
4. 프리미엄 **가격 계층 으로 업데이트 페이지에서** **예**를 선택합니다. 
    
    ![업그레이드 확인](./media/update-tier/message-private-endpoint-connection.png)
5. **알림** 페이지에서 작업 상태를 확인합니다.

    ![업데이트 상태](./media/update-tier/status.png)



## <a name="use-azure-cli"></a>Azure CLI 사용
이 섹션에서는 Azure CLI 명령을 사용하여 토픽 또는 도메인의 가격 책정 계층을 변경하는 방법을 보여 주십니다. 

### <a name="upgrade-a-topic-from-basic-to-premium"></a>주제를 기본에서 프리미엄으로 업그레이드

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""premium\""}}"
```

### <a name="downgrade-a-topic-from-premium-to-basic"></a>주제를 프리미엄에서 기본으로 다운그레이드

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""basic\""}}"
```

### <a name="upgrade-a-domain-from-basic-to-premium"></a>도메인을 기본에서 프리미엄으로 업그레이드

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""premium\""}}"
```

### <a name="downgrade-a-domain-from-premium-to-basic"></a>도메인을 프리미엄에서 기본으로 다운그레이드

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""basic\""}}"
```



## <a name="use-azure-powershell"></a>Azure PowerShell 사용
이 섹션에서는 PowerShell 명령을 사용하여 토픽 또는 도메인의 가격 책정 계층을 변경하는 방법을 보여 주십니다. 

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>REST API 호출에 대한 토큰 및 헤더 준비 
REST API 호출 및 권한 부여 및 기타 헤더 정보와 함께 사용할 인증 토큰을 얻으려면 다음 필수 구성 조건 명령을 실행합니다. 

```azurepowershell-interactive
# replace <CLIENT ID> and <CLIENT SECRET>
$body = "grant_type=client_credentials&client_id=<CLIENT ID>&client_secret=<CLIENT SECRET>&resource=https://management.core.windows.net"

# get the authentication token. Replace <TENANT ID>
$Token = Invoke-RestMethod -Method Post `
    -Uri https://login.microsoftonline.com/<TENANT ID>/oauth2/token `
    -Body $body `
    -ContentType 'application/x-www-form-urlencoded'

# set authorization and content-type headers
$Headers = @{}
$Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
$Headers.Add("Content-Type","application/json")
```

### <a name="upgrade-a-topic-from-basic-to-premium"></a>주제를 기본에서 프리미엄으로 업그레이드

```azurepowershell-interactive
$body = @{"sku"=@{"name"="premium"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="downgrade-a-topic-from-premium-to-basic"></a>주제를 프리미엄에서 기본으로 다운그레이드

```azurepowershell-interactive
$body = @{"sku"=@{"name"="basic"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="upgrade-a-domain-from-basic-to-premium"></a>도메인을 기본에서 프리미엄으로 업그레이드

```azurepowershell-interactive
$body = @{"sku"=@{"name"="premium"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="downgrade-a-domain-from-premium-to-basic"></a>도메인을 프리미엄에서 기본으로 다운그레이드

```azurepowershell-interactive
$body = @{"sku"=@{"name"="basic"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

## <a name="next-steps"></a>다음 단계
프리미엄 계층 토픽 및 도메인의 경우 개인 끝점을 구성하여 선택한 가상 네트워크에서만 액세스를 제한할 수 있습니다. 단계별 지침은 [개인 끝점 구성을](configure-private-endpoints.md)참조하십시오.
