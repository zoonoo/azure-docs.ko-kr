---
title: Event Grid 문제 해결
description: 이 문서에서는 Azure Event Grid 문제를 해결 하는 다양 한 방법을 제공 합니다.
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: 9c52ba8561c10dd94ec6ef51c78b8534c6c58e96
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100417599"
---
# <a name="troubleshoot-azure-event-grid-issues"></a>Azure Event Grid 문제 해결
이 문서에서는 Azure Event Grid 문제를 해결 하는 데 도움이 되는 정보를 제공 합니다. 

## <a name="diagnostic-logs"></a>진단 로그
게시 및 배달 실패 로그를 캡처하고 볼 수 있도록 Event Grid 토픽 또는 도메인에 대 한 진단 설정을 사용 하도록 설정 합니다. 자세한 내용은 [진단 로그](enable-diagnostic-logs-topic.md)를 참조 하세요.

## <a name="metrics"></a>메트릭
토픽 및 구독에 대 한 메트릭을 확인 하 고이에 대 Event Grid 한 경고를 만들 수 있습니다. 자세한 내용은 [Event Grid 메트릭](monitor-event-delivery.md)을 참조 하세요.

## <a name="alerts"></a>경고
Azure Event Grid 메트릭 및 활동 로그 작업에 대 한 경고를 만듭니다. 자세한 내용은 [Event Grid 메트릭 및 활동 로그에 대 한 경고](set-alerts.md)를 참조 하세요.

## <a name="subscription-validation-issues"></a>구독 유효성 검사 문제
이벤트 구독을 만드는 동안 제공 된 끝점의 유효성 검사가 실패 했다는 오류 메시지가 표시 될 수 있습니다. 구독 유효성 검사 문제를 해결 하려면 [구독 유효성 검사 Event Grid 문제 해결](troubleshoot-subscription-validation.md)을 참조 하세요. 

## <a name="network-connectivity-issues"></a>네트워크 연결 문제
클라이언트 응용 프로그램에서 Event Grid 토픽/도메인에 연결할 수 없는 다양 한 이유가 있습니다. 발생 하는 연결 문제는 영구적 이거나 일시적일 수 있습니다. 이러한 문제를 해결 하는 방법에 대 한 자세한 내용은 [연결 문제 해결](troubleshoot-network-connectivity.md)을 참조 하세요.

## <a name="error-codes"></a>오류 코드
400, 409 및 403와 같은 오류 코드를 포함 하는 오류 메시지가 표시 되 면 [Event Grid 오류 문제 해결](troubleshoot-errors.md)을 참조 하세요. 

## <a name="distributed-tracing-net"></a>분산 추적 (.NET)
Event Grid .NET 라이브러리는 추적 배포를 지원 합니다. 추적 배포에 대 한 [CloudEvents 사양의 지침](https://github.com/cloudevents/spec/blob/master/extensions/distributed-tracing.md) 을 준수 하기 위해 라이브러리는 `traceparent` 분산 추적을 사용 하는 `tracestate` 경우의 [extensionattributes](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventgrid/Azure.Messaging.EventGrid/src/Customization/CloudEvent.cs#L126) 에 및를 설정 `CloudEvent` 합니다. 응용 프로그램에서 분산 추적을 사용 하도록 설정 하는 방법에 대 한 자세한 내용은 Azure SDK [분산 추적 설명서](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Azure.Core/samples/Diagnostics.md#Distributed-tracing)를 참조 하세요.

### <a name="sample"></a>샘플
[줄 카운터 샘플](/samples/azure/azure-sdk-for-net/line-counter/)을 참조 하세요. 이 샘플 앱에서는 저장소, Event Hubs 및 Event Grid 클라이언트를 ASP.NET Core 통합, 분산 추적 및 호스팅된 서비스와 함께 사용 하는 방법을 보여 줍니다. 이를 통해 사용자는 파일 이름을 포함 하는 Event Hubs 이벤트를 트리거하는 blob에 파일을 업로드할 수 있습니다. Event Hubs 프로세서는 이벤트를 받은 다음 앱에서 blob을 다운로드 하 고 파일의 줄 수를 계산 합니다. 앱은 줄 수를 포함 하는 페이지에 대 한 링크를 표시 합니다. 링크를 클릭 하면 Event Grid를 사용 하 여 파일 이름을 포함 하는 CloudEvent이 게시 됩니다.

## <a name="next-steps"></a>다음 단계
도움이 더 필요한 경우 [Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-eventgrid)에 문제를 게시하거나 [지원 티켓](https://azure.microsoft.com/support/options/)을 여세요. 
