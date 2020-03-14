---
title: Azure Event Grid 토픽 또는 도메인에 대 한 IP 방화벽 구성 (미리 보기)
description: 이 문서에서는 Event Grid 토픽 또는 도메인에 대 한 방화벽 설정을 구성 하는 방법을 설명 합니다.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: spelluru
ms.openlocfilehash: b195872ca1002970fa96ae133d5eb47a9267796d
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79299869"
---
# <a name="configure-ip-firewall-for-azure-event-grid-topics-or-domains-preview"></a>Azure Event Grid 토픽 또는 도메인에 대 한 IP 방화벽 구성 (미리 보기)
기본적으로 요청에 유효한 인증 및 권한 부여가 제공 되는 한, 항목 및 도메인은 인터넷에서 액세스할 수 있습니다. IP 방화벽을 사용 하면 [CIDR (클래스 없는 도메인 간 라우팅)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 표기법에서 ipv4 주소 또는 ipv4 주소 범위 집합 으로만 제한할 수 있습니다. 다른 IP 주소에서 시작 된 게시자는 거부 되며 403 (사용할 수 없음) 응답을 받게 됩니다. Event Grid에서 지 원하는 네트워크 보안 기능에 대 한 자세한 내용은 [Event Grid 네트워크 보안](network-security.md)을 참조 하세요.

이 문서에서는 Azure Event Grid 토픽 또는 도메인에 대 한 IP 방화벽 설정을 구성 하는 방법을 설명 합니다.

## <a name="use-azure-portal"></a>Azure Portal 사용
이 섹션에서는 Azure Portal를 사용 하 여 인바운드 IP 방화벽 규칙을 만드는 방법을 보여 줍니다. 이 섹션에 표시 된 단계는 항목에 대 한 것입니다. 비슷한 단계를 사용 하 여 **도메인**에 대 한 인바운드 IP 규칙을 만들 수 있습니다. 

1. [Azure Portal](https://portal.azure.com)에서 event grid 토픽 또는 도메인으로 이동 하 고 **네트워킹** 탭으로 전환 합니다.
2. 인터넷을 포함 한 모든 네트워크에서 리소스에 액세스할 수 있도록 **공용 네트워크** 를 선택 합니다. 

    IP 기반 방화벽 규칙을 사용 하 여 트래픽을 제한할 수 있습니다. 클래스 없는 CIDR (도메인 간 라우팅) 표기법에서 단일 IPv4 주소 또는 IP 주소의 범위를 지정 합니다. 

    ![공용 네트워크 페이지](./media/configure-firewall/public-networks-page.png)
3. 개인 끝점 연결만이 리소스에 액세스할 수 있도록 하려면 **전용 끝점을 선택 합니다** . 이 페이지의 **개인 끝점 연결** 탭을 사용 하 여 연결을 관리할 수 있습니다. 

    ![공용 네트워크 페이지](./media/configure-firewall/private-endpoints-page.png)
4. 도구 모음에서 **저장**을 선택합니다. 



## <a name="use-azure-cli"></a>Azure CLI 사용
이 섹션에서는 Azure CLI 명령을 사용 하 여 인바운드 IP 규칙을 사용 하 여 항목을 만드는 방법을 보여 줍니다. 이 섹션에 표시 된 단계는 항목에 대 한 것입니다. 비슷한 단계를 사용 하 여 **도메인**에 대 한 인바운드 IP 규칙을 만들 수 있습니다. 


### <a name="enable-public-network-access-for-an-existing-topic"></a>기존 항목에 대 한 공용 네트워크 액세스 사용
기본적으로 토픽 및 도메인에 대해 공용 네트워크 액세스를 사용할 수 있습니다. 인바운드 IP 방화벽 규칙을 구성 하 여 트래픽을 제한할 수 있습니다. 

```azurecli-interactive
az rest --method patch --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" --body "{\""properties\"": {\""publicNetworkAccess\"": \""Enabled\""}}"
```

### <a name="disable-public-network-access-for-an-existing-topic"></a>기존 항목에 대 한 공용 네트워크 액세스 사용 안 함
토픽 또는 도메인에 대해 공용 네트워크 액세스를 사용 하지 않도록 설정 하면 공용 인터넷을 통한 트래픽이 허용 되지 않습니다. 개인 끝점 연결만 이러한 리소스에 액세스할 수 있습니다. 

```azurecli-interactive
az rest --method patch --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" --body "{\""properties\"": {\""publicNetworkAccess\"": \""Disabled\""}}"
```

### <a name="create-topic-with-inbound-ip-rules"></a>인바운드 ip 규칙을 사용 하 여 토픽 만들기
다음 샘플 CLI 명령은 인바운드 IP 규칙을 사용 하 여 event grid 토픽을 한 단계로 만듭니다. 

```azurecli-interactive
az rest --method put \
    --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" \
    --body {\""location\"":\""<LOCATION>\", \""properties\"" :{\""publicNetworkAccess\"":\""enabled\"",\""InboundIpRules\"": [ {\""ipMask\"": \""<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>\"", \""action\"": \""allow\""} ]}}
```

### <a name="create-topic-first-and-then-add-inbound-ip-rules"></a>먼저 토픽을 만든 다음 인바운드 ip 규칙을 추가 합니다.
이 예에서는 먼저 event grid 토픽을 만든 다음 개별 명령에 항목에 대 한 인바운드 IP 규칙을 추가 합니다. 또한 두 번째 명령에 설정 된 인바운드 IP 규칙도 업데이트 합니다. 

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
이 섹션에서는 Azure PowerShell 명령을 사용 하 여 인바운드 IP 방화벽 규칙을 사용 하 여 Azure Event Grid 항목을 만드는 방법을 보여 줍니다. 이 섹션에 표시 된 단계는 항목에 대 한 것입니다. 비슷한 단계를 사용 하 여 **도메인**에 대 한 인바운드 IP 규칙을 만들 수 있습니다. 

### <a name="prerequisite"></a>필수 요소
[방법: 포털을 사용 하 여 리소스에 액세스할 수 있는 AZURE AD 응용 프로그램 및 서비스 주체 만들기](../active-directory/develop/howto-create-service-principal-portal.md) 에 대 한 지침을 수행 하 여 Azure Active Directory 응용 프로그램을 만들고 다음 값을 적어둡니다.

- 디렉터리 (테 넌 트) ID
- 응용 프로그램 (클라이언트) ID
- 응용 프로그램 (클라이언트) 암호

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

### <a name="enable-public-network-access-for-an-existing-topic"></a>기존 항목에 대 한 공용 네트워크 액세스 사용
기본적으로 토픽 및 도메인에 대해 공용 네트워크 액세스를 사용할 수 있습니다. 인바운드 IP 방화벽 규칙을 구성 하 여 트래픽을 제한할 수 있습니다. 

```azurepowershell-interactive
$body = @{"properties"=@{"publicNetworkAccess"="enabled"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="disable-public-network-access-for-an-existing-topic"></a>기존 항목에 대 한 공용 네트워크 액세스 사용 안 함
토픽 또는 도메인에 대해 공용 네트워크 액세스를 사용 하지 않도록 설정 하면 공용 인터넷을 통한 트래픽이 허용 되지 않습니다. 개인 끝점 연결만 이러한 리소스에 액세스할 수 있습니다. 

```azurepowershell-interactive
$body = @{"properties"=@{"publicNetworkAccess"="disabled"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="create-an-event-grid-topic-with-inbound-rules-in-one-step"></a>한 단계에서 인바운드 규칙을 사용 하 여 event grid 토픽 만들기

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


### <a name="create-event-grid-topic-first-and-then-add-inbound-ip-rules"></a>먼저 event grid 토픽을 만든 다음 인바운드 ip 규칙을 추가 합니다.

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
