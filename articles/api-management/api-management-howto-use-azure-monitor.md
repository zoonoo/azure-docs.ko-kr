---
title: Azure API Management에서 게시된 API 모니터링 | Microsoft Docs
description: 이 자습서의 단계에 따라 Azure API Management에서 API를 모니터링하는 방법을 알아봅니다.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: c3148adc42cb4f899a87d894909eedff4c798575
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59680250"
---
# <a name="monitor-published-apis"></a>게시된 API 모니터링

Azure Monitor를 통해 Azure 리소스의 메트릭 또는 로그에 대해 시각화, 쿼리, 라우팅, 보관 및 조치를 수행할 수 있습니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 활동 로그 보기
> * 진단 로그 보기
> * API의 메트릭 보기 
> * API가 무단 호출을 받을 경우의 경고 규칙 설정

다음 비디오는 Azure Monitor를 사용하여 API Management를 모니터링하는 방법을 보여 줍니다. 

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Monitor-API-Management-with-Azure-Monitor/player]

## <a name="prerequisites"></a>필수 조건

+ [Azure API Management 용어](api-management-terminology.md)를 익힙니다.
+ 다음 빠른 시작을 완료합니다. [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)
+ 또한 [첫 번째 API 가져오기 및 게시](import-and-publish.md) 자습서도 완료 합니다.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="view-metrics-of-your-apis"></a>API의 메트릭 보기

API Management는 1분 간격으로 메트릭을 내보내, 거의 실시간으로 API의 상태를 확인할 수 있도록 합니다. 다음은 몇 가지 사용 가능한 메트릭에 대한 요약입니다.

* 용량(미리 보기): APIM 서비스를 업그레이드/다운그레이드할지 결정하는 데 도움이 됩니다. 메트릭은 1분 간격으로 내보내지며 보고 시점의 게이트웨이 용량을 반영합니다. 메트릭의 범위는 0-100이고, CPU 및 메모리 사용률 등의 게이트웨이 리소스를 기반으로 계산됩니다.
* 총 게이트웨이 요청: 기간 동안의 API 요청 수입니다. 
* 성공적인 게이트웨이 요청: 304, 307 및 301보다 작은(예: 200) 모든 항목을 포함하여 성공적인 HTTP 응답 코드를 수신하는 API 요청의 수입니다.
* 실패한 게이트웨이 요청: 400 및 500보다 큰 모든 항목을 포함하여 잘못된 HTTP 응답 코드를 수신하는 API 요청의 수입니다.
* 허가되지 않은 게이트웨이 요청: 401, 403 및 429를 포함하는 HTTP 응답 코드를 수신하는 API 요청의 수입니다.
* 기타 게이트웨이 요청: 앞의 범주 중 하나에 속하지 않는(예: 418) HTTP 응답 코드를 수신하는 API 요청의 수입니다.

![메트릭 차트](./media/api-management-azure-monitor/apim-monitor-metrics.png)

메트릭에 액세스하려면

1. 페이지 맨 아래의 메뉴에서 **메트릭**을 선택합니다.

    ![메트릭](./media/api-management-azure-monitor/api-management-metrics-blade.png)

2. 드롭다운 목록에서 관심 있는 메트릭을 선택합니다. 예를 들어 **성공한 게이트웨이 요청 수**를 선택합니다. 더 많은 메트릭을 차트에 추가할 수도 있습니다.
3. 차트에는 성공한 총 API 호출 수가 표시됩니다.

## <a name="set-up-an-alert-rule-for-unauthorized-request"></a>권한 없는 요청에 대한 경고 규칙 설정

메트릭 및 활동 로그를 기반으로 경고를 수신하도록 구성할 수 있습니다. Azure Monitor를 사용하여 트리거되면 다음을 수행하도록 경고를 구성할 수 있습니다.

* 전자 메일 알림 보내기
* 웹후크 호출
* Azure 논리 앱 호출

경고를 구성하려면

1. 페이지 맨 아래의 메뉴 모음에서 **경고**를 선택합니다.

    ![경고](./media/api-management-azure-monitor/alert-menu-item.png)

2. 이 경고의 **새로운 경고 규칙**을 클릭합니다.
3. **조건 추가**를 선택합니다.
4. 신호 유형 드롭다운에서 **메트릭**을 선택합니다.
5. **무단 게이트웨이 요청**을 모니터링할 신호로 선택합니다.

    ![경고](./media/api-management-azure-monitor/signal-type.png)

