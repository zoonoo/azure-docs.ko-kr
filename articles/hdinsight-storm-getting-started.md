<properties
	pageTitle="Apache Storm 자습서: Storm Starter 샘플 체험 | Microsoft Azure"
	description="HDInsight에서 Storm Starter 샘플을 실행하여 Apache Storm을 사용한 빅 데이터 분석을 시작합니다. Apache Storm을 사용하여 실시간으로 데이터를 처리하는 방법을 알아봅니다."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="04/28/2015"
   ms.author="larryfr"/>


# HDInsight의 Apache Storm 자습서: Storm Starter 샘플을 사용하여 빅 데이터 분석 시작 

Apache Storm은 데이터 스트림 처리용 확장 가능한 분산형 실시간 계산 시스템입니다. Azure HDInsight의 Storm을 사용하여 실시간 데이터 분석을 수행하는 클라우드 기반 Storm 클러스터를 만들 수 있습니다.

## 시작하기 전에

이 Apache Storm 자습서를 성공적으로 완료하려면 다음 항목이 필요합니다.

* Azure 구독

## zure 저장소 계정 만들기

HDInsight의 Storm에서는 Azure Blob 저장소를 사용하여 클러스터에 제출된 토폴로지 및 로그 파일을 저장합니다. 다음 단계를 사용하여 클러스터에서 사용할 Azure 저장소 계정을 만들 수 있습니다.

