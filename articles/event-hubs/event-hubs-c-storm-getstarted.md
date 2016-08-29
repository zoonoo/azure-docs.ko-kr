<properties
	pageTitle="C 및 Apache Storm과 함께 이벤트 허브 시작 | Microsoft Azure"
	description="이 자습서에 따라 Azure 이벤트 허브를 사용하여 C로 이벤트를 보내고 Apache Storm 클러스터에서 이벤트를 받을 수 있습니다."
	services="event-hubs"
	documentationCenter=""
	authors="fsautomata"
	manager="timlt"
	editor=""/>

<tags
	ms.service="event-hubs"
	ms.workload="na"
	ms.tgt_pltfrm="c"
	ms.devlang="java"
	ms.topic="article"
	ms.date="08/16/2016"
	ms.author="sethm"/>

# 이벤트 허브 시작

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## 소개

이벤트 허브는 연결된 장치와 응용 프로그램에서 생성되는 엄청난 양의 데이터를 처리 및 분석할 수 있도록 초당 수백만 개의 이벤트를 수용할 수 있는 확장성이 뛰어난 수집 시스템입니다. 이벤트 허브로 수집된 데이터는 실시간 분석 공급자나 저장소 클러스터를 사용하여 변환하고 저장할 수 있습니다.

자세한 내용은 [이벤트 허브 개요]를 참조하세요.

이 자습서에서는 C의 콘솔 응용 프로그램을 사용하여 이벤트 허브로 메시지를 수집하고 Apache Storm을 사용하여 메시지를 병렬로 검색하는 방법을 알아봅니다.

이 자습서를 완료하려면 다음이 필요합니다.

+ C 개발 환경. 이 자습서에서는 Ubuntu 14.04를 사용하는 [Azure Linux VM](../virtual-machines/virtual-machines-linux-quick-create-cli.md)에 gcc 스택이 있다고 가정합니다. 다른 환경에 대한 지침은 외부 링크에서 제공됩니다.

+ [Maven](http://maven.apache.org/)을 실행하도록 구성된 Java 개발 환경. 이 자습서에서는 [Eclipse](https://www.eclipse.org/)를 사용한다고 가정합니다.

+ 활성 Azure 계정. 계정이 없는 경우 몇 분 만에 무료 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 체험](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-c](../../includes/service-bus-event-hubs-get-started-send-c.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-storm](../../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## 응용 프로그램 실행

이제 응용 프로그램을 실행할 준비가 되었습니다.

1.	Eclipse에서 **LogTopology** 클래스를 실행한 다음 모든 파티션에 대한 수신기가 시작될 때까지 기다립니다.

2.	**발신자** 프로그램을 실행하고 수신기 창에 이벤트가 표시되는지 확인합니다.

	![][23]

> [AZURE.NOTE] 이 자습서에 한해 Storm을 로컬 모드에서 개발용으로 사용합니다. Storm 배포 및 패턴에 대한 자세한 내용은 [HDInsight Storm 개요] 및 공식 [Apache Storm] 설명서를 참조하세요.

## 다음 단계

다음 리소스는 이벤트 허브와 Storm을 통합하는 응용 프로그램을 개발할 때 사용할 수 있습니다.

- [Storm 및 HDInsight를 사용하여 센서 데이터 분석][]은 이벤트 허브, Storm 및 HBase를 사용하여 센서 데이터를 Hadoop 클러스터에 수집하는 방법을 보여 주는 전체 시나리오 자습서입니다.
- [Storm 및 HDInsight에서 SCP.NET 및 C#을 사용하여 스트리밍 데이터 처리 응용 프로그램 개발][]은 C#을 사용하여 Storm 파이프라인을 작성하는 방법을 보여 주는 자습서입니다.

<!-- Images. -->
[23]: ./media/event-hubs-c-storm-getstarted/receive-storm3.png

<!-- Links -->
[Azure 클래식 포털]: https://manage.windowsazure.com/
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[이벤트 허브 개요]: event-hubs-overview.md

[Apache Storm]: https://storm.incubator.apache.org
[HDInsight Storm 개요]: ../hdinsight/hdinsight-storm-overview.md/
[Storm 및 HDInsight를 사용하여 센서 데이터 분석]: ../hdinsight/hdinsight-storm-sensor-data-analysis.md
[Storm 및 HDInsight에서 SCP.NET 및 C#을 사용하여 스트리밍 데이터 처리 응용 프로그램 개발]: ../hdinsight/hdinsight-storm-develop-csharp-visual-studio-topology.md

<!---HONumber=AcomDC_0817_2016-->