6. **신호 논리 구성** 보기에서, 경고가 트리거되어야 하는 임계값을 지정하고 **완료**를 클릭합니다.

    ![경고](./media/api-management-azure-monitor/threshold.png)

7. 기존 작업 그룹을 선택하거나 새 항목을 만듭니다. 아래 예제에서는 이메일이 관리자에게 전송됩니다. 

    ![경고](./media/api-management-azure-monitor/action-details.png)

8. 경고 규칙의 이름과 설명을 입력하고, 심각도를 선택합니다. 
9. **경고 규칙 만들기**를 누릅니다.
10. 이제, API 키를 사용하지 않고 회의 API를 호출해봅니다. 경고가 트리거되고 이메일이 관리자에게 전송됩니다. 

## <a name="activity-logs"></a>활동 로그

활동 로그는 API Management 서비스에서 수행된 작업에 대한 정보를 제공합니다. 활동 로그를 통해 API Management 서비스에 대한 모든 쓰기 작업(PUT, POST, DELETE)에서 "무엇을, 누가, 언제"를 판단할 수 있습니다.

> [!NOTE]
> 활동 로그에는 읽기(GET) 작업 또는 Azure Portal에서 수행되었거나 원본 Management API를 사용하는 작업이 포함되지 않습니다.

API Management 서비스에서 활동 로그에 액세스하거나 Azure Monitor에서 모든 Azure 리소스의 로그에 액세스할 수 있습니다. 

![활동 로그](./media/api-management-azure-monitor/apim-monitor-activity-logs.png)

활동 로그를 보려면

1. APIM 서비스 인스턴스를 선택합니다.
2. **활동 로그**를 클릭합니다.

    ![활동 로그](./media/api-management-azure-monitor/api-management-activity-logs-blade.png)

3. 원하는 필터링 범위를 선택하고 **적용**을 클릭합니다.

## <a name="diagnostic-logs"></a>진단 로그

진단 로그는 감사 뿐만 아니라 문제 해결에 중요한 작업 및 오류에 대한 풍부한 정보를 제공합니다. 진단 로그는 활동 로그와 다릅니다. 활동 로그는 Azure 리소스에서 수행된 작업에 대한 정보를 제공합니다. 진단 로그는 리소스에서 수행하는 작업에 대한 정보를 제공합니다.

진단 로그를 구성하려면:

1. APIM 서비스 인스턴스를 선택합니다.
2. **진단 설정**을 클릭합니다.

    ![진단 로그](./media/api-management-azure-monitor/api-management-diagnostic-logs-blade.png)

3. **진단 켜기**를 클릭합니다. 진단 로그를 메트릭과 함께 스토리지 계정에 보관하고, Event Hub로 스트림하고, Azure Monitor 로그로 보낼 수 있습니다. 

API Management는 현재 다음 스키마를 갖는 각 항목으로 개별 API 요청에 대한 진단 로그(시간 단위로 일괄 처리됨)를 제공합니다.

```json
{  
    "isRequestSuccess" : "",
    "time": "",
    "operationName": "",
    "category": "",
    "durationMs": ,
    "callerIpAddress": "",
    "correlationId": "",
    "location": "",
    "httpStatusCodeCategory": "",
    "resourceId": "",
    "properties": {   
        "method": "", 
        "url": "", 
        "clientProtocol": "", 
        "responseCode": , 
        "backendMethod": "", 
        "backendUrl": "", 
        "backendResponseCode": ,
        "backendProtocol": "",  
        "requestSize": , 
        "responseSize": , 
        "cache": "", 
        "cacheTime": "", 
        "backendTime": , 
        "clientTime": , 
        "apiId": "",
        "operationId": "", 
        "productId": "", 
        "userId": "", 
        "apimSubscriptionId": "", 
        "backendId": "",
        "lastError": { 
            "elapsed" : "", 
            "source" : "", 
            "scope" : "", 
            "section" : "" ,
            "reason" : "", 
            "message" : ""
        } 
    }      
}  
```

