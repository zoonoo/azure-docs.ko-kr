---
title: Azure Event Grid 토픽 또는 도메인의 가격 책정 계층 업데이트
description: 이 문서에서는 Azure Portal, Azure CLI 및 Azure PowerShell를 사용 하 여 Azure Event Grid 토픽 또는 도메인 (basic에서 premium, premium)의 가격 책정 계층을 업데이트 하는 방법을 설명 합니다.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: spelluru
ms.openlocfilehash: 1e92a9f0b9ba007b289c634995b04f4f1575310a
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79300142"
---
# <a name="update-pricing-tier"></a>가격 책정 계층 업데이트 
이 문서에서는 Azure Portal, Azure CLI 및 Azure PowerShell를 사용 하 여 Azure Event Grid 토픽 또는 도메인의 가격 책정 계층을 업데이트 하는 방법을 보여 줍니다. 

## <a name="use-azure-portal"></a>Azure Portal 사용
이 섹션에서는 Azure Portal 항목 또는 도메인의 가격 책정 계층을 변경 하는 방법을 보여 줍니다. 

### <a name="overview-page"></a>개요 페이지
**개요** 페이지에서 토픽 또는 도메인의 가격 책정 계층을 변경할 수 있습니다. 다음 예제에서는 기본 계층에서 프리미엄 계층으로 토픽을 업그레이드 하는 방법을 보여 줍니다. 프리미엄 계층에서 기본 계층으로 다운 그레이드 하는 단계도 비슷합니다.

1. [Azure Portal](https://portal.azure.com)에서 토픽 또는 도메인 페이지로 이동 합니다. 
2. **개요** 페이지에서 현재 가격 책정 계층을 선택 합니다 (다음 예제에서는 **기본**).
    
    ![현재 가격 책정 계층을 선택 합니다.](./media/update-tier/select-tier.png)
3. **가격 책정 계층** 페이지에서 계층을 변경 하 고 **확인**을 선택 합니다. 

    ![가격 책정 계층 업데이트](./media/update-tier/change-tier.png)
4. **알림** 페이지에서 작업의 상태를 확인 합니다.

    ![업데이트 상태](./media/update-tier/status.png)    
5. **개요** 페이지에 업데이트 된 계층이 표시 되는지 확인 합니다. 

    ![업데이트 상태](./media/update-tier/changed-tier.png)

### <a name="networking-page"></a>네트워킹 페이지
**네트워킹** 페이지의 기본 계층에서 프리미엄 계층으로 **업그레이드할** 수 있습니다. 그러나이 페이지에서는 프리미엄 계층에서 기본 계층으로 다운 그레이드할 수 없습니다. 

1. [Azure Portal](https://portal.azure.com)에서 토픽 또는 도메인 페이지로 이동 합니다. 
2. **네트워킹** 페이지에서 **개인 끝점 연결 (미리 보기)** 탭으로 전환 합니다. 
3. 현재 가격 책정 계층이 **basic**인 경우 다음 메시지가 표시 됩니다. 이를 선택합니다. 

    ![개인 끝점 연결의 계층 업데이트 페이지](./media/update-tier/private-endpoint-connections-page.png)
4. **프리미엄 가격 책정 계층 업데이트** 페이지에서 **예**를 선택 합니다. 
    
    ![업그레이드 확인](./media/update-tier/message-private-endpoint-connection.png)
5. **알림** 페이지에서 작업의 상태를 확인 합니다.

    ![업데이트 상태](./media/update-tier/status.png)



## <a name="use-azure-cli"></a>Azure CLI 사용
이 섹션에서는 Azure CLI 명령을 사용 하 여 토픽 또는 도메인의 가격 책정 계층을 변경 하는 방법을 보여 줍니다. 

### <a name="upgrade-a-topic-from-basic-to-premium"></a>기본에서 프리미엄으로 토픽 업그레이드

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""premium\""}}"
```

### <a name="downgrade-a-topic-from-premium-to-basic"></a>프리미엄에서 기본 항목으로 항목 다운 그레이드

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""basic\""}}"
```

### <a name="upgrade-a-domain-from-basic-to-premium"></a>Basic에서 premium으로 도메인 업그레이드

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""premium\""}}"
```

### <a name="downgrade-a-domain-from-premium-to-basic"></a>도메인을 프리미엄에서 기본으로 다운 그레이드

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""basic\""}}"
```



## <a name="use-azure-powershell"></a>Azure PowerShell 사용
이 섹션에서는 PowerShell 명령을 사용 하 여 토픽 또는 도메인의 가격 책정 계층을 변경 하는 방법을 보여 줍니다. 

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>REST API 호출에 대 한 토큰 및 헤더 준비 
REST API 호출, 권한 부여 및 기타 헤더 정보와 함께 사용할 인증 토큰을 가져오려면 다음 필수 구성 요소 명령을 실행 합니다. 

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

### <a name="upgrade-a-topic-from-basic-to-premium"></a>기본에서 프리미엄으로 토픽 업그레이드

```azurepowershell-interactive
$body = @{"sku"=@{"name"="premium"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="downgrade-a-topic-from-premium-to-basic"></a>프리미엄에서 기본 항목으로 항목 다운 그레이드

```azurepowershell-interactive
$body = @{"sku"=@{"name"="basic"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="upgrade-a-domain-from-basic-to-premium"></a>Basic에서 premium으로 도메인 업그레이드

```azurepowershell-interactive
$body = @{"sku"=@{"name"="premium"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="downgrade-a-domain-from-premium-to-basic"></a>도메인을 프리미엄에서 기본으로 다운 그레이드

```azurepowershell-interactive
$body = @{"sku"=@{"name"="basic"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

## <a name="next-steps"></a>다음 단계
프리미엄 계층 항목 및 도메인의 경우 선택한 가상 네트워크 에서만 액세스를 제한 하도록 개인 끝점을 구성할 수 있습니다. 단계별 지침은 [개인 끝점 구성](configure-private-endpoints.md)을 참조 하세요.
