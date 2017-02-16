---
title: "C로 작성된 Event Hubs 시작 | Microsoft Docs"
description: "이 자습서에 따라 Azure 이벤트 허브를 사용하여 C로 이벤트를 보내고 EventProcessorHost를 통해 Java로 이벤트를 받을 수 있습니다."
services: event-hubs
documentationcenter: 
author: jtaubensee
manager: timlt
editor: 
ms.assetid: aa6553f9-e12e-4568-9bf3-667f1c47a6cf
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: c
ms.devlang: csharp
ms.topic: article
ms.date: 01/04/2017
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: 964adaa1b4d1247cf26bd1b8a345d785a114e3ab
ms.openlocfilehash: ec1ed9ccf9510587a76a4860f910ac2772b35f9a


---
# <a name="get-started-with-event-hubs"></a>이벤트 허브 시작
[!INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>소개
이벤트 허브는 연결된 장치와 응용 프로그램에서 생성되는 엄청난 양의 데이터를 처리 및 분석할 수 있도록 초당 수백만 개의 이벤트를 수용할 수 있는 확장성이 뛰어난 수집 시스템입니다. 이벤트 허브로 수집된 데이터는 실시간 분석 공급자나 저장소 클러스터를 사용하여 변환하고 저장할 수 있습니다.

자세한 내용은 [이벤트 허브 개요][Event Hubs overview]를 참조하세요.

이 자습서에서는 C의 콘솔 응용 프로그램을 사용하여 메시지를 이벤트 허브로 수집하고 C# [이벤트 프로세서 호스트][Event Processor Host] 라이브러리를 사용하여 메시지를 병렬로 검색하는 방법을 알아봅니다.

이 자습서를 완료하려면 다음이 필요합니다.

* C 개발 환경. 이 자습서에서는 Ubuntu 14.04를 사용하는 [Azure Linux VM](../virtual-machines/virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 에 gcc 스택이 있다고 가정합니다. 다른 환경에 대한 지침은 외부 링크에서 제공됩니다.
* Microsoft Visual Studio Express for Windows
* 활성 Azure 계정. <br/>계정이 없는 경우 몇 분 만에 무료 계정을 만들 수 있습니다. 자세한 내용은 <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure 무료 평가판</a>을 참조하세요.

[!INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[!INCLUDE [service-bus-event-hubs-get-started-send-c](../../includes/service-bus-event-hubs-get-started-send-c.md)]

[!INCLUDE [service-bus-event-hubs-get-started-receive-ephjava](../../includes/service-bus-event-hubs-get-started-receive-ephjava.md)]

## <a name="run-the-applications"></a>응용 프로그램 실행
이제 응용 프로그램을 실행할 준비가 되었습니다.

1. **수신자** 프로젝트를 실행합니다.
   
   ![][21]
2. **발신자** 프로그램을 실행하고 수신자 창에 이벤트가 표시되는지 확인합니다.
   
   ![][24]

## <a name="next-steps"></a>다음 단계
이제 이벤트 허브를 만들고 데이터를 보내고 받는 작업 중인 응용 프로그램을 구축했으므로 다음 시나리오로 진행할 수 있습니다.

* [Event Hubs를 사용하는 응용 프로그램 예제][sample application that uses Event Hubs] 전체.
* [Event Hubs를 사용하는 이벤트 처리 확장][Scale out Event Processing with Event Hubs] 샘플
* [Event Hubs 개요][Event Hubs overview]

<!-- Images. -->
[21]: ./media/event-hubs-c-ephjava-getstarted/ephjava.png
[24]: ./media/event-hubs-c-ephjava-getstarted/receive-eph-c.png

<!-- Links -->
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Event Hubs overview]: event-hubs-overview.md
[sample application that uses Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Scale out Event Processing with Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3



<!--HONumber=Jan17_HO1-->


