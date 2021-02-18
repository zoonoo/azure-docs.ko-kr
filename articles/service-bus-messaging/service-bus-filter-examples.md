---
title: Azure Service Bus |에서 구독 필터 설정 Microsoft Docs
description: 이 문서에서는 Azure Service Bus 토픽 구독에 대 한 필터 및 작업을 정의 하는 예제를 제공 합니다.
ms.topic: how-to
ms.date: 02/17/2021
ms.openlocfilehash: bcbb72901ed8e2dfe0932163ee18683e0011ce70
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/18/2021
ms.locfileid: "100654392"
---
# <a name="set-subscription-filters-azure-service-bus"></a>구독 필터 설정 (Azure Service Bus)
이 문서에서는 Service Bus 토픽 구독에 대 한 필터 설정에 대 한 몇 가지 예를 제공 합니다. 필터에 대 한 개념 정보는 [필터](topic-filters.md)를 참조 하세요.

## <a name="filter-on-system-properties"></a>시스템 속성에 대 한 필터링
필터에서 시스템 속성을 참조 하려면 다음 형식을 사용 `sys.<system-property-name>` 합니다.. 

```csharp
sys.label LIKE '%bus%'`
sys.messageid = 'xxxx'
sys.correlationid like 'abc-%'
```

## <a name="filter-on-message-properties"></a>메시지 속성에 대 한 필터
다음은 필터에서 메시지 속성을 사용 하는 예입니다. 또는만 사용 하 여 메시지 속성에 액세스할 수 있습니다 `user.property-name` `property-name` .

```csharp
MessageProperty = 'A'
SuperHero like 'SuperMan%'
```

## <a name="filter-on-message-properties-with-special-characters"></a>특수 문자를 사용 하 여 메시지 속성 필터링
메시지 속성 이름에 특수 문자가 있으면 큰따옴표 ()를 사용 `"` 하 여 속성 이름을 묶습니다. 예를 들어 속성 이름이 이면 `"http://schemas.microsoft.com/xrm/2011/Claims/EntityLogicalName"` 필터에서 다음 구문을 사용 합니다. 

```csharp
"http://schemas.microsoft.com/xrm/2011/Claims/EntityLogicalName" = 'account'
```

## <a name="filter-on-message-properties-with-numeric-values"></a>숫자 값을 사용 하 여 메시지 속성 필터링
다음 예에서는 필터에 숫자 값이 있는 속성을 사용 하는 방법을 보여 줍니다. 

```csharp
MessageProperty = 1
MessageProperty > 1
MessageProperty > 2.08
MessageProperty = 1 AND MessageProperty2 = 3
MessageProperty = 1 OR MessageProperty2 = 3
```

## <a name="parameter-based-filters"></a>매개 변수 기반 필터
매개 변수 기반 필터를 사용 하는 몇 가지 예는 다음과 같습니다. 이 예제에서 `DataTimeMp` 는 형식의 메시지 속성 `DateTime` 이며는 `@dtParam` 필터에 개체로 전달 되는 매개 변수입니다 `DateTime` .

```csharp
DateTimeMp < @dtParam
DateTimeMp > @dtParam

(DateTimeMp2-DateTimeMp1) <= @timespan //@timespan is a parameter of type TimeSpan
DateTimeMp2-DateTimeMp1 <= @timespan
```

## <a name="using-in-and-not-in"></a>IN 및 NOT IN 사용

```csharp
StoreId IN('Store1', 'Store2', 'Store3')"

sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'

sys.To NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8')
```

C # 샘플은 [GitHub의 토픽 필터 샘플](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Azure.Messaging.ServiceBus/BasicSendReceiveTutorialwithFilters)을 참조 하세요.


## <a name="correlation-filter-using-correlationid"></a>CorrelationID를 사용 하 여 상관 관계 필터

```csharp
new CorrelationFilter("Contoso");
```

가로 설정 된 메시지 `CorrelationID` 를 필터링 `Contoso` 합니다. 

## <a name="correlation-filter-using-system-and-user-properties"></a>시스템 및 사용자 속성을 사용한 상관 관계 필터

```csharp
var filter = new CorrelationFilter();
filter.Label = "Important";
filter.ReplyTo = "johndoe@contoso.com";
filter.Properties["color"] = "Red";
```

다음과 같습니다. `sys.ReplyTo = 'johndoe@contoso.com' AND sys.Label = 'Important' AND color = 'Red'`

## <a name="next-steps"></a>다음 단계
다음 샘플을 참조하세요. 

- [.NET-필터가 포함 된 기본 송신 및 수신 자습서](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/BasicSendReceiveTutorialwithFilters/BasicSendReceiveTutorialWithFilters)
- [.NET 토픽 필터](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TopicFilters)
- [Azure Resource Manager 템플릿](/azure/templates/microsoft.servicebus/2017-04-01/namespaces/topics/subscriptions/rules)