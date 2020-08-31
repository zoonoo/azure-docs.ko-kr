---
title: Azure Monitor에서 Azure Service Bus 메트릭 | Microsoft Docs
description: 이 문서에서는 Azure Monitor를 사용하여 Service Bus 엔터티(큐, 토픽 및 구독)를 모니터링하는 방법을 설명합니다.
ms.topic: article
ms.date: 07/15/2020
ms.openlocfilehash: 3081b46bebdba8e83e5584178b37aab2dffee599
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88065015"
---
# <a name="azure-service-bus-metrics-in-azure-monitor"></a>Azure Monitor에서 Azure Service Bus 메트릭

Service Bus 메트릭은 Azure 구독에서 리소스의 상태를 제공합니다. 풍부한 메트릭 데이터 집합을 사용하여 네임스페이스 수준에서뿐만 아니라 엔터티 수준에서 Service Bus 리소스의 전반적인 상태를 평가할 수 있습니다. 이러한 통계는 Service Bus의 상태를 모니터링하는 데 도움을 주므로 중요할 수 있습니다. Azure 지원에 문의할 필요 없이 메트릭을 통해 근본 원인 문제를 해결할 수도 있습니다.

Azure Monitor는 다양한 Azure 서비스를 모니터링하기 위한 통합된 사용자 인터페이스를 제공합니다. 자세한 내용은 GitHub의 [Microsoft Azure에서 모니터링](../azure-monitor/overview.md) 및 [.NET을 사용하여 Azure Monitor 메트릭 검색](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) 샘플을 참조하세요.

> [!IMPORTANT]
> 2시간 동안 엔터티와 상호 작용이 없으면 엔터티가 더 이상 유휴 상태가 되지 않을 때까지 메트릭은 값으로 "0" 표시를 시작합니다.

## <a name="access-metrics"></a>메트릭에 액세스

Azure Monitor는 메트릭에 액세스하는 여러 가지 방법을 제공합니다. [Azure Portal](https://portal.azure.com)을 통해 메트릭에 액세스하거나 Azure Monitor API(REST 및 .NET) 및 Azure Monitor 로그 및 Event Hubs 같은 분석 솔루션을 사용할 수 있습니다. 자세한 내용은 [Azure Monitor의 메트릭](../azure-monitor/platform/data-platform-metrics.md)을 참조하세요.

메트릭은 기본적으로 활성화되며 최근 30일분 데이터에 액세스할 수 있습니다. 더 오랜 기간에 대한 데이터를 보존해야 하는 경우 메트릭 데이터를 Azure Storage 계정에 보관할 수 있습니다. 이 값은 Azure Monitor의 [진단 설정](../azure-monitor/platform/diagnostic-settings.md)에서 구성합니다.

## <a name="access-metrics-in-the-portal"></a>포털에서 메트릭에 액세스

[Azure Portal](https://portal.azure.com)에서 시간 경과에 따른 메트릭을 모니터링할 수 있습니다. 다음 예제에서는 성공한 요청 및 계정 수준에서 들어오는 요청을 확인하는 방법을 보여 줍니다.

![Azure Portal의 모니터-메트릭 (미리 보기) 페이지 스크린샷][1]

네임스페이스를 통해 메트릭에 직접 액세스할 수도 있습니다. 이렇게 하려면 네임스페이스를 선택한 다음, **Metrics**을 클릭합니다. 엔터티의 범위에 대해 필터링된 메트릭을 표시하려면 엔터티를 선택한 다음 **메트릭**을 클릭합니다.

![엔터티 범위로 필터링 된 모니터 메트릭 (미리 보기) 페이지의 스크린샷][2]

차원을 지원하는 메트릭의 경우 원하는 차원 값을 사용하여 필터링해야 합니다.

## <a name="billing"></a>결제

Azure Monitor에 대한 메트릭 및 경고는 경고 단위로 요금이 부과됩니다. 이러한 요금은 경고를 설정 하 고 저장 하기 전에 포털에서 사용할 수 있어야 합니다. 

메트릭 데이터를 수집하는 추가 솔루션 비용은 해당 솔루션에 의해 직접 청구됩니다. 예를 들어 메트릭 데이터를 Azure Storage 계정에 보관하는 경우 Azure Storage에서 요금을 청구합니다. 고급 분석을 위해 Log Analytics에 메트릭 데이터를 스트리밍할 경우 Log Analytics에서 요금을 청구합니다.

다음 메트릭은 서비스의 상태에 대한 개요를 제공합니다. 

> [!NOTE]
> 다른 이름으로 이동되므로 여러 가지 메트릭을 사용 중단하는 중입니다. 참조를 업데이트해야 할 수도 있습니다. “사용되지 않음” 키워드로 표시된 메트릭은 앞으로 지원되지 않습니다.

모든 메트릭 값은 매분마다 Azure Monitor에 전송됩니다. 시간 세분성은 메트릭 값이 표시되는 시간 간격을 정의합니다. 모든 Service Bus 메트릭에 대해 지원되는 시간 간격은 1분입니다.

## <a name="request-metrics"></a>요청 메트릭

데이터 및 관리 작업 요청 수를 계산합니다.

| 메트릭 이름 | Description |
| ------------------- | ----------------- |
| 들어오는 요청| 지정된 기간 동안 Service Bus 서비스에 대한 요청 수입니다. <br/><br/> 단위: 개수 <br/> 집계 유형: 합계 <br/> 차원: 엔터티 이름|
|성공한 요청|지정된 기간 동안 Service Bus 서비스에 대한 성공한 요청 수입니다.<br/><br/> 단위: 개수 <br/> 집계 유형: 합계 <br/> 차원: 엔터티 이름|
|서버 오류|지정된 기간 동안 Service Bus 서비스에서 오류로 인해 처리되지 않은 요청 수입니다.<br/><br/> 단위: 개수 <br/> 집계 유형: 합계 <br/> 차원: 엔터티 이름|
|사용자 오류(다음 하위 섹션 참조)|지정된 기간 동안 사용자 오류로 인해 처리되지 않은 요청 수입니다.<br/><br/> 단위: 개수 <br/> 집계 유형: 합계 <br/> 차원: 엔터티 이름|
|제한된 요청|사용량 초과로 인해 제한된 요청 수입니다.<br/><br/> 단위: 개수 <br/> 집계 유형: 합계 <br/> 차원: 엔터티 이름|

### <a name="user-errors"></a>사용자 오류

다음 두 가지 유형의 오류는 사용자 오류로 분류됩니다.

1. 클라이언트 쪽 오류(HTTP의 경우 400 오류)
2. [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception)과 같은 메시지를 처리하는 동안 발생하는 오류입니다.


## <a name="message-metrics"></a>메시지 메트릭

| 메트릭 이름 | Description |
| ------------------- | ----------------- |
|들어오는 메시지|지정된 기간 동안 Service Bus에 전송된 이벤트 또는 메시지 수입니다.<br/><br/> 단위: 개수 <br/> 집계 유형: 합계 <br/> 차원: 엔터티 이름|
|보내는 메시지|지정된 기간 동안 Service Bus에서 수신한 이벤트 또는 메시지 수입니다.<br/><br/> 단위: 개수 <br/> 집계 유형: 합계 <br/> 차원: 엔터티 이름|
| 메시지| 큐/토픽에 있는 메시지 수 <br/><br/> 단위: 개수 <br/> 집계 유형: 평균 <br/> 차원: 엔터티 이름 |
| 활성 메시지| 큐/토픽에 있는 활성 메시지 수 <br/><br/> 단위: 개수 <br/> 집계 유형: 평균 <br/> 차원: 엔터티 이름 |
| 배달 못한 메시지| 큐/토픽에서 배달 못한 메시지 수입니다. <br/><br/> 단위: 개수 <br/> 집계 유형: 평균 <br/>차원: 엔터티 이름 |
| 예약된 메시지| 큐/토픽에서 예약된 메시지 수입니다. <br/><br/> 단위: 개수 <br/> 집계 유형: 평균  <br/> 차원: 엔터티 이름 |
| 크기 | 엔터티 (큐 또는 토픽)의 크기 (바이트)입니다. <br/><br/>단위: 개수 <br/>집계 유형: 평균 <br/>차원: 엔터티 이름 | 

> [!NOTE]
> 다음 메트릭의 값은 지정 시간 값입니다. 해당 지점 시간 직후에 사용된 들어오는 메시지는 이러한 메트릭에 반영되지 않을 수 있습니다. 
> - 메시지
> - 활성 메시지 
> - 배달 못한 메시지 
> - 예약된 메시지 

## <a name="connection-metrics"></a>연결 메트릭

| 메트릭 이름 | Description |
| ------------------- | ----------------- |
|활성 연결 수|네임스페이스와 엔터티의 활성 연결 수입니다.<br/><br/> 단위: 개수 <br/> 집계 유형: 합계 <br/> 차원: 엔터티 이름|
|열린 연결 |열린 연결 수입니다.<br/><br/> 단위: 개수 <br/> 집계 유형: 합계 <br/> 차원: 엔터티 이름|
|닫힌 연결 |닫힌 연결 수입니다.<br/><br/> 단위: 개수 <br/> 집계 유형: 합계 <br/> 차원: 엔터티 이름|

## <a name="resource-usage-metrics"></a>리소스 사용량 메트릭

> [!NOTE] 
> 다음 메트릭은 **프리미엄** 계층에서만 제공됩니다. 
> 
> 프리미엄 계층 네임스페이스의 작동 중단을 모니터링하는 중요 메트릭은 **네임스페이스당 CPU 사용량** 및 **네임스페이스당 메모리 크기**입니다. Azure Monitor를 사용하여 이러한 메트릭에 대한 [경고를 설정](../azure-monitor/platform/alerts-metric.md)합니다.
> 
> 모니터링할 수 있는 다른 메트릭은 **제한된 요청**입니다. 그렇지만 네임스페이스가 해당 메모리, CPU 및 조정된 연결 제한을 벗어나지 않으면 문제가 되지 않습니다. 자세한 내용은 [Azure Service Bus 프리미엄 계층의 제한](service-bus-throttling.md#throttling-in-azure-service-bus-premium-tier)을 참조하세요.

| 메트릭 이름 | Description |
| ------------------- | ----------------- |
|네임스페이스당 CPU 사용량|네임스페이스의 CPU 사용량 비율입니다.<br/><br/> 단위: 백분율 <br/> 집계 유형: 최대 <br/> 차원: 엔터티 이름|
|네임스페이스당 메모리 크기 사용량|네임스페이스의 메모리 사용량 비율입니다.<br/><br/> 단위: 백분율 <br/> 집계 유형: 최대 <br/> 차원: 엔터티 이름|

## <a name="metrics-dimensions"></a>메트릭 차원

Azure Service Bus는 Azure Monitor의 메트릭에 대해 다음과 같은 차원을 지원합니다. 메트릭에 차원을 추가하는 것은 선택 사항입니다. 차원을 추가하지 않는 경우 메트릭은 네임스페이스 수준에서 지정됩니다. 

|차원 이름|Description|
| ------------------- | ----------------- |
|엔터티 이름| Service Bus는 네임스페이스에서 메시징 엔터티를 지원합니다.|

## <a name="set-up-alerts-on-metrics"></a>메트릭에 대한 경고 설정

1. **Service Bus 네임스페이스** 페이지의 **메트릭** 탭에서 **경고 구성**을 선택합니다. 

    ![메트릭 페이지 - 경고 메뉴 구성](./media/service-bus-metrics-azure-monitor/metrics-page-configure-alerts-menu.png)
2. **대상 선택** 옵션을 선택하고, **리소스 선택** 페이지에서 다음 작업을 수행합니다. 
    1. **리소스 종류별로 필터링** 필드에서 **Service Bus 네임스페이스**를 선택합니다. 
    2. **구독별로 필터링** 필드에서 구독을 선택합니다.
    3. 목록에서 **Service Bus 네임스페이스**를 선택합니다. 
    4. **완료** 를 선택합니다. 
    
        ![네임스페이스 선택](./media/service-bus-metrics-azure-monitor/select-namespace.png)
1. **조건 추가**를 선택하고, **신호 논리 구성** 페이지에서 다음 작업을 수행합니다.
    1. **신호 형식**으로 **메트릭**을 선택합니다. 
    2. 신호를 선택합니다. 다음은 그 예입니다.  **서비스 오류** 

        ![서버 오류 선택](./media/service-bus-metrics-azure-monitor/select-server-errors.png)
    1. **조건**으로 **다음보다 큼**을 선택합니다.
    2. **시간 집계**로 **전체**를 선택합니다. 
    3. **임계값**으로 **5**를 입력합니다. 
    4. **완료** 를 선택합니다.    

        ![조건 지정](./media/service-bus-metrics-azure-monitor/specify-condition.png)    
1. **규칙 만들기** 페이지에서 **경고 세부 정보 정의**를 확장하고 다음 작업을 수행합니다.
    1. 경고의 **이름**을 입력합니다. 
    2. 경고에 대한 **설명** 을 입력합니다.
    3. 경고의 **심각도**를 선택합니다. 

        ![경고 세부 정보](./media/service-bus-metrics-azure-monitor/alert-details.png)
1. **규칙 만들기** 페이지에서 **작업 그룹 정의**를 선택하고, **새 작업 그룹**을 선택하고, **작업 그룹 추가 페이지**에서 다음 작업을 수행합니다. 
    1. 작업 그룹의 이름을 입력합니다.
    2. 작업 그룹의 짧은 이름을 입력합니다. 
    3. 구독을 선택합니다. 
    4. 리소스 그룹을 선택합니다. 
    5. 이 연습에서는 **작업 이름**으로 **이메일 보내기**를 입력합니다.
    6. **작업 형식**으로 **이메일/SMS/푸시/음성**을 선택합니다. 
    7. **세부 정보 편집**을 선택합니다. 
    8. **이메일/SMS/푸시/음성** 페이지에서 다음 작업을 수행합니다.
        1. **이메일**을 선택합니다. 
        2. **이메일 주소**를 입력합니다. 
        3. **확인**을 선택합니다.

            ![경고 세부 정보](./media/service-bus-metrics-azure-monitor/add-action-group.png)
        4. **작업 그룹 추가** 페이지에서 **확인**을 선택합니다. 
1. **규칙 만들기** 페이지에서 **경고 규칙 만들기**를 선택합니다. 

    ![경고 규칙 만들기 단추](./media/service-bus-metrics-azure-monitor/create-alert-rule.png)

## <a name="next-steps"></a>다음 단계

[Azure Monitor 개요](../azure-monitor/overview.md)를 참조하세요.

[1]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor1.png
[2]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor2.png