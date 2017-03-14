---
title: "Azure Service Bus 진단 로그 | Microsoft Docs"
description: "Microsoft Azure에서 Service Bus의 진단 로그를 분석하는 방법을 알아봅니다."
keywords: 
documentationcenter: 
services: service-bus-messaging
author: banisadr
manager: 
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/17/2017
ms.author: babanisa
translationtype: Human Translation
ms.sourcegitcommit: 90321171586110a3b60c3df5b749003ebbf70ec9
ms.openlocfilehash: 70205b33e9d52e41f5c1a637fee4da192e2a971a
ms.lasthandoff: 02/22/2017


---
# <a name="service-bus-diagnostic-logs"></a>Service Bus 진단 로그

## <a name="introduction"></a>소개
Service Bus에서는 두 가지 유형의 로그를 제공합니다. 
* [활동 로그:](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) 항상 사용되며 작업(job)에 대해 수행한 작업(operation)에 대한 정보를 제공합니다.
* [진단 로그:](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) 사용자 구성 가능하며 작업이 실행되어 삭제될 때까지 생성, 업데이트된 작업의 작업 시작과 함께 발생한 모든 항목에 대한 풍부한 정보를 제공합니다.

## <a name="how-to-enable-diagnostic-logs"></a>진단 로그를 사용하도록 설정하는 방법
진단 로그는 기본적으로 **해제**됩니다. 사용하도록 설정하려면 다음 단계를 따르세요.

Azure Portal에 로그인하고 스트리밍 작업 블레이드로 이동하고 "모니터링" 아래에서 "진단 로그" 블레이드를 사용합니다.

![진단 로그에 대한 블레이드 탐색](./media/service-bus-diagnostic-logs/image1.png)  

그런 다음 "진단 사용" 링크를 클릭합니다.

![진단 로그 사용](./media/service-bus-diagnostic-logs/image2.png)

열린 진단에서 상태를 "On(켜기)"으로 변경합니다.

![진단 로그 상태 변경](./media/service-bus-diagnostic-logs/image3.png)

원하는 보관 대상(저장소 계정, 이벤트 허브, Log Analytics)을 구성하고 수집할 로그의 범주(실행, 작성)를 선택합니다. 그런 다음 새 진단 구성을 저장합니다.

저장하면 구성이 10분 후에 적용되고 그 후 로그가 구성된 보관 대상에 나타납니다("진단 로그" 블레이드에서 확인 가능).

진단 구성에 대한 자세한 내용은 [진단 로그](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) 페이지에서 확인할 수 있습니다.

## <a name="diagnostic-logs-schema"></a>진단 로그 스키마

모든 로그는 JSON 형식으로 저장되며 각 항목에는 아래 형식의 문자열 필드가 있습니다.

### <a name="operation-logs"></a>작업 로그

OperationalLogs는 Service Bus 작업 중에 발생하는 결과, 특히 큐 만들기, 사용된 리소스 및 작업 상태와 같은 작업 유형을 캡처합니다.

이름 | 설명
------- | -------
ActivityId | 추적 용도의 내부 ID
EventName | 작업 이름             
resourceId | ARM 리소스 ID
SubscriptionId | 구독 ID
EventTimeString | 작업 시간
EventProperties | 작업 속성
가동 상태 | 작업 상태
Caller | 작업 호출자(포털 또는 관리 클라이언트)
카테고리 | OperationalLogs

#### <a name="example-operation-log"></a>예제 작업 로그

```json
Example: 
{
     "ActivityId": "6aa994ac-b56e-4292-8448-0767a5657cc7",
     "EventName": "Create Queue",
     "resourceId": "/SUBSCRIPTIONS/1A2109E3-9DA0-455B-B937-E35E36C1163C/RESOURCEGROUPS/DEFAULT-SERVICEBUS-CENTRALUS/PROVIDERS/MICROSOFT.SERVICEBUS/NAMESPACES/SHOEBOXEHNS-CY4001",
     "SubscriptionId": "1a2109e3-9da0-455b-b937-e35e36c1163c",
     "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
     "EventProperties": "{\"SubscriptionId\":\"1a2109e3-9da0-455b-b937-e35e36c1163c\",\"Namespace\":\"shoeboxehns-cy4001\",\"Via\":\"https://shoeboxehns-cy4001.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
     "Status": "Succeeded",
     "Caller": "ServiceBus Client",
     "category": "OperationalLogs"
}
```

## <a name="next-steps"></a>다음 단계
* [Service Bus 소개](service-bus-messaging-overview.md)
* [Service Bus 시작](service-bus-create-namespace-portal.md)

