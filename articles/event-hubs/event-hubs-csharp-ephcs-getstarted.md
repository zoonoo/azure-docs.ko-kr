---
title: "C#로 작성된 Event Hubs 시작 | Microsoft Docs"
description: "이 자습서에 따라 EventProcessorHost를 통해 C#과 함께 Azure Event Hubs를 사용할 수 있습니다."
services: event-hubs
documentationcenter: 
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 2ec2378a-34f7-44c3-b976-cc444c98c338
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 12/07/2016
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: 015dbab81f807cd05d1fedb97f5fc62a012f2084
ms.openlocfilehash: 54aaf68864fc83501b860ecc3daef1c0216c0140


---
# <a name="get-started-with-event-hubs"></a>이벤트 허브 시작
[!INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>소개
이벤트 허브는 연결된 장치 및 응용 프로그램에서 많은 양의 이벤트 데이터(원격 분석)를 처리하는 서비스입니다. 이벤트 허브에 데이터를 수집한 후 저장소 클러스터를 사용하여 데이터를 저장하거나 실시간 분석 공급자를 사용하여 변환할 수 있습니다. 이 대규모 이벤트 수집 및 처리 기능은 IoT(사물 인터넷)를 포함하여 최신 응용 프로그램 아키텍처의 핵심 구성 요소입니다.

이 자습서에서는 Azure 포털을 사용하여 이벤트 허브를 만드는 방법을 보여 줍니다. 또한 C#으로 작성한 콘솔 응용 프로그램을 사용하여 이벤트 허브에 메시지를 수집하는 방법 및 C# [이벤트 프로세서 호스트][Event Processor Host] 라이브러리를 사용하여 메시지를 병렬로 검색하는 방법을 알아봅니다.

이 자습서를 완료하려면 다음이 필요합니다.

* [Microsoft Visual Studio](http://visualstudio.com)
* 활성 Azure 계정. 계정이 없는 경우 몇 분 만에 무료 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 체험](https://azure.microsoft.com/free/)을 참조하세요.

[!INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[!INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]

[!INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## <a name="run-the-applications"></a>응용 프로그램 실행
이제 응용 프로그램을 실행할 준비가 되었습니다.

1. Visual Studio 내에서 앞서 만든 **수신기** 프로젝트를 엽니다.
2. **수신기** 솔루션을 마우스 오른쪽 단추로 클릭한 다음 **추가**를 클릭한 후 **기존 프로젝트**를 클릭합니다.
3. 기존 Sender.csproj 파일을 찾은 다음 두 번 클릭하여 솔루션에 추가합니다.
4. 다시 **수신기** 솔루션을 마우스 오른쪽 단추로 클릭한 다음 **속성**을 클릭합니다. **수신기** 속성 페이지가 표시됩니다.
5. **시작 프로젝트**를 클릭한 다음 **여러 시작 프로젝트** 단추를 클릭합니다. **수신기** 및 **보낸 사람** 프로젝트 모두에 대한 **작업** 상자를 설정하여 **시작**합니다.
   
    ![][19]
6. **프로젝트 종속성**을 클릭합니다. **프로젝트** 상자에서 **보낸 사람**을 클릭합니다. **종속 대상** 상자에서 **수신기**를 확인해야 합니다.
   
    ![][20]
7. **확인**을 클릭하여 **속성** 대화 상자를 해제합니다.
8. F5를 눌러서 Visual Studio 내에서 **수신기** 프로젝트를 실행한 다음 이 프로젝트에서 모든 파티션에 대한 수신기를 시작할 때까지 기다립니다.
   
   ![][21]
9. **보낸 사람** 프로젝트는 자동으로 실행됩니다. 콘솔 창에서 **Enter** 키를 누르고 수신기 창에서 이벤트가 표시되는지 확인합니다.
   
   ![][22]

**보낸 사람** 창에서 **Ctrl+C** 키를 눌러 발신자 응용 프로그램을 종료한 다음 수신기 창에서 **Enter** 키를 눌러 해당 응용 프로그램을 종료합니다.

## <a name="next-steps"></a>다음 단계
이제 이벤트 허브를 만들고 데이터를 보내고 받는 작업 중인 응용 프로그램을 구축했으므로 다음 시나리오로 진행할 수 있습니다.

* [Event Hubs를 사용하는 응용 프로그램 예제][sample application that uses Event Hubs] 전체.
* [이벤트 프로세서 호스트](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost)
* [Event Hubs를 사용하는 이벤트 처리 확장][Scale out Event Processing with Event Hubs] 샘플
* [Event Hubs 개요][Event Hubs overview]

<!-- Images. -->
[19]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj1.png
[20]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj2.png
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Event Hubs overview]: event-hubs-overview.md
[sample application that uses Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Scale out Event Processing with Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[queued messaging solution]: ../service-bus-messaging/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md




<!--HONumber=Dec16_HO2-->


