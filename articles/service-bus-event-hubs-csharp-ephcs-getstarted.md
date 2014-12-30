<properties pageTitle="이벤트 허브 시작" metaKeywords="Azure Service Bus, Event Hub, getting started Event Hubs" description="Follow this tutorial to get started using Azure Event Hubs with C# using EventProcessorHost" metaCanonical="" services="" documentationCenter="" title="Get Started with Event Hubs" authors="elioda" solutions="" manager="timlt" editor="" />

<tags ms.service="service-bus" ms.workload="core" ms.tgt_pltfrm="csharp" ms.devlang="csharp" ms.topic="hero-article" ms.date="10/27/2014" ms.author="elioda" />

# <a name="getting-started"> </a>이벤트 허브 시작

[WACOM.INCLUDE [service-bus-selector-get-started](../includes/service-bus-selector-get-started.md)]

이벤트 허브는 초당 수백만 개의 이벤트를 처리할 수 있는 확장성이 뛰어난 수집 시스템으로, 응용 프로그램에서 연결된 장치와 응용 프로그램이 생성하는 대량의 데이터를 처리하고 분석할 수 있도록 지원합니다. 이벤트 허브로 수집된 데이터는 실시간 분석 공급자나 저장소 클러스터를 사용하여 변환하고 저장할 수 있습니다. 이벤트 허브에 대한 자세한 내용은 [이벤트 허브 개발자 가이드]를 참조하세요. 

자세한 내용은 [이벤트 허브 개요]를 참조하세요.

이 자습서에서는 C#의 콘솔 응용 프로그램을 사용하여 메시지를 이벤트 허브로 수집하고 C# [이벤트 프로세서 호스트](영문) 라이브러리를 사용하여 메시지를 병렬로 검색하는 방법을 알아봅니다.

이 자습서를 완료하려면 다음이 필요합니다.

+ Microsoft Visual Studio Express 2013 for Windows

+ 활성 Azure 계정. <br/>계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 <a href="http://www.windowsazure.com/ko-kr/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fko-kr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure 무료 평가판</a>을 참조하세요.

## 이벤트 허브 만들기

1. [Azure 관리 포털]에 로그온하고 화면 맨 아래에 있는 **새로 만들기**를 클릭합니다.

2. **앱 서비스**, **서비스 버스**, **이벤트 허브**, **빠른 생성**을 차례로 클릭합니다.

   	![][1]

3. 이벤트 허브의 이름을 입력하고 원하는 지역을 선택한 후 **Create a new Event Hub**(새 이벤트 허브 만들기)를 클릭합니다.

   	![][2]

4. 방금 만든 네임스페이스(일반적으로 ***이벤트 허브 이름*-ns**)를 클릭합니다.

   	![][3]

5. 페이지 맨 위에 있는 **이벤트 허브** 탭을 클릭한 후 방금 만든 이벤트 허브를 클릭합니다.

   	![][4]

6. 맨 위에 있는 **구성** 탭을 클릭하고 *보내기* 권한이 있는 **SendRule**이라는 규칙과 *관리, 보내기, 수신 대기* 권한이 있는 **ReceiveRule**라는 규칙을 추가한 후 **저장**을 클릭합니다.

   	![][5]

7. 페이지 맨 위에 있는 **대시보드** 탭을 클릭한 후 **연결 정보**를 클릭합니다. 두 연결 문자열을 기록해 둡니다.

   	![][6]

이제 이벤트 허브가 만들어졌고 이벤트를 보내고 받기 위한 연결 문자열이 있습니다.

[WACOM.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../includes/service-bus-event-hubs-get-started-send-csharp.md)]


[WACOM.INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## 응용 프로그램 실행

이제 응용 프로그램을 실행할 준비가 되었습니다.

1.	Visual Studio에서 **수신기** 프로젝트를 실행한 후 이 프로젝트에서 모든 파티션에 대한 수신기를 시작할 때까지 기다립니다.

   	![][21]

2.	**송신기** 프로젝트를 실행하고 콘솔 창에서 **Enter** 키를 눌러 이벤트가 수신기 창에 표시되는지 확인합니다.

   	![][22]

<!-- Images. -->
[1]: ./media/service-bus-event-hubs-getstarted/create-event-hub1.png
[2]: ./media/service-bus-event-hubs-getstarted/create-event-hub2.png
[3]: ./media/service-bus-event-hubs-getstarted/create-event-hub3.png
[4]: ./media/service-bus-event-hubs-getstarted/create-event-hub4.png
[5]: ./media/service-bus-event-hubs-getstarted/create-event-hub5.png
[6]: ./media/service-bus-event-hubs-getstarted/create-event-hub6.png

[21]: ./media/service-bus-event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/service-bus-event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[Azure 관리 포털]: https://manage.windowsazure.com/
[이벤트 프로세서 호스트](영문): https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[이벤트 허브 개요]: http://msdn.microsoft.com/ko-kr/library/azure/dn836025.aspx

<!--HONumber=35_1-->
