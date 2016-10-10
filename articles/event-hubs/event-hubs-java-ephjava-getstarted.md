<properties
	pageTitle="Java에서 이벤트 허브 시작 | Microsoft Azure"
	description="이 자습서에 따라 Azure 이벤트 허브를 사용하여 Java로 이벤트를 보내고 EventProcessorHost를 통해 이벤트를 받을 수 있습니다."
	services="event-hubs"
	documentationCenter=""
	authors="jtaubensee"
	manager="timlt"
	editor=""/>

<tags
	ms.service="event-hubs"
	ms.workload="core"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/27/2016"
	ms.author="jotaub;sethm"/>

# 이벤트 허브 시작

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## 소개

이벤트 허브는 연결된 장치와 응용 프로그램에서 생성되는 엄청난 양의 데이터를 처리 및 분석할 수 있도록 초당 수백만 개의 이벤트를 수용할 수 있는 확장성이 뛰어난 수집 시스템입니다. 이벤트 허브로 수집된 데이터는 실시간 분석 공급자나 저장소 클러스터를 사용하여 변환하고 저장할 수 있습니다.

자세한 내용은 [이벤트 허브 개요][]를 참조하세요.

이 자습서에서는 Java의 콘솔 응용 프로그램을 사용하여 이벤트 허브로 메시지를 수집하고 Java 이벤트 프로세서 호스트 라이브러리를 사용하여 메시지를 병렬로 검색하는 방법을 보여줍니다.

이 자습서를 완료하려면 다음이 필요합니다.

+ Java 개발 환경. 이 자습서에서는 [Eclipse](https://www.eclipse.org/)를 사용한다고 가정합니다.

+ 활성 Azure 계정. <br/>계정이 없는 경우 몇 분 만에 무료 계정을 만들 수 있습니다. 자세한 내용은 <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fko-KR%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure 무료 평가판</a>을 참조하세요.

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-java](../../includes/service-bus-event-hubs-get-started-send-java.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephjava](../../includes/service-bus-event-hubs-get-started-receive-ephjava.md)]

## 응용 프로그램 실행

이제 응용 프로그램을 실행할 준비가 되었습니다.

1.	**수신자** 프로젝트를 실행합니다.

	![][21]

2.	**발신자** 프로젝트를 실행합니다.

	![][22]

## 다음 단계

이제 이벤트 허브를 만들고 데이터를 보내고 받는 작업 중인 응용 프로그램을 구축했으므로 다음 시나리오로 진행할 수 있습니다.

- [이벤트 허브를 사용하는 샘플 응용 프로그램][] 전체.
- [이벤트 허브를 사용하는 이벤트 처리 확장][] 샘플
- 서비스 버스 큐를 사용하는 [큐 메시징 솔루션][].

자세한 내용은 [Java 개발자 센터](/develop/java/)를 참조하세요.

<!-- Images. -->
[21]: ./media/event-hubs-java-ephjava-getstarted/ephjava.png
[22]: ./media/event-hubs-java-ephjava-getstarted/java-send.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[이벤트 허브 개요]: event-hubs-overview.md
[이벤트 허브를 사용하는 샘플 응용 프로그램]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[이벤트 허브를 사용하는 이벤트 처리 확장]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[큐 메시징 솔루션]: ../service-bus/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
 

<!---HONumber=AcomDC_0928_2016-->