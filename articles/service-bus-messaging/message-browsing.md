---
title: Azure Service Bus 메시지 검색
description: Service Bus 메시지 검색 및 피킹을 사용하여 Azure Service Bus 클라이언트에서 큐 또는 구독의 모든 메시지를 열거할 수 있습니다.
ms.topic: article
ms.date: 03/29/2021
ms.openlocfilehash: 23ec2187ae58b1b4f83addb80573bdb130ff99bd
ms.sourcegitcommit: aba63ab15a1a10f6456c16cd382952df4fd7c3ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/25/2021
ms.locfileid: "107989399"
---
# <a name="message-browsing"></a>메시지 찾아보기
메시지 검색 또는 피킹을 사용하여 Service Bus 클라이언트는 큐 또는 구독의 모든 메시지를 진단 및 디버깅 용도로 열거할 수 있습니다.

큐 또는 구독에 대한 피킹 작업은 요청된 메시지 수를 반환합니다. 다음 표에서는 피킹 작업에서 반환되는 메시지 유형을 보여 줍니다. 

| 메시지 유형 | 포함 여부 | 
| ---------------- | ----- | 
| 활성 메시지 | 예 |
| 배달 못한 메시지 | 예 | 
| 잠긴 메시지 | 예 |
| 만료된 메시지 |  (배달 못한 편지로 처리되기 전) 일 수 있습니다. |
| 예약된 메시지 | 큐의 경우 예입니다. 구독의 경우 아니요 |

## <a name="dead-lettered-messages"></a>배달 못한 메시지
큐 또는 구독의 **배달 못한 편지로 처리됨** 메시지로 피킹하려면 큐 또는 구독과 연결된 배달 못한 큐에서 피킹 작업을 실행해야 합니다. 자세한 내용은 [배달 못한 편지 큐 액세스](service-bus-dead-letter-queues.md#path-to-the-dead-letter-queue)를 참조하세요.

## <a name="expired-messages"></a>만료된 메시지
만료된 메시지는 피킹 작업에서 반환된 결과에 포함될 수 있습니다. 사용된 메시지 및 만료된 메시지는 비동기 "가비지 수집"을 실행하여 정리됩니다. 이 단계가 메시지 만료 직후에 반드시 발생하는 것은 아닙니다. 따라서 피킹 작업은 이미 만료된 메시지를 반환할 수 있습니다. 이런 메시지는 다음에 큐 또는 구독에서 수신 작업이 호출될 때 제거되거나 배달되지 못한 편지로 처리됩니다. 지연된 메시지를 큐에서 복구하려고 시도할 때 이런 동작에 유의해야 합니다. 

만료된 메시지는 어떠한 방법으로도(Peek에 의해 반환되는 경우에도) 일반 검색을 수행할 수 없습니다. Peek은 로그의 현재 상태를 반영하는 진단 도구이기 때문에 해당 메시지를 반환하는 것은 의도적입니다.

## <a name="locked-messages"></a>잠긴 메시지
또한, 피킹은 **잠겨서** 현재 다른 수신기가 처리하는 메시지를 반환합니다. 그러나 Peek은 연결이 끊긴 스냅샷을 반환하기 때문에 피킹 메시지에서 메시지의 잠금 상태를 관찰할 수 없습니다.

## <a name="peek-apis"></a>Peek API
피킹은 큐, 구독 및 배달 못한 메시지 큐에서 작동합니다. 

반복적으로 호출되면 피킹 작업은 큐 또는 구독에 있는 모든 메시지를 순서대로(가장 낮은 시퀀스 번호에서 가장 높은 시퀀스 번호로) 열거합니다. 다음은 메시지를 큐에 넣는 순서이며 최종적으로 메시지가 검색되는 순서는 아닙니다.

또한 피킹 작업에 SequenceNumber를 전달할 수 있습니다. 피킹을 시작할 위치를 결정하는 데 사용됩니다. 추가로 열거할 매개 변수를 지정하지 않고도 피킹 작업에 대한 후속 호출을 수행할 수 있습니다.

## <a name="next-steps"></a>다음 단계
선택한 언어로 샘플을 사용하여 Azure Service Bus 기능을 살펴봅니다. 

- [.NET용 Azure Service Bus 클라이언트 라이브러리 샘플(최신)](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/) - - **메시지 보내기 및 받기** 샘플
- [Java용 Azure Service Bus 클라이언트 라이브러리 샘플(최신)](/samples/azure/azure-sdk-for-java/servicebus-samples/) - **메시지 보기** 샘플
- [Python용 Azure Service Bus 클라이언트 라이브러리 샘플](/samples/azure/azure-sdk-for-python/servicebus-samples/)  - **receive_peek.py** 샘플
- [JavaScript용 Azure Service Bus 클라이언트 라이브러리 샘플](/samples/azure/azure-sdk-for-js/service-bus-javascript/)  - **browseMessages.js** 샘플
- [TypeScript용 Azure Service Bus 클라이언트 라이브러리 샘플](/samples/azure/azure-sdk-for-js/service-bus-typescript/) - **browseMessages.ts** 샘플

이전 .NET 및 Java 클라이언트 라이브러리에 대한 샘플은 다음을 참조하세요.
- [.NET용 Azure Service Bus 클라이언트 라이브러리 샘플(레거시)](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/) - **메시지 찾아보기(피킹)** 샘플
- [Java용 Azure Service Bus 클라이언트 라이브러리 샘플(레거시)](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus) - **메시지 찾아보기** 샘플 

