<properties 
	pageTitle="HDInsight에서 Apache Spark로 Azure 이벤트 허브를 사용하여 스트리밍 데이터 처리 | Microsoft Azure" 
	description="Azure 이벤트 허브에 데이터를 보내는 방법에 대한 단계별 지침 및 Zeppelin Notebook을 사용하여 Spark에서 해당 이벤트 수신" 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"
	tags="azure-portal"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/08/2015" 
	ms.author="nitinme"/>


# Spark 스트리밍: HDInsight에서 Apache Spark로 Azure 이벤트 허브의 이벤트 처리(Windows)

> [AZURE.NOTE]이제 HDInsight가 Linux에서 Spark 클러스터를 제공합니다. Hdinsight Spark Linux 클러스터에서 스트리밍 응용 프로그램을 실행하는 방법에 대한 자세한 내용은 [Spark 스트리밍: HDInsight에서 Apache Spark로 Azure 이벤트 허브의 이벤트 처리(Linux)](hdinsight-apache-spark-eventhub-streaming.md)를 참조하세요.

Spark 스트리밍는 핵심 Spark API를 확장하여 뛰어난 확장성, 높은 처리량, 내결함성 스트림 처리 응용 프로그램을 빌드합니다. 여러 소스에서 데이터를 수집할 수 있습니다. 이 문서에서는 이벤트 허브를 사용하여 데이터를 수집합니다. 이벤트 허브는 초당 수백만의 이벤트를 유입하는 확장성이 뛰어난 수집 시스템입니다.

이 자습서에서는 Azure 이벤트 허브를 만드는 방법, C#의 콘솔 응용 프로그램을 사용하여 이벤트 허브로 메시지를 수집하고 HDInsight에서 Apache Spark를 구성하는 Zeppelin Notebook을 사용하여 메시지를 병렬로 검색하는 방법을 알아봅니다.