1. [Azure 포털](http://manage.windowsazure.com/)에 로그인합니다.

2. 왼쪽 아래에서 **새로 만들기**를 클릭하고 **데이터 서비스**, **저장소**를 차례로 가리킨 후 **빠른 생성**을 클릭합니다.

	![빠른 생성을 사용하여 새 저장소 계정을 설정할 수 있는 Azure 포털](./media/hdinsight-storm-getting-started/HDI.StorageAccount.QuickCreate.png)

3. **URL**, **위치** 및 **복제** 정보를 입력하고 **저장소 계정 만들기**를 클릭합니다. HDInsight용 저장소를 만들 때는 선호도 그룹을 선택하지 마세요. 저장소 목록에 새 저장소 계정이 표시됩니다.

	>[AZURE.NOTE]이 자습서에서 사용하게 되는 것과 같은 HDInsight 클러스터를 프로비전하기 위한 빠른 생성 옵션은 클러스터를 프로비전할 때 위치를 묻지 않습니다. 대신 기본적으로 저장소 계정이 있는 동일한 데이터 센터에 클러스터를 배치합니다. 따라서 클러스터에 지원되는 위치에서 저장소 계정을 만들어야 합니다. 지원되는 위치는 **동아시아**, **동남 아시아**, **북유럽**, **서유럽**, **미국 동부**, **미국 서부**, **미국 중북부**, **미국 중남부**입니다.

4. 새 저장소 계정의 **상태**가 **온라인**으로 변경될 때까지 기다립니다.

새 저장소 계정 만들기에 대한 자세한 내용은 <a href="../storage-create-storage-account/" target="_blank">저장소 계정을 만드는 방법</a>을 참조하세요.

## Azure 포털에서 Storm 클러스터 프로비전

HDInsight 클러스터를 프로비전할 때는 Apache Storm과 관련 응용 프로그램을 포함하는 Azure 계산 리소스를 프로비전합니다. Azure 포털, HDInsight용 Azure PowerShell cmdlet 또는 HDInsight .NET SDK를 사용하여 다른 버전용 Hadoop 클러스터를 만들 수도 있습니다. 지침에 대해서는 [사용자 지정 옵션을 사용하여 HDInsight 클러스터 프로비전][hdinsight-provision]을 참조하세요. 여러 다른 HDInsight 버전 및 해당 SLA(서비스 수준 계약)에 대한 자세한 내용은 [HDInsight 구성 요소 버전 관리](hdinsight-component-versioning.md) 페이지를 참조하세요.

[AZURE.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]

1. [Azure 포털][azureportal]에 로그인합니다.

2. 왼쪽에서 **HDInsight**를 클릭하고 페이지 왼쪽 아래에서 **+새로 만들기**를 클릭합니다.

3. 두 번째 열에서 HDInsight 아이콘을 클릭하고 **STORM**을 선택합니다.

	![빠른 생성](./media/hdinsight-storm-getting-started/quickcreate.png)

4. 고유한 클러스터 이름을 입력하고 관리 계정의 고유한 암호를 입력합니다. **저장소 계정**에서 이전에 만든 저장소 계정을 선택합니다.

	**클러스터 크기**에서 **1 데이터 노드**를 이 클러스터에 사용하도록 선택합니다. 이 옵션은 클러스터와 관련된 비용을 최소화합니다. 프로덕션용의 경우 더 큰 클러스터를 만듭니다.

	> [AZURE.NOTE]클러스터의 관리자 계정 이름은 **admin**으로 지정됩니다. 입력한 암호는 이 계정의 암호입니다. Storm 토폴로지를 제출하거나 관리하는 등 클러스터에 대한 작업을 수행하려면 이 정보가 필요합니다.

5. 마지막으로 **HDINSIGHT 클러스터 만들기** 옆에 있는 확인 표시를 선택하여 클러스터를 만듭니다.

> [AZURE.NOTE]클러스터 프로비저닝에서 클러스터를 만들고, 소프트웨어를 구성하고, 샘플 데이터 및 토폴로지를 설치하는 데 약간의 시간(일반적으로 15분 미만)이 걸립니다.

## HDInsight에서 Storm Starter 샘플 실행

이 Apache Storm 자습서에서는 GitHub의 Storm Starter 샘플을 사용한 빅 데이터 분석을 소개합니다.

각 HDInsight Storm 클러스터에는 클러스터에서 Storm 토폴로지를 업로드 및 실행하는 데 사용할 수 있는 Storm 대시보드가 제공됩니다. 또한 각 클러스터에는 Storm 대시보드에서 직접 실행할 수 있는 샘플 토폴로지가 제공됩니다.

### <a id="connect"></a>대시보드에 연결

대시보드는 **https://&lt;clustername>.azurehdinsight.net//**(여기서 **clustername**은 클러스터의 이름)에 있습니다. 대시보드 링크는 클러스터에 대한 Azure 포털 페이지의 아래쪽에서 찾을 수 있습니다.

![Storm 대시보드 링크가 있는 Azure 포털](./media/hdinsight-storm-getting-started/dashboard-link.png)

> [AZURE.NOTE]대시보드를 연결할 때 사용자 이름 및 암호를 입력하라는 메시지가 표시됩니다. 이는 관리자 이름(**admin**) 및 클러스터를 만들 때 사용한 암호입니다.

Storm 대시보드가 로드되면 **토폴로지 제출** 양식이 표시됩니다.

![토폴로지 제출 양식이 있는 Storm 대시보드](./media/hdinsight-storm-getting-started/submit.png)

**토폴로지 제출** 양식은 Storm 토폴로지가 포함된 .jar 파일을 업로드 및 실행하는 데 사용됩니다. 여기에는 클러스터와 함께 제공된 몇 가지 기본 샘플도 포함되어 있습니다.

### <a id="run"></a>GitHub의 Storm Starter 프로젝트에서 단어 개수 샘플 실행

클러스터와 함께 제공된 샘플에는 여러 변형의 단어 계산 토폴로지가 포함되어 있습니다. 이러한 샘플에는 문장을 임의로 내보내는 **spout**와 각 문장을 개별 단어로 나눈 다음 각 단어가 발생한 횟수를 계산하는 **bolt**가 있습니다. 이러한 샘플은 Apache Storm의 일부인 <a href="https://github.com/apache/storm/tree/master/examples/storm-starter" target="_blank">Storm Starter 샘플</a>에서 제공됩니다.

Storm Starter 샘플을 실행하려면 다음 단계를 수행합니다.

1. **Jar 파일** 드롭다운에서 **StormStarter - WordCount**를 선택합니다. 그러면 이 샘플의 매개 변수로 **클래스 이름** 및 **추가 매개 변수** 필드가 채워집니다.

	![WordCount가 선택된 Storm 대시보드](./media/hdinsight-storm-getting-started/submit.png)

	* **클래스 이름** - 토폴로지를 제출하는 .jar 파일의 클래스입니다.
	* **추가 매개 변수** - 토폴로지에 필요한 모든 매개 변수입니다. 이 예제에서는 이 필드를 사용하여 제출된 토폴로지의 이름을 제공합니다.

2. **제출** 단추를 클릭합니다. 잠시 후 **결과** 필드에 작업을 제출하는 데 사용된 명령 및 해당 명령의 결과가 표시됩니다. **오류** 필드에는 토폴로지 제출 시 발생한 모든 오류가 표시됩니다.

	![제출 단추 및 결과](./media/hdinsight-storm-getting-started/submit-results.png)

	> [AZURE.NOTE]결과가 토폴로지가 완료되었음을 나타내지 않습니다. **Storm 토폴로지는 시작된 후 사용자가 중지할 때까지 실행됩니다.** 단어 개수 토폴로지는 임의의 문장을 생성하며 중지할 때까지 각 단어가 나오는 횟수를 계산합니다.

### <a id="monitor"></a>토폴로지 모니터링

Storm UI를 사용하여 토폴로지를 모니터링할 수 있습니다.

1. Storm 대시보드의 위쪽에서 **Storm UI**를 선택합니다. 실행 중인 모든 토폴로지 및 클러스터에 대한 요약 정보가 표시됩니다.

	![토폴로지 요약을 보여 주는 Storm UI](./media/hdinsight-storm-getting-started/stormui.png)

	위 페이지에서는 토폴로지가 활성화된 시간과 작업자, 실행자 및 사용 중인 작업 수를 볼 수 있습니다.

	> [AZURE.NOTE]**이름** 열에는 앞에서 **추가 매개 변수** 필드를 통해 제공한 이름이 포함되어 있습니다.

4. **토폴로지 요약** 아래의 **이름** 열에서 **wordcount** 항목을 선택합니다. 그러면 토폴로지에 대한 자세한 정보가 표시됩니다.

	![Storm UI](./media/hdinsight-storm-getting-started/topology-summary.png)

	이 페이지에서는 다음 정보를 제공합니다.

	* **토폴로지 통계** - 기간으로 구성된 토폴로지 성능에 대한 기본 정보입니다.

		> [AZURE.NOTE]특정 기간을 선택하면 페이지의 다른 섹션에 표시되는 정보에 대한 기간이 변경됩니다.

	* **Spout** - 각 Spout에서 반환된 마지막 오류를 포함하여 Spout에 대한 기본 정보입니다.

	* **Bolt** - Bolt에 대한 기본 정보입니다.

	* **토폴로지 구성** - 토폴로지 구성에 대한 자세한 정보입니다.

	이 페이지에서는 토폴로지에 대해 수행할 수 있는 작업도 제공합니다.

	* **활성화** - 비활성화된 토폴로지 처리를 다시 시작합니다.

	* **비활성화** - 실행 중인 토폴로지를 일시 중지합니다.

	* **균형 다시 맞추기** - 토폴로지의 병렬 처리를 조정합니다. 클러스터에서 노드 수를 변경한 후 실행 중인 토폴로지의 균형을 다시 맞추어야 합니다. 이렇게 하면 토폴로지가 병렬 처리를 조정하여 클러스터에서 증가/감소한 노드 수를 보충할 수 있습니다. 자세한 내용은 <a href="http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">Storm 토폴로지의 병렬 처리 이해</a>를 참조하세요.

	* **중단** - 지정된 시간 제한 후 Storm 토폴로지를 종료합니다.

5. 이 페이지의 **Spout** 또는 **Bolt** 섹션에서 항목을 선택합니다. 그러면 선택한 구성 요소에 대한 정보가 표시됩니다.

	![Storm UI](./media/hdinsight-storm-getting-started/component-summary.png)

	이 페이지에는 다음 정보가 표시됩니다.

	* **Spout/Bolt 통계** - 기간으로 구성된 구성 요소 성능에 대한 기본 정보입니다.

		> [AZURE.NOTE]특정 기간을 선택하면 페이지의 다른 섹션에 표시되는 정보에 대한 기간이 변경됩니다.

	* **입력 통계**(Bolt에만 해당) - Bolt에서 사용되는 데이터를 생성하는 구성 요소에 대한 정보입니다.

	* **출력 통계** - 이 Bolt에서 내보낸 데이터에 대한 정보입니다.

	* **실행자** - 이 구성 요소의 인스턴스에 대한 정보입니다.

	* **오류** - 이 구성 요소에 의해 생성된 오류입니다.

5. Spout 또는 Bolt에 대한 세부 정보를 볼 때 구성 요소의 특정 인스턴스에 대한 세부 정보를 확인하려면 **실행자** 섹션의 **포트** 열에서 항목을 선택합니다.

		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["with"]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["nature"]
		2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [snow]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [snow, 747293]
		2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [white]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [white, 747293]
		2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [seven]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [seven, 1493957]

	이 데이터에서 **seven**이라는 단어가 1493957번 발생했음을 알 수 있습니다. 이는 이 토폴로지가 시작된 이후에 발생한 횟수입니다.

### 토폴로지 중지

단어 개수 토폴로지에 대한 **토폴로지 요약** 페이지로 돌아가 **토폴로지 동작** 섹션에서 **중단** 단추를 선택합니다. 메시지가 표시되면 토폴로지를 중지하기 전에 대기할 시간(초)으로 10을 입력합니다. 제한 시간이 지난 후에는 대시보드의 **Storm UI** 섹션을 방문한 경우 토폴로지가 더 이상 표시되지 않습니다.

## 요약

이 Apache Storm 자습서에서는 Storm Starter를 사용하여 HDInsight의 Storm 클러스터를 만드는 방법 및 Storm 대시보드를 사용하여 Storm 토폴로지를 배포, 모니터링 및 관리하는 방법에 대해 알아보았습니다.

## <a id="next"></a>다음 단계

* **Visual Studio용 HDInsight 도구** - HDInsight 도구를 사용하면 Visual Studio에서 위에 설명된 Storm 대시보드와 유사한 Storm 토폴로지를 제출, 모니터링 및 관리할 수 있습니다. 또한 HDInsight 도구는 C# Storm 토폴로지를 만드는 기능을 제공하며, 클러스터에서 배포 및 실행할 수 있는 샘플 토폴로지를 포함합니다.

	자세한 내용은 <a href="../hdinsight-hadoop-visual-studio-tools-get-started/" target="_blank">Visual Studio용 HDInsight 도구 사용 시작</a>을 참조하세요.

* **샘플 파일** - HDInsight Storm 클러스터의 **%STORM_HOME%\\contrib** 디렉터리에서는 여러 예제가 제공됩니다. 각 예제는 다음 항목을 포함합니다.

	* 소스 코드 - storm-starter-0.9.1.2.1.5.0-2057-sources.jar 등

	* Java 문서 - storm-starter-0.9.1.2.1.5.0-2057-javadoc.jar 등

	* 예제 - storm-starter-0.9.1.2.1.5.0-2057-jar-with-dependencies.jar 등

	'jar' 명령을 사용하여 소스 코드 또는 Java 문서를 추출합니다. 예를 들어 'jar -xvf storm-starter-0.9.1.2.1.5.0.2057-javadoc.jar' 등을 사용할 수 있습니다.

	> [AZURE.NOTE]Java 문서는 웹 페이지로 구성됩니다. 추출 후에는 브라우저를 사용하여 **index.html** 파일을 확인합니다.

	이러한 샘플에 액세스하려면 HDInsight의 Storm 클러스터에 대한 원격 데스크톱을 사용하도록 설정한 다음 **%STORM_HOME%\\contrib**에서 파일을 복사해야 합니다.

* 다음 문서에는 HDInsight의 Storm에서 사용할 수 있는 다른 예제의 목록이 들어 있습니다.

	* [HDInsight의 Storm에 대한 예제 토폴로지](hdinsight-storm-example-topology.md)

[apachestorm]: https://storm.incubator.apache.org
[stormdocs]: http://storm.incubator.apache.org/documentation/Documentation.html
[stormstarter]: https://github.com/apache/storm/tree/master/examples/storm-starter
[stormjavadocs]: https://storm.incubator.apache.org/apidocs/
[azureportal]: https://manage.windowsazure.com/
[hdinsight-provision]: hdinsight-provision-clusters.md

<!--HONumber=54-->