| 자산  | Type | 설명 |
| ------------- | ------------- | ------------- |
| isRequestSuccess | 부울 | HTTP 요청이 완료되고 응답 상태 코드가 2xx 또는 3xx 범위 이내이면 True입니다. |
| 실시간 | 날짜-시간 | 게이트웨이에서 HTTP 요청을 수신하는 타임스탬프 |
| operationName | string | 상수 값 'Microsoft.ApiManagement/GatewayLogs' |
| 카테고리 | string | 상수 값 'GatewayLogs' |
| durationMS | 정수 | 게이트웨이에서 요청을 수신한 순간부터 응답이 완전히 전송될 때까지 걸린 시간(밀리초) |
| callerIpAddress | string | 즉각적인 게이트웨이 호출자의 IP 주소(중간자 가능) |
| CorrelationId | string | API Management에서 할당하는 고유의 http 요청 식별자 |
| location | string | 요청을 처리한 게이트웨이가 있었던 Azure 지역의 이름 |
| httpStatusCodeCategory | string | http 응답 상태 코드의 범주: 성공(301 이하, 304 또는 307), 권한이 없음(401, 403, 429), 오류가 있음(400, 500~600), 기타 |
| ResourceId | string | API Management 리소스의 ID /SUBSCRIPTIONS/\<subscription>/RESOURCEGROUPS/\<resource-group>/PROVIDERS/MICROSOFT.APIMANAGEMENT/SERVICE/\<name> |
| properties | object | 현재 요청의 속성 |
| 메서드 | string | 들어오는 요청의 HTTP 메서드 |
| URL | string | 들어오는 요청의 URL |
| clientProtocol | string | 들어오는 요청의 HTTP 프로토콜 버전 |
| responseCode | 정수 | 클라이언트로 전송된 HTTP 응답의 상태 코드 |
| backendMethod | string | 백 엔드로 전송된 요청의 HTTP 메서드 |
| backendUrl | string | 백 엔드로 전송된 요청의 URL |
| backendResponseCode | 정수 | 백 엔드에서 받은 HTTP 응답 코드 |
| backendProtocol | string | 백 엔드로 전송된 요청의 HTTP 프로토콜 버전 | 
| requestSize | 정수 | 요청을 처리하는 동안 클라이언트에서 받은 바이트 수 | 
| responseSize | 정수 | 요청을 처리하는 동안 클라이언트로 전송된 바이트 수 | 
| 캐시 | string | 요청 처리에서 API Management 캐시 개입 상태(적중, 놓침, 없음) | 
| cacheTime | 정수 | 전체 API Management 캐시 IO(연결, 바이트 송신 및 수신)에 소요된 시간(밀리초) | 
| backendTime | 정수 | 전체 백 엔드 IO(연결, 바이트 송신 및 수신)에 소요된 시간(밀리초) | 
| clientTime | 정수 | 전체 클라이언트 IO(연결, 바이트 송신 및 수신)에 소요된 시간(밀리초) | 
| apiId | string | 현재 요청에 대한 API 엔터티 식별자 | 
| operationId | string | 현재 요청에 대한 작업 엔터티 식별자 | 
| productId | string | 현재 요청에 대한 제품 엔터티 식별자 | 
| userId | string | 현재 요청에 대한 사용자 엔터티 식별자 | 
| apimSubscriptionId | string | 현재 요청에 대한 구독 엔터티 식별자 | 
| backendId | string | 현재 요청에 대한 백 엔드 엔터티 식별자 | 
| LastError | object | 마지막 요청 처리 오류 | 
| elapsed | 정수 | 게이트웨이에서 요청을 받은 순간부터 오류가 발생한 순간까지 경과한 시간(밀리초) | 
| 원본 | string | 오류를 발생시킨 정책 또는 처리 내부 처리기의 이름 | 
| scope | string | 오류를 발생시킨 정책이 포함되어 있는 정책 문서의 범위 | 
| section | string | 오류를 발생시킨 정책이 포함되어 있는 정책 문서의 섹션 | 
| reason | string | 오류 원인 | 
| Message | string | 오류 메시지 | 

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 활동 로그 보기
> * 진단 로그 보기
> * API의 메트릭 보기
> * API가 무단 호출을 받을 경우의 경고 규칙 설정

다음 자습서를 진행합니다.

> [!div class="nextstepaction"]
> [호출 추적](api-management-howto-api-inspector.md)
