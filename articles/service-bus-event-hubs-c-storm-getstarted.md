<properties 
	pageTitle="이벤트 허브 시작" 
	description="이 자습서에 따라 Azure 이벤트 허브를 사용하여 C로 이벤트를 보내고 Apache Storm 클러스터에서 이벤트를 받을 수 있습니다." 
	services="service-bus" 
	documentationCenter="" 
	authors="fsautomata" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="service-bus" 
	ms.workload="core" 
	ms.tgt_pltfrm="c" 
	ms.devlang="java" 
	ms.topic="hero-article" 
	ms.date="02/10/2015" 
	ms.author="sethm"/>

# 이벤트 허브 시작

[AZURE.INCLUDE [service-bus-selector-get-started](../includes/service-bus-selector-get-started.md)]

## 소개

이벤트 허브는 연결된 장치와 응용 프로그램에서 생성되는 엄청난 양의 데이터를 처리 및 분석할 수 있도록 초당 수백만 개의 이벤트를 수용할 수 있는 확장성이 뛰어난 수집 시스템입니다. 이벤트 허브로 수집된 데이터는 실시간 분석 공급자나 저장소 클러스터를 사용하여 변환하고 저장할 수 있습니다.

자세한 내용은 [이벤트 허브 개요]를 참조하세요.

이 자습서에서는 C의 콘솔 응용 프로그램을 사용하여 이벤트 허브로 메시지를 수집하고 Apache Storm을 사용하여 메시지를 병렬로 검색하는 방법을 알아봅니다.

이 자습서를 완료하려면 다음이 필요합니다.

+ C 개발 환경. 이 자습서에서는 Ubuntu 14.04를 사용하는 [Azure Linux VM](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-tutorial/)에 gcc 스택이 있다고 가정합니다. 다른 환경에 대한 지침은 외부 링크에서 제공됩니다.

+ [Maven](http://maven.apache.org/)을 실행하도록 구성된 Java 개발 환경. 이 자습서에서는 [Eclipse](https://www.eclipse.org/)를 사용한다고 가정합니다.

+ 활성 Azure 계정. <br/>계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fko-kr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure 무료 평가판</a>을 참조하세요.

## 이벤트 허브 만들기

1. [Azure 관리 포털]에 로그온하여 화면 아래쪽의 **새로 만들기**를 클릭합니다.

2. **앱 서비스**, **서비스 버스**, **이벤트 허브**, **빨리 만들기**를 차례로 클릭합니다.

   	![][1]

3. 이벤트 허브의 이름을 입력하고 원하는 지역을 선택한 후 **새 이벤트 허브 만들기**를 클릭합니다.

   	![][2]

4. 방금 만든 네임스페이스(일반적으로 ***이벤트 허브 이름*-ns**)를 클릭합니다.

   	![][3]

5. 페이지 위쪽의 **이벤트 허브** 탭을 클릭한 후 방금 만든 이벤트 허브를 클릭합니다.

   	![][4]

6. 페이지 위쪽의 **구성** 탭을 클릭하고 *Send* 권한이 있는 **SendRule** 규칙과 *Listen* 권한이 있는 **ReceiveRule** 규칙을 추가한 후에 **저장**을 클릭합니다.

   	![][5]

7. 같은 페이지에서 **SendRule** 및 **ReceiveRule**에 대해 생성된 키를 기록해 둡니다.

   	![][6c]

이제 이벤트 허브가 만들어졌고 이벤트를 보내고 받기 위한 연결 문자열이 있습니다.

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-c](../includes/service-bus-event-hubs-get-started-send-c.md)]


[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-storm](../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## 응용 프로그램 실행

이제 응용 프로그램을 실행할 준비가 되었습니다.

1.	Eclipse에서 **LogTopology** 클래스를 실행한 다음 모든 파티션에 대한 수신기가 시작될 때까지 기다립니다.

2.	**발신자** 프로그램을 실행하고 수신기 창에 이벤트가 표시되는지 확인합니다.

   	![][23]

> [AZURE.NOTE] 이 자습서에 한해 Storm을 로컬 모드에서 개발용으로 사용합니다. Storm 배포 및 패턴에 대한 자세한 내용은 [HDInsight Storm 개요] 및 공식 [Apache Storm] 설명서를 참조하세요.

## 다음 단계

다음 리소스는 이벤트 허브와 Storm을 통합하는 응용 프로그램을 개발할 때 사용할 수 있습니다.

- [Storm 및 HDInsight를 사용하여 센서 데이터 분석]은 이벤트 허브, Storm 및 HBase를 사용하여 센서 데이터를 Hadoop 클러스터에 수집하는 방법을 보여 주는 전체 시나리오 자습서입니다.
- [HDInsight 및 Storm에서 SCP.NET 및 C#을 사용하여 스트리밍 데이터 처리 응용 프로그램 개발]은 C#을 사용하여 Storm 파이프라인을 작성하는 방법을 보여 주는 자습서입니다.

<!-- Images. -->
[1]: ./media/service-bus-event-hubs-getstarted/create-event-hub1.png
[2]: ./media/service-bus-event-hubs-getstarted/create-event-hub2.png
[3]: ./media/service-bus-event-hubs-getstarted/create-event-hub3.png
[4]: ./media/service-bus-event-hubs-getstarted/create-event-hub4.png
[5]: ./media/service-bus-event-hubs-getstarted/create-event-hub5.png
[6]: ./media/service-bus-event-hubs-getstarted/create-event-hub6.png
[6c]: ./media/service-bus-event-hubs-getstarted/create-event-hub6c.png

[23]: ./media/service-bus-event-hubs-getstarted/receive-storm3.png

<!-- Links -->
[Azure 관리 포털]: https://manage.windowsazure.com/
[이벤트 프로세서 호스트]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[이벤트 허브 개요]: http://msdn.microsoft.com/library/azure/dn836025.aspx

[Apache Storm]: https://storm.incubator.apache.org
[HDInsight Storm 개요]: http://azure.microsoft.com/documentation/articles/hdinsight-storm-overview/
[Storm 및 HDInsight를 사용하여 센서 데이터 분석]: http://azure.microsoft.com/documentation/articles/hdinsight-storm-sensor-data-analysis/
[HDInsight 및 Storm에서 SCP.NET 및 C#을 사용하여 스트리밍 데이터 처리 응용 프로그램 개발]: http://azure.microsoft.com/documentation/articles/hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application/
<!--HONumber=47-->
