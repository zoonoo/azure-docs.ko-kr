<properties 
	pageTitle="Microsoft Azure HDInsight(Hadoop)에서 Apache Storm 사용 시작" 
	description="Apache Storm을 사용하여  HDInsight(Hadoop)에서 데이터를 실시간으로 처리하는 방법에 대해 알아보기" 
	services="hdinsight" 
	documentationCenter="" 
	authors="blackmist" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/19/2014" 
	ms.author="larryfr"/>

# HDInsight(Hadoop)에서 Storm 사용 시작

Apache Storm은 데이터 스트림 처리용 확장 가능한 분산형 실시간 계산 시스템입니다. Azure HDInsight에서는 Storm을 사용하여 실시간 데이터 분석을 수행하는 클라우드 기반 Hadoop 클러스터를 만들 수 있습니다.

## 이 자습서의 내용

* [HDInsight에서 Storm 클러스터를 프로비전하는 방법](#provision)

* [클러스터에 연결하는 방법](#connect)

* [Storm 토폴로지를 실행하는 방법](#run)

* [Storm 토폴로지를 모니터링하는 방법](#monitor)

* [Storm 토폴로지를 중지하는 방법](#stop)

* [다음 단계](#next)

## 시작하기 전에

이 자습서를 성공적으로 완료하려면 다음 항목이 필요합니다.

* Azure 구독

* Microsoft Azure PowerShell

* Apache Storm을 사용해 본 적이 없으면 먼저 [HDInsight Storm 개요](hdinsight-storm-overview.md) 문서를 확인해야 합니다.

## <a id="provision"></a>Azure 포털에서 Storm 클러스터 프로비전

[AZURE.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]

1. [Azure 관리 포털][azureportal]에 로그인합니다.

2. 왼쪽에서 **HDInsight**를 클릭하고 페이지 왼쪽 아래에서 **+새로 만들기**를 클릭합니다.

3. 두 번째 열에서 HDInsight 아이콘을 클릭하고 **사용자 지정**을 선택합니다.

4. **클러스터 세부 정보** 페이지에서 새 클러스터의 이름을 입력하고 **클러스터 유형**으로 **Storm**을 선택합니다. 화살표를 클릭하여 계속합니다.

	![cluster details](./media/hdinsight-storm-getting-started/wizard1.png)

5. 이 클러스터에 사용할 **데이터 노드**의 수를 입력하고 이 클러스터를 만들 **지역/가상 네트워크**를 입력한 후에 화살표를 클릭하여 계속합니다.

	> [AZURE.NOTE] 이 문서에서 사용하는 클러스터의 비용을 최소화하려면 **클러스터 크기**를 1로 줄이고 클러스터를 다 사용한 후에는 삭제합니다.

	![data nodes and region](./media/hdinsight-storm-getting-started/wizard2.png)

6. 관리자의 **사용자 이름** 및 **암호**를 입력하고 화살표를 클릭하여 계속합니다.

	![account and password](./media/hdinsight-storm-getting-started/wizard3.png)

7. **저장소 계정**에서는 **새 저장소 만들기** 또는 기존 저장소 계정을 선택합니다. 그런 다음 사용할 **계정 이름** 및 **기본 컨테이너**를 선택하거나 입력합니다.

	![storage account](./media/hdinsight-storm-getting-started/wizard4.png)

8. **스크립트 작업** 페이지에서 **스크립트 작업 추가**를 클릭하여 클러스터가 생성될 때 클러스터를 사용자 지정하기 위해 실행할 사용자 지정 스크립트에 대한 세부 정보를 제공합니다. 예를 들어 스크립트 작업을 사용하여 <a href="http://spark.apache.org/docs/latest/index.html" target="_blank">Apache Spark</a>를 설치할 클러스터를 사용자 지정할 수 있습니다. 자세한 내용은 [스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정(영문)](hdinsight-hadoop-customize-cluster.md)을 참조하세요. 
	
	![Configure Script Action to customize an HDInsight HBase cluster](./media/hdinsight-storm-getting-started/wizard5.png "Use Script Action to customize an HDInsight cluster") 

	<table border='1'>
		<tr><th>속성</th><th>값</th></tr>
		<tr><td>이름</td>
			<td>스크립트 작업의 이름을 지정합니다.</td></tr>
		<tr><td>스크립트 URI</td>
			<td>클러스터를 사용자 지정하기 위해 호출되는 스크립트에 URI를 지정합니다.</td></tr>
		<tr><td>노드 유형</td>
			<td>사용자 지정 스크립트가 실행되는 노드를 지정합니다. <b>모든 노드</b>, <b>Head nodes only</b> 또는 <b>Worker nodes only</b>를 선택할 수 있습니다.
		<tr><td>매개 변수</td>
			<td>스크립트에 필요한 경우 매개 변수를 지정합니다.</td></tr>
	</table>

	두 개 이상의 스크립트 작업을 추가하여 클러스터에 여러 구성 요소를 설치할 수 있습니다. 스크립트를 추가한 후 확인 표시를 클릭하여 클러스터 프로비저닝을 시작합니다.

## HDInsight에서 Storm 사용

HDInsight의 Storm 미리 보기 릴리스에서는 Storm을 사용하려면 원격 데스크톱을 사용하여 클러스터에 연결해야 합니다. HDInsight 클러스터에 연결하고 Storm 명령을 사용하려면 다음 단계를 수행합니다.

###<a id="connect"></a>클러스터에 연결

1. [Azure 관리 포털][management]을 사용하여 HDInsight에 대한 원격 데스크톱 연결을 사용하도록 설정합니다. 포털에서 HDInsight 클러스터를 선택한 다음 __구성__ 페이지의 하단에서 __원격 사용__을 선택합니다.

    ![enable remote](./media/hdinsight-storm-getting-started/enableremotedesktop.png)

    메시지가 표시되면 원격 세션에 사용할 사용자 이름 및 암호를 입력합니다. 원격 액세스 만료 날짜도 선택해야 합니다.

	![remote desktop config](./media/hdinsight-storm-getting-started/configremotedesktop.png)

2. 원격 액세스가 사용되면 __연결__을 선택하여 연결을 시작합니다. 그러면 원격 데스크톱 세션을 시작하는 데 사용할 수 있는 __.rdp__ 파일이 다운로드됩니다.

    ![connect](./media/hdinsight-storm-getting-started/connect.png)

	> [AZURE.NOTE] 원격 컴퓨터에 연결하는 동안 인증서를 신뢰하는지 묻는 여러 메시지가 표시될 수 있습니다.

3. 연결한 후 바탕 화면의 __Hadoop 명령줄__ 아이콘을 사용하여 Hadoop 명령줄을 엽니다.

	![hadoop cli](./media/hdinsight-storm-getting-started/hadoopcommandline.png)

4. Hadoop 명령줄에서 다음 명령을 사용하여 디렉터리를 Storm 명령이 포함된 디렉터리로 변경합니다.

	cd %storm_home%\bin

5. 사용 가능한 명령 목록을 보려면 매개 변수 없이 `storm`을 입력합니다.

HDInsight 클러스터는 몇 가지 예제 Storm 토폴로지와 함께 제공됩니다. 샘플 **WordcountTopology**는 다음 단계에서 사용됩니다. HDInsight의 Storm과 함께 제공되는 예제에 대한 자세한 내용은 [다음 단계](#next)를 참조하세요.

### <a id="run"></a>Storm 토폴로지를 실행하려면

**WordCountTopology**를 실행하려면 다음 명령을 사용합니다.

	storm jar ..\contrib\storm-starter\storm-starter-<version>-jar-with-dependencies.jar storm.starter.WordCountTopology wordcount

그러면 **storm-starter-&lt;version>-jar-with-dependencies.jar** 파일에 포함된 **storm.starter.WordCountTopology** 클래스에서 **wordcount** 토폴로지를 실행할 것임을 Storm에 지시합니다. 이 파일은 다른 Storm 예제와 함께 %storm_home% 디렉터리의 contrib 폴더에 있습니다.

> [AZURE.NOTE] 예제가 포함된 JAR 파일의 버전은 주기적으로 변경될 수 있습니다. 그러므로 이 명령을 실행할 때는 클러스터와 함께 제공된 파일의 버전을 지정하세요.

명령을 입력해도 아무런 결과도 반환되지 않습니다. **Storm 토폴로지는 일단 시작되면 직접 중지하기 전까지 실행됩니다.** WordCountTopology는 임의의 문장을 생성하며 중지할 때까지 각 단어가 나오는 횟수를 계산합니다.

### <a id="monitor"></a>Storm 토폴로지의 상태를 모니터링하려면

WordCountTopology 샘플은 디렉터리에 출력을 쓰지 않지만 Storm UI 웹 페이지를 사용하여 토폴로지의 상태와 출력을 모두 확인할 수 있습니다.

1. 원격 데스크톱을 사용하여 HDInsight 클러스터에 연결합니다.

2. 바탕 화면에서 **Storm UI** 링크를 엽니다. 그러면 Storm UI 웹 페이지가 표시됩니다.  **토폴로지 요약**에서 **wordcount** 항목을 선택합니다.

	![storm ui](./media/hdinsight-storm-getting-started/stormui.png)

	그러면 토폴로지를 구성하는 구성 요소인 **Spout** 및 **Bolt**를 포함하여 토폴로지의 통계가 표시됩니다.

3. 페이지에서 **spout** 링크를 선택하고, **Executor(모든 시간)** 목록의 항목 중 **내보냄** 및 **전송됨** 열에 0보다 큰 수가 있는 하나에 대한 **포트** 번호를 선택합니다.

	![spouts and bolts](./media/hdinsight-storm-getting-started/stormuiboltsnspouts.png)

	![selecting executors](./media/hdinsight-storm-getting-started/executors.png)

	> [AZURE.NOTE] 클러스터의 노드 수와 실행 중인 토폴로지에 따라서는 토폴로지가 처리를 시작할 때까지 몇 분 정도 걸릴 수 있습니다. **내보냄** 및 **전송됨** 값이 증가하기 시작할 때까지 페이지를 주기적으로 새로 고칩니다.

4. 다음과 같은 정보가 포함된 페이지가 표시됩니다.

		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: spout default [snow white and the seven dwarfs]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:17, stream: default, id: {}, [and]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [and, 16774]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:20, stream: default, id: {}, [and]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [and, 16775]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:17, stream: default, id: {}, [dwarfs]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [dwarfs, 8359]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:20, stream: default, id: {}, [dwarfs]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [dwarfs, 8360]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: spout default [i am at two with nature]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:23, stream: default, id: {}, [two]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [two, 8236]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:22, stream: default, id: {}, [a]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [a, 8280]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:19, stream: default, id: {}, [and]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [and, 16776]

	이 코드 조각에서 Spout가 개별 단어로 분할된  'snow white and the seven dwarfs'를 내보냈음을 확인할 수 있습니다. 또한 토폴로지가 시작된 후 각 단어가 토폴로지에서 처리된 횟수도 계산됩니다. 예를 들어 'dwarfs'라는 단어는 위의 출력을 확인할 때 Spout에 의해 8360번 내보내기되었습니다.

### <a id="stop"></a>Storm 토폴로지를 중지하려면

**WordCountTopology**는 중지할 때까지 계속 실행됩니다. 토폴로지를 중지하려면 다음 명령을 사용합니다.

	storm kill wordcount

이 명령을 실행한 직후에 Storm UI 웹 페이지를 표시하면 **토폴로지 요약**의 **wordcount** 상태가 KILLED로 변경되었음을 확인할 수 있습니다. 그리고 몇 초 후에는 **토폴로지 요약** 섹션에 토폴로지가 더 이상 나열되지 않습니다.

## <a id="next"></a>다음 단계

* **샘플 파일** - Storm 클러스터의 **%storm_home%\contrib** 디렉터리에서는 여러 예제가 제공됩니다. 각 예제는 다음 항목을 포함합니다.

	* 소스 코드 - storm-starter-0.9.1.2.1.5.0-2057-sources.jar 등

	* Java 문서 - storm-starter-0.9.1.2.1.5.0-2057-javadoc.jar 등

	* 예제 - storm-starter-0.9.1.2.1.5.0-2057-jar-with-dependencies.jar 등

	 'jar' 명령을 사용하여 소스 코드 또는 Java 문서를 추출합니다. 예: 'jar -xvf storm-starter-0.9.1.2.1.5.0.2057-javadoc.jar'.

	> [AZURE.NOTE] Java 문서는 웹 페이지로 구성됩니다. 추출 후에는 브라우저를 사용하여 **index.html** 파일을 확인합니다.

* [Storm 및 HDInsight로 센서 데이터 분석](hdinsight-storm-sensor-data-analysis.md)

* [HDInsight에서 Storm에 대해 SCP.NET 및 C#을 사용하여 스트리밍 데이터 처리 응용 프로그램 개발](hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application.md)

[apachestorm]: https://storm.incubator.apache.org
[stormdocs]: http://storm.incubator.apache.org/documentation/Documentation.html
[stormstarter]: https://github.com/apache/storm/tree/master/examples/storm-starter
[stormjavadocs]: https://storm.incubator.apache.org/apidocs/
[azureportal]: https://manage.windowsazure.com/

<!--HONumber=42-->