> [AZURE.NOTE]이 문서의 지침을 따르려면 Azure 포털의 두 가지 버전을 모두 사용해야 합니다. 이벤트 허브를 만드는 데는 [Azure 포털](https://manage.windowsazure.com)을 사용합니다. HDInsight Spark 클러스터 작업을 하는 데는 [Azure Preview 포털](https://ms.portal.azure.com/)을 사용합니다.

**필수 조건:**

다음이 있어야 합니다.

- Azure 구독. [Azure 무료 평가판](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.
- Apache Spark 클러스터. 자세한 내용은 [Azure HDInsight에서 Apache Spark 클러스터 만들기](hdinsight-apache-spark-provision-clusters.md)를 참조하세요.
- [Azure 이벤트 허브](service-bus-event-hubs-csharp-ephcs-getstarted.md).
- Microsoft Visual Studio 2013이 설치된 워크스테이션입니다. 자세한 내용은 [Visual Studio 설치](https://msdn.microsoft.com/library/e2h7fzkw.aspx)를 참조하세요.

##<a name="createeventhub"></a>Azure 이벤트 허브 만들기

1. [Azure 포털](https://manage.windowsazure.com)에서 **새로 만들기** > **서비스 버스** > **이벤트 허브** > **사용자 지정 만들기**를 선택합니다.

2. **새 이벤트 허브 추가** 화면에서 **이벤트 허브 이름**을 입력하고 허브를 만들 **하위 지역**을 선택한 다음 새 네임스페이스를 만들거나 기존 네임스페이스를 선택합니다. **화살표**를 클릭하여 계속합니다.

	![마법사 페이지 1](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/hdispark.streaming.create.event.hub.png "Azure 이벤트 허브 만들기")

	> [AZURE.NOTE]대기 시간 및 비용을 줄이려면 HDInsight의 Apache Spark 클러스터와 동일한 **위치**를 선택해야 합니다.

3. **이벤트 허브 구성** 화면에서 **파티션 수** 및 **메시지 보존** 값을 입력한 다음 확인 표시를 클릭합니다. 이 예에서는 파티션 개수로 10을, 메시지 보존으로는 1을 사용합니다. 파티션 개수 값은 나중에 필요하므로 기록해 둡니다.

	![마법사 페이지 2](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/hdispark.streaming.create.event.hub2.png "이벤트 허브에 대한 파티션 크기 및 보존 일 지정")

4. 사용자가 만든 이벤트 허브를 클릭하고 **구성**을 클릭한 다음 이벤트 허브에 대한 두 가지 액세스 정책을 만듭니다.

	<table>
<tr><th>이름</th><th>권한</th></tr>
<tr><td>mysendpolicy</td><td>보내기</td></tr>
<tr><td>myreceivepolicy</td><td>수신 대기</td></tr>
</table>권한을 만든 후 페이지 아래쪽의 **저장** 아이콘을 선택합니다. 그러면 이 이벤트 허브로 보내고(**mysendpolicy**) 수신하는(**myreceivepolicy**) 데 사용되는 공유 액세스 정책이 만들어집니다.

	![정책](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/hdispark.streaming.event.hub.policies.png "이벤트 허브 정책 만들기")

	
5. 같은 페이지에는 두 정책에 대해 생성된 정책 키를 기록합니다. 이러한 키는 나중에 사용되므로 저장합니다.

	![policy keys](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/hdispark.streaming.event.hub.policy.keys.png "정책 키 저장")

6. **대시보드** 페이지의 아래에서 검색할 **연결 정보**를 클릭하고 두 정책을 사용하여 이벤트 허브에 대한 연결 문자열을 저장합니다.

	![policy keys](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/hdispark.streaming.event.hub.policy.connection.strings.png "정책 연결 문자열 저장")

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]

##<a name="receivezeppelin"></a>Zeppelin을 사용하여 HDInsight의 Spark에서 메시지 수신

이 섹션에서는 [Zeppelin](https://zeppelin.incubator.apache.org) Notebook을 만들어 이벤트 허브의 메시지를 HDInsight의 Spark 클러스터로 수신합니다.

### 리소스를 스트리밍 응용 프로그램용 Zeppelin에 할당

Zeppelin을 사용하여 스트리밍 응용 프로그램을 만드는 동안 다음 고려 사항을 확인합니다.

* **이벤트 허브 파티션 및 Zeppelin에 할당된 코어**입니다. 이전 단계에서 일부 파티션이 있는 이벤트 허브를 만들었습니다. 아래에서 만든 Zeppelin 스트리밍 응용 프로그램에서 같은 수의 파티션을 다시 지정합니다. Zeppelin을 사용하여 이벤트 허브에서 데이터를 성공적으로 스트리밍하려면 Zeppelin에 할당할 코어 수는 이벤트 허브에 있는 파티션 수의 두 배여야 합니다.
* **Zeppelin에 할당될 코어의 최소 수**입니다. 아래에서 만든 스트리밍 응용 프로그램에서 응용 프로그램에서 스트리밍되는 메시지를 저장하는 임시 테이블을 만듭니다. 그런 다음 Spark SQL 문을 사용하여 이 임시 테이블에서 메시지를 읽습니다. Spark SQL 문을 성공적으로 실행하려면 Zeppelin에 할당된 최소 두 개의 코어가 있어야 합니다.

위의 두 가직 요구 사항을 결합하면 다음을 얻게 됩니다.

* Zeppelin에 할당해야 하는 코어의 최소 수는 2입니다.
* 할당된 코어 수는 항상 이벤트 허브에 있는 파티션 수의 두 배여야 합니다. 

Spark 클러스터의 리소스를 할당하는 방법에 대한 지침은 [HDInsight에 Apache Spark 클러스터에 대한 리소스 관리](hdinsight-apache-spark-resource-manager-v1.md)를 참조하세요.

### Zeppelin을 사용하여 스트리밍 응용 프로그램 만들기

1. [Azure Preview 포털](https://portal.azure.com/)의 시작 보드에서 Spark 클러스터 타일을 클릭합니다(Spark 클러스터를 시작 보드에 고정한 경우). **모두 찾아보기** > **HDInsight 클러스터**에서 클러스터로 이동할 수도 있습니다.   

2. Spark 클러스터 블레이드에서 **빠른 연결**을 클릭한 다음 **클러스터 대시보드** 블레이드에서 **Zeppelin Notebook**을 클릭합니다. 메시지가 표시되면 클러스터에 대한 관리자 자격 증명을 입력합니다.

	> [AZURE.NOTE]또한 브라우저에서 다음 URL을 열어 클러스터에 대한 Zeppelin Notebook에 도달할 수 있습니다. __CLUSTERNAME__을 클러스터의 이름으로 바꿉니다.
	>
	> `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. 새 노트북을 만듭니다. 헤더 창에서 **노트북**을 클릭하고 드롭다운에서 **새 메모 만들기**를 클릭합니다.

	![새 Zeppelin Notebook 만들기](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/hdispark.createnewnote.png "새 Zeppelin 노트북 만들기")

	같은 페이지의 **노트북** 제목 아래에 **Note XXXXXXXXX**로 시작하는 이름의 새 노트북이 표시됩니다. 새 노트북을 클릭합니다.

3. 새 노트북에 대한 웹 페이지에서 제목을 클릭하고 원하는 경우 노트북의 이름을 변경합니다. Enter 키를 눌러 변경된 이름을 저장합니다. 또한 노트북 헤더의 오른쪽 위 모서리에 **연결됨** 상태가 표시되는지 확인합니다.

	![Zeppelin 노트북 상태](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/hdispark.newnote.connected.png "Zeppelin Notebook 상태")

4. 새 노트북에서 기본적으로 만들어지는 빈 단락에 다음 코드 조각을 붙여넣고 이벤트 허브 구성을 사용하여 자리 표시자를 바꿉니다. 이 조각에서는 이벤트 허브에서 스트림을 제공하고 이 스트림을 **mytemptable**라는 임시 테이블로 등록합니다. 다음 섹션에서 발신자 응용 프로그램을 시작하겠습니다. 그런 다음 테이블에서 직접 데이터를 읽을 수 있습니다.

	> [AZURE.NOTE]아래 코드 조각에서 **eventhubs.checkpoint.dir**은 기본 저장소 컨테이너에 디렉터리를 설정해야 합니다. 디렉터리가 없는 경우 스트리밍 응용 프로그램이 디렉터리를 만듭니다. 전체 경로를 "**wasb://container@storageaccount.blob.core.windows.net/mycheckpointdir/**"와 같은 디렉터리 또는 상대 경로를 "**/mycheckpointdir**"와 같은 디렉터리에 지정할 수 있습니다.

		import org.apache.spark.streaming.{Seconds, StreamingContext}
		import org.apache.spark.streaming.eventhubs.EventHubsUtils
		import sqlContext.implicits._
		
		val ehParams = Map[String, String](
		  "eventhubs.policyname" -> "<name of policy with listen permissions>",
		  "eventhubs.policykey" -> "<key of policy with listen permissions>",
		  "eventhubs.namespace" -> "<service bus namespace>",
		  "eventhubs.name" -> "<event hub in the service bus namespace>",
		  "eventhubs.partition.count" -> "1",
		  "eventhubs.consumergroup" -> "$default",
		  "eventhubs.checkpoint.dir" -> "/<check point directory in your storage account>",
		  "eventhubs.checkpoint.interval" -> "10"
		)
		
		val ssc =  new StreamingContext(sc, Seconds(10))
		val stream = EventHubsUtils.createUnionStream(ssc, ehParams)
		
		case class Message(msg: String)
		stream.map(msg=>Message(new String(msg))).foreachRDD(rdd=>rdd.toDF().registerTempTable("mytemptable"))

		stream.print
		ssc.start


##<a name="runapps"></a>응용 프로그램 실행

1. Zeppelin Notebook에서 조각을 사용하여 단락을 실행합니다. **SHIFT + ENTER** 또는 오른쪽 위 모서리에 있는 **재생** 단추를 누릅니다.

	단락의 오른쪽 모서리 상태가 준비, 보류 중, 실행 중, 완료 순서로 진행됩니다. 출력은 같은 단락 하단에 표시됩니다. 스크린샷은 다음과 같습니다.

	![조각의 출력](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/hdispark.streaming.event.hub.zeppelin.code.output.png "snipet의 출력")

2. **보낸 사람** 프로젝트를 실행하고 콘솔 창에서 **Enter** 키를 눌러 이벤트 허브로 메시지를 보내기 시작합니다.

3. Zeppelin Notebook의 새 단락에서 다음 코드 조각을 입력하여 Spark에서 받은 메시지를 읽습니다.

		%sql 
		select * from mytemptable limit 10

	다음 화면 캡처는 **mytemptable**에서 받은 메시지를 보여 줍니다.

	![Zeppelin에 메시지 수신](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/hdispark.streaming.event.hub.zeppelin.output.png "Zeppelin Notebook에 메시지 수신")

4. 응용 프로그램을 종료하려면 Spark SQL 인터프리터를 다시 시작합니다. 맨 위쪽에서 **인터프리터** 탭을 클릭하고 Spark 인터프리터에 대해 **다시 시작**을 클릭합니다.

	![Zeppelin 인터프리터 다시 시작](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/hdispark.zeppelin.restart.interpreter.png "Zeppelin 인터프리터 다시 시작")

##<a name="sparkstreamingha"></a>고가용성을 요구하는 스트리밍 응용 프로그램 실행

HDInsight에서 Spark 클러스터로 스트리밍 데이터를 받으려면 Zeppelin 사용은 응용 프로그램을 프로토타입으로 만드는 적합한 방식입니다. 그러나 높은 가용성과 복원력으로 프로덕션 설정에서 스트리밍 응용 프로그램을 실행 하려면 다음을 수행해야 합니다.

1. 종속성 jar를 클러스터와 연결된 저장소 계정에 복사합니다.
2. 스트리밍 응용 프로그램을 빌드합니다.
3. 클러스터로 RDP를 연결하고 응용 프로그램 jar를 헤드 노드로 복사합니다.
3. 클러스터로 RDP를 연결하고 클러스터 노드에서 실행 응용 프로그램을 실행합니다.

이러한 단계를 수행하는 방법에 대한 지침 및 샘플 스트리밍 응용 프로그램을 GitHub의 [https://github.com/hdinsight/hdinsight-spark-examples](https://github.com/hdinsight/hdinsight-spark-examples)에서 다운로드할 수 있습니다.


##<a name="seealso"></a>참고 항목


* [개요: Azure HDInsight에서 Apache Spark](hdinsight-apache-spark-overview-v1.md)
* [빠른 시작: HDInsight에서 Apache Spark 만들기 및 Spark SQL을 사용하여 대화형 쿼리 실행](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md)
* [HDInsight에서 Spark를 사용하여 기계 학습 응용 프로그램 빌드](hdinsight-apache-spark-ipython-notebook-machine-learning-v1.md)
* [HDInsight에서 BI 도구로 Spark를 사용하여 대화형 데이터 분석 수행](hdinsight-apache-spark-use-bi-tools-v1.md)
* [Azure HDInsight에서 Apache Spark 클러스터에 대한 리소스 관리](hdinsight-apache-spark-resource-manager-v1.md)


[hdinsight-versions]: ../hdinsight-component-versioning/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/

<!---HONumber=AcomDC_1223_2015-->