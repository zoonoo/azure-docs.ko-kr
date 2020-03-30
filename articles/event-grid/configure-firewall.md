---
title: Azure 이벤트 그리드 토픽 또는 도메인에 대한 IP 방화벽 구성(미리 보기)
description: 이 문서에서는 Event Grid 항목 또는 도메인에 대한 방화벽 설정을 구성하는 방법을 설명합니다.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: spelluru
ms.openlocfilehash: b195872ca1002970fa96ae133d5eb47a9267796d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299869"
---
# <a name="configure-ip-firewall-for-azure-event-grid-topics-or-domains-preview"></a>Azure 이벤트 그리드 토픽 또는 도메인에 대한 IP 방화벽 구성(미리 보기)
기본적으로 요청이 유효한 인증 및 권한 부여와 함께 제공되는 경우 인터넷에서 주제 및 도메인에 액세스할 수 있습니다. IP 방화벽을 사용하면 [CIDR(클래스리스 도메인 간 라우팅)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 표기법의 IPv4 주소 또는 IPv4 주소 범위 집합으로만 제한할 수 있습니다. 다른 IP 주소에서 보낸 게시자는 거부되며 403(금지됨) 응답을 받게 됩니다. 이벤트 그리드에서 지원하는 네트워크 보안 기능에 대한 자세한 내용은 [이벤트 그리드에 대한 네트워크 보안을](network-security.md)참조하십시오.

이 문서에서는 Azure Event Grid 항목 또는 도메인에 대한 IP 방화벽 설정을 구성하는 방법을 설명합니다.

## <a name="use-azure-portal"></a>Azure Portal 사용
이 섹션에서는 Azure 포털을 사용하여 인바운드 IP 방화벽 규칙을 만드는 방법을 보여 주십니다. 이 섹션에 표시된 단계는 항목에 대한 단계입니다. 유사한 단계를 사용하여 도메인에 대한 인바운드 IP 규칙을 만들 수 **있습니다.** 

1. Azure [포털에서](https://portal.azure.com)이벤트 그리드 항목 또는 도메인으로 이동한 다음 **네트워킹** 탭으로 전환합니다.
2. 공용 **네트워크를** 선택하여 인터넷을 포함한 모든 네트워크가 리소스에 액세스할 수 있도록 허용합니다. 

    IP 기반 방화벽 규칙을 사용하여 트래픽을 제한할 수 있습니다. 클래스리스 도메인 간 라우팅(CIDR) 표기법의 단일 IPv4 주소 또는 IP 주소 범위를 지정합니다. 

    ![공용 네트워크 페이지](./media/configure-firewall/public-networks-page.png)
3. **개인 끝점만** 이 리소스에 액세스할 수 있도록 개인 끝점만 선택합니다. 이 페이지의 **비공개 끝점 연결** 탭을 사용하여 연결을 관리합니다. 

    ![공용 네트워크 페이지](./media/configure-firewall/private-endpoints-page.png)
4. 도구 모음에서 **저장**을 선택합니다. 



## <a name="use-azure-cli"></a>Azure CLI 사용
이 섹션에서는 Azure CLI 명령을 사용하여 인바운드 IP 규칙이 있는 토픽을 만드는 방법을 보여 주십니다. 이 섹션에 표시된 단계는 항목에 대한 단계입니다. 유사한 단계를 사용하여 도메인에 대한 인바운드 IP 규칙을 만들 수 **있습니다.** 


### <a name="enable-public-network-access-for-an-existing-topic"></a>기존 토픽에 대한 공용 네트워크 액세스 사용
기본적으로 토픽 및 도메인에 대해 공용 네트워크 액세스가 활성화됩니다. 인바운드 IP 방화벽 규칙을 구성하여 트래픽을 제한할 수 있습니다. 

```azurecli-interactive
az rest --method patch --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" --body "{\""properties\"": {\""publicNetworkAccess\"": \""Enabled\""}}"
```

### <a name="disable-public-network-access-for-an-existing-topic"></a>기존 항목에 대한 공용 네트워크 액세스를 사용하지 않도록 설정
주제 나 도메인에 대 한 공용 네트워크 액세스를 사용 하지 않도록 설정 하는 경우 공용 인터넷을 통해 트래픽이 허용 되지 않습니다. 개인 끝점 연결만 이러한 리소스에 액세스할 수 있습니다. 

```azurecli-interactive
az rest --method patch --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" --body "{\""properties\"": {\""publicNetworkAccess\"": \""Disabled\""}}"
```

### <a name="create-topic-with-inbound-ip-rules"></a>인바운드 IP 규칙으로 토픽 만들기
다음 샘플 CLI 명령은 한 단계에서 인바운드 IP 규칙이 있는 이벤트 그리드 항목을 만듭니다. 

```azurecli-interactive
az rest --method put \
    --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" \
    --body {\""location\"":\""<LOCATION>\", \""properties\"" :{\""publicNetworkAccess\"":\""enabled\"",\""InboundIpRules\"": [ {\""ipMask\"": \""<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>\"", \""action\"": \""allow\""} ]}}
```

### <a name="create-topic-first-and-then-add-inbound-ip-rules"></a>먼저 토픽을 만든 다음 인바운드 IP 규칙을 추가합니다.
이 예제에서는 먼저 이벤트 그리드 항목을 만들고 별도의 명령에서 토픽에 대한 인바운드 IP 규칙을 추가합니다. 또한 두 번째 명령에서 설정된 인바운드 IP 규칙을 업데이트합니다. 

```azurecli-interactive

# create the event grid topic first
az rest --method put \
    --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" \
    --body {\""location\"":\""<LOCATION>\""}

# add inbound IP rules
az rest --method put \
    --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" 
    --body {\""location\"":\""<LOCATION>\", \""properties\"" :{\""publicNetworkAccess\"":\""enabled\"", \""InboundIpRules\"": [ {\""ipMask\"": \""<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>\"", \""action\"": \""allow\""} ]}}

# later, update topic with additional ip rules or remove them. 
az rest --method put \
    --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" 
    --body {\""location\"":\""<LOCATION>\", \""properties\"" :{\""publicNetworkAccess\"":\""enabled\"", \""InboundIpRules\"": [ {\""ipMask\"": \""<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>\"", \""action\"": \""allow\""}, {\""ipMask\"": \""<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>\"", \""action\"": \""allow\""} ]}}
```


## <a name="use-powershell"></a>PowerShell 사용
이 섹션에서는 Azure PowerShell 명령을 사용하여 인바운드 IP 방화벽 규칙을 사용하여 Azure 이벤트 그리드 토픽을 만드는 방법을 보여 주십니다. 이 섹션에 표시된 단계는 항목에 대한 단계입니다. 유사한 단계를 사용하여 도메인에 대한 인바운드 IP 규칙을 만들 수 **있습니다.** 

### <a name="prerequisite"></a>필수 요소
[방법: 포털을 사용하여 리소스에 액세스하여](../active-directory/develop/howto-create-service-principal-portal.md) Azure Active Directory 응용 프로그램을 만들고 다음 값을 기록할 수 있는 Azure AD 응용 프로그램 및 서비스 주체를 만듭니다.

- 디렉터리(테넌트) ID
- 응용 프로그램(클라이언트) ID
- 응용 프로그램(클라이언트) 보안

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

### <a name="enable-public-network-access-for-an-existing-topic"></a>기존 토픽에 대한 공용 네트워크 액세스 사용
기본적으로 토픽 및 도메인에 대해 공용 네트워크 액세스가 활성화됩니다. 인바운드 IP 방화벽 규칙을 구성하여 트래픽을 제한할 수 있습니다. 

```azurepowershell-interactive
$body = @{"properties"=@{"publicNetworkAccess"="enabled"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="disable-public-network-access-for-an-existing-topic"></a>기존 항목에 대한 공용 네트워크 액세스를 사용하지 않도록 설정
주제 나 도메인에 대 한 공용 네트워크 액세스를 사용 하지 않도록 설정 하는 경우 공용 인터넷을 통해 트래픽이 허용 되지 않습니다. 개인 끝점 연결만 이러한 리소스에 액세스할 수 있습니다. 

```azurepowershell-interactive
$body = @{"properties"=@{"publicNetworkAccess"="disabled"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="create-an-event-grid-topic-with-inbound-rules-in-one-step"></a>한 단계에서 인바운드 규칙이 있는 이벤트 그리드 토픽 만들기

```azurepowershell-interactive

# prepare the body for the REST PUT method. Notice that inbound IP rules are included. 
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled"; "inboundIpRules"=@(@{"ipmask"="<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>";"action"="allow"})}} | ConvertTo-Json -Depth 5

# create the event grid topic with inbound IP rules
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body

# verify that the topic was created
Invoke-RestMethod -Method 'Get' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5
```


### <a name="create-event-grid-topic-first-and-then-add-inbound-ip-rules"></a>먼저 이벤트 그리드 토픽을 만든 다음 인바운드 IP 규칙을 추가합니다.

```azurepowershell-interactive

# prepare the body for the REST PUT method. Notice that no inbound IP rules are specified. 
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled";}} | ConvertTo-Json -Depth 5

# create the Event Grid topic
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body`

# verify that the topic was created
Invoke-RestMethod -Method 'Get' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5

# prepare the body for REST PUT method. Notice that it includes inbound IP rules now. This feature available in both basic and premium tiers.
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled"; "inboundIpRules"=@(@{"ipmask"="<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>";"action"="allow"}, @{"ipmask"="<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>";"action"="allow"})}} | ConvertTo-Json -Depth 5

# update the topic with inbound IP rules
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body

# verify that the topic was updated
Invoke-RestMethod -Method 'Get' 
    -Uri "https://management.azure.com/subscriptions/<AzURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5

```

## <a name="next-steps"></a>다음 단계

* 이벤트 배달 모니터링에 대한 정보는 [Event Grid 메시지 배달 모니터링](monitor-event-delivery.md)을 참조하세요.
* 인증 키에 대한 자세한 내용은 [Event Grid 보안 및 인증](security-authentication.md)을 참조하세요.
* Azure Event Grid 구독을 만드는 방법에 대한 자세한 내용은 [Event Grid 구독 스키마](subscription-creation-schema.md)를 참조하세요.
