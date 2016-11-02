<properties
    pageTitle="Apache Storm과 함께 C#에서 이벤트 허브 시작 | Microsoft Azure"
    description="이 자습서에 따라 Azure 이벤트 허브를 사용하여 C#으로 이벤트를 보내고 Apache Storm 클러스터에서 이벤트를 받을 수 있습니다."
    services="event-hubs"
    documentationCenter=""
    authors="jtaubensee"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article" 
    ms.date="09/06/2016"
    ms.author="jotaub;sethm"/>


# <a name="get-started-with-event-hubs"></a>이벤트 허브 시작

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>소개

이벤트 허브는 연결된 장치와 응용 프로그램에서 생성되는 엄청난 양의 데이터를 처리 및 분석할 수 있도록 초당 수백만 개의 이벤트를 수용할 수 있는 확장성이 뛰어난 수집 시스템입니다. 이벤트 허브로 수집된 데이터는 실시간 분석 공급자나 저장소 클러스터를 사용하여 변환하고 저장할 수 있습니다.

자세한 내용은 [이벤트 허브 개요]를 참조하세요.

이 자습서에서는 C#의 콘솔 응용 프로그램을 사용하여 이벤트 허브로 메시지를 수집하고 Apache Storm을 사용하여 메시지를 병렬로 검색하는 방법을 알아봅니다.

이 자습서를 완료하려면 다음이 필요합니다.

+ [Microsoft Visual Studio](http://visualstudio.com)

+ [Maven](http://maven.apache.org/)을 실행하도록 구성된 Java 개발 환경. 이 자습서에서는 [Eclipse](https://www.eclipse.org/)

+ 활성 Azure 계정. <br/>계정이 없는 경우 몇 분 만에 무료 계정을 만들 수 있습니다. 자세한 내용은 <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure 무료 평가판</a>을 참조하세요.

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]


[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-storm](../../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## <a name="run-the-applications"></a>응용 프로그램 실행

이제 응용 프로그램을 실행할 준비가 되었습니다.

1.  Eclipse에서 **LogTopology** 클래스를 실행한 다음 모든 파티션에 대한 수신기가 시작될 때까지 기다립니다.

2.  콘솔 창에서 **발신자** 프로젝트를 실행하고 **Enter** 키를 누른 다음 수신기 창에서 이벤트가 표시되는지 확인합니다.

    ![][22]

## <a name="next-steps"></a>다음 단계

이제 이벤트 허브를 만들고 데이터를 보내고 받는 작업 중인 응용 프로그램을 구축했으므로 다음 시나리오로 진행할 수 있습니다.

- [이벤트 허브를 사용하는 샘플 응용 프로그램][]전체.
- [이벤트 허브를 사용하는 이벤트 처리 확장][] 샘플

<!-- Images. -->
[22]: ./media/event-hubs-csharp-storm-getstarted/receive-storm1.png

<!-- Links -->
[Azure 클래식 포털]: https://manage.windowsazure.com/
[이벤트 허브 개요]: event-hubs-overview.md
[이벤트 허브를 사용하는 샘플 응용 프로그램]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[이벤트 허브를 사용하는 이벤트 처리 확장]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
 


<!--HONumber=Oct16_HO2-->


