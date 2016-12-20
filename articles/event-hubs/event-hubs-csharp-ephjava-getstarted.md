---
title: "C#로 작성된 Event Hubs 시작 | Microsoft Docs"
description: "이 자습서에 따라 Azure 이벤트 허브를 사용하여 C로 이벤트를 보내고 EventProcessorHost를 통해 Java로 이벤트를 받을 수 있습니다."
services: event-hubs
documentationcenter: 
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 059fb733-a397-400e-8e43-0c7ea5930b8b
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 09/27/2016
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: c9aa7cebab12eafe71476ee45ef578d3201e69e5
ms.openlocfilehash: e7c38aa5a778d5e8a653e753e5794d7c7da31dc1


---
# <a name="get-started-with-event-hubs"></a>이벤트 허브 시작
[!INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>소개
이벤트 허브는 연결된 장치 및 응용 프로그램에서 많은 양의 이벤트 데이터(원격 분석)를 처리하는 서비스입니다. 이벤트 허브에 데이터를 수집한 후 저장소 클러스터를 사용하여 데이터를 저장하거나 실시간 분석 공급자를 사용하여 변환할 수 있습니다. 이 대규모 이벤트 수집 및 처리 기능은 IoT(사물 인터넷)를 포함하여 최신 응용 프로그램 아키텍처의 핵심 구성 요소입니다.

이 자습서에서는 Azure 포털을 사용하여 이벤트 허브를 만드는 방법을 보여 줍니다. 또한 C#으로 작성한 콘솔 응용 프로그램을 사용하여 이벤트 허브에 메시지를 수집하는 방법 및 Java 이벤트 프로세서 호스트 라이브러리를 사용하여 메시지를 병렬로 검색하는 방법을 알아봅니다.

이 자습서를 완료하려면 다음이 필요합니다.

* [Microsoft Visual Studio](http://visualstudio.com)
* 활성 Azure 계정. <br/>계정이 없는 경우 몇 분 만에 무료 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 체험](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F target="_blank")을 참조하세요.

[!INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[!INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]

[!INCLUDE [service-bus-event-hubs-get-started-receive-ephjava](../../includes/service-bus-event-hubs-get-started-receive-ephjava.md)]

## <a name="run-the-applications"></a>응용 프로그램 실행
이제 응용 프로그램을 실행할 준비가 되었습니다.

1. **수신자** 프로젝트를 실행합니다.
   
   ![][21]
2. **발신자** 프로젝트를 실행합니다.
   
   ![][22]

## <a name="next-steps"></a>다음 단계
이제 이벤트 허브를 만들고 데이터를 보내고 받는 작업 중인 응용 프로그램을 구축했으므로 다음 시나리오로 진행할 수 있습니다.

* [Event Hubs를 사용하는 응용 프로그램 예제][sample application that uses Event Hubs] 전체.
* [Event Hubs를 사용하는 이벤트 처리 확장][Scale out Event Processing with Event Hubs] 샘플
* [Event Hubs 개요][Event Hubs overview]

<!-- Images. -->
[21]: ./media/event-hubs-csharp-ephjava-getstarted/ephjava.png
[22]: ./media/event-hubs-csharp-ephjava-getstarted/cs-send.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Event Hubs overview]: event-hubs-overview.md
[sample application that uses Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Scale out Event Processing with Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3



<!--HONumber=Dec16_HO1-->


