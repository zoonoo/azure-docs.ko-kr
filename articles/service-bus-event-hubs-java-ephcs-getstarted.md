<properties pageTitle="이벤트 허브 시작" metaKeywords="Azure 서비스 버스, 이벤트 허브, 이벤트 허브 시작" description="이 자습서에 따라 Azure 이벤트 허브를 사용하여 Java로 이벤트를 보내고 EventProcessorHost를 통해 C#으로 이벤트를 받을 수 있습니다." metaCanonical="" services="" documentationCenter="" title="Get Started with Event Hubs" authors="elioda" solutions="" manager="timlt" editor="" />

<tags ms.service="service-bus" ms.workload="core" ms.tgt_pltfrm="csharp" ms.devlang="csharp" ms.topic="hero-article" ms.date="10/27/2014" ms.author="elioda" />

# <a name="getting-started"> </a>이벤트 허브 시작

[WACOM.INCLUDE [service-bus-selector-get-started](../includes/service-bus-selector-get-started.md)]

이벤트 허브는 연결된 장치와 응용 프로그램에서 생성되는 엄청난 양의 데이터를 처리 및 분석할 수 있도록 초당 수백만 개의 이벤트를 수용할 수 있는 확장성이 뛰어난 수집 시스템입니다. 이벤트 허브로 수집된 데이터는 실시간 분석 공급자나 저장소 클러스터를 사용하여 변환하고 저장할 수 있습니다.

자세한 내용은 [이벤트 허브 개요](영문)를 참조하세요.

이 자습서에서는 Java의 콘솔 응용 프로그램을 사용하여 이벤트 허브로 메시지를 수집하고 C# [이벤트 프로세서 호스트] 라이브러리를 사용하여 메시지를 병렬로 검색하는 방법을 알아봅니다.

이 자습서를 완료하려면 다음이 필요합니다.

+ Java 개발 환경. 이 자습서에서는 [Eclipse](https://www.eclipse.org/)를 사용한다고 가정합니다.

+ Microsoft Visual Studio Express 2013 for Windows

+ 활성 Azure 계정. <br/>계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 <a href="http://www.windowsazure.com/ko-kr/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fko-kr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure 무료 평가판</a>을 참조하세요.

## 이벤트 허브 만들기

1. [Azure 관리 포털]에 로그온하고 화면 아래쪽에서 **새로 만들기**를 클릭합니다.

2. **앱 서비스**, **서비스 버스**, **이벤트 허브**를 차례로 클릭한 다음 **빠른 생성**을 클릭합니다.

   	![][1]

3. 이벤트 허브의 이름을 입력하고 원하는 지역을 선택한 후 **Create a new Event Hub**를 클릭합니다.

   	![][2]

4. 방금 만든 네임스페이스(일반적으로 ***이벤트 허브 이름*-ns**)를 클릭합니다.

   	![][3]

5. 페이지 맨 위에서 **이벤트 허브** 탭을 클릭한 다음 방금 만든 이벤트 허브를 클릭합니다.

   	![][4]

6. 페이지 맨 위에서 **구성** 탭을 클릭하고 *보내기* 권한이 있는 **SendRule** 이라는 규칙을 추가하고 *관리, 보내기, 수신 대기* 권한이 있는 **ReceiveRule**이라는 다른 규칙을 추가한 다음 **저장**을 클릭합니다.

   	![][5]

7. 같은 페이지에서 **SendRule**에 대해 생성된 키를 기록해 둡니다.

   	![][6b]

8. 페이지 맨 위에서 **대시보드** 탭을 클릭한 다음 **연결 정보**를 클릭합니다. 두 연결 문자열을 기록해 둡니다.

   	![][6]

이제 이벤트 허브가 만들어지고 이벤트를 보내고 받는 데 필요한 연결 문자열이 있습니다.

[WACOM.INCLUDE [service-bus-event-hubs-get-started-send-java](../includes/service-bus-event-hubs-get-started-send-java.md)]


[WACOM.INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## 응용 프로그램 실행

이제 응용 프로그램을 실행할 준비가 되었습니다.

1.	Visual Studio에서 **수신기** 프로젝트를 실행하고 모든 파티션에 대한 수신기를 시작할 때까지 기다립니다.

   	![][21]

2.	콘솔 창에서 **발신자** 프로젝트를 실행하고 콘솔 창에서 **Enter** 키를 누르고 수신기 창에서 이벤트가 표시되는지 확인합니다.

   	![][22]

<!-- Images. -->
[1]: ./media/service-bus-event-hubs-getstarted/create-event-hub1.png
[2]: ./media/service-bus-event-hubs-getstarted/create-event-hub2.png
[3]: ./media/service-bus-event-hubs-getstarted/create-event-hub3.png
[4]: ./media/service-bus-event-hubs-getstarted/create-event-hub4.png
[5]: ./media/service-bus-event-hubs-getstarted/create-event-hub5.png
[6]: ./media/service-bus-event-hubs-getstarted/create-event-hub6.png
[6b]: ./media/service-bus-event-hubs-getstarted/create-event-hub6b.png


[21]: ./media/service-bus-event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/service-bus-event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[Azure 관리 포털]: https://manage.windowsazure.com/
[이벤트 프로세서 호스트]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[이벤트 허브 개요]: http://msdn.microsoft.com/ko-kr/library/azure/dn836025.aspx
