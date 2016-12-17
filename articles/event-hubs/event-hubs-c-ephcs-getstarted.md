---
title: "C 및 C#에서 Event Hubs 시작 | Microsoft Docs"
description: "이 자습서에 따라 Azure 이벤트 허브를 사용하여 C로 이벤트를 보내고 EventProcessorHost를 통해 C#으로 이벤트를 받을 수 있습니다."
services: event-hubs
documentationcenter: 
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 5170c138-39ec-4eea-9925-e6902e5c425a
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: c
ms.devlang: csharp
ms.topic: article
ms.date: 08/16/2016
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: 63cf1a5476a205da2f804fb2f408f4d35860835f
ms.openlocfilehash: 30ef95eebb2d260198d56af1832f897a0e8e8001


---
# <a name="get-started-with-event-hubs"></a>이벤트 허브 시작
[!INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>소개
이벤트 허브는 연결된 장치와 응용 프로그램에서 생성되는 엄청난 양의 데이터를 처리 및 분석할 수 있도록 초당 수백만 개의 이벤트를 수용할 수 있는 확장성이 뛰어난 수집 시스템입니다. 이벤트 허브로 수집된 데이터는 실시간 분석 공급자나 저장소 클러스터를 사용하여 변환하고 저장할 수 있습니다.

자세한 내용은 [Event Hubs 개요][Event Hubs overview]를 참조하세요.

이 자습서에서는 C의 콘솔 응용 프로그램을 사용하여 메시지를 Event Hub로 수집하고 C# [이벤트 프로세서 호스트][이벤트 프로세서 호스트] 라이브러리를 사용하여 메시지를 병렬로 검색하는 방법을 알아봅니다.

이 자습서를 완료하려면 다음이 필요합니다.

* C 개발 환경. 이 자습서에서는 Ubuntu 14.04를 사용하는 [Azure Linux VM](../virtual-machines/virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 에 gcc 스택이 있다고 가정합니다. 다른 환경에 대한 지침은 외부 링크에서 제공됩니다.
* Microsoft Visual Studio Express for Windows
* 활성 Azure 계정. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 체험](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

[!INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[!INCLUDE [service-bus-event-hubs-get-started-send-c](../../includes/service-bus-event-hubs-get-started-send-c.md)]

[!INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## <a name="run-the-applications"></a>응용 프로그램 실행
이제 응용 프로그램을 실행할 준비가 되었습니다.

1. Visual Studio 내에서 **수신기** 프로젝트를 실행한 후 이 프로젝트에서 모든 파티션에 대한 수신기를 시작할 때까지 기다립니다.
   
   ![][21]
2. **발신자** 프로그램을 실행하고 수신기 창에 이벤트가 표시되는지 확인합니다.
   
   ![][24]

## <a name="next-steps"></a>다음 단계
이제 이벤트 허브를 만들고 데이터를 보내고 받는 작업 중인 응용 프로그램을 구축했으므로 다음 시나리오로 진행할 수 있습니다.

* 전체 [Event Hubs를 사용하는 샘플 응용 프로그램][이벤트 허브를 사용하는 샘플 응용 프로그램].
* [Event Hubs를 사용하는 이벤트 처리 확장][이벤트 허브를 사용하는 이벤트 처리 확장] 샘플.
* [Event Hubs 개요][이벤트 허브 개요]

<!-- Images. -->
[21]: ./media/event-hubs-c-ephcs-getstarted/run-csharp-ephcs1.png
[24]: ./media/event-hubs-c-ephcs-getstarted/receive-eph-c.png

<!-- Links -->
[Azure 클래식 포털]: https://manage.windowsazure.com/
[이벤트 프로세서 호스트]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[이벤트 허브 개요]: event-hubs-overview.md
[이벤트 허브를 사용하는 샘플 응용 프로그램]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[이벤트 허브를 사용하는 이벤트 처리 확장]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3



<!--HONumber=Nov16_HO3-->


