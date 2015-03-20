<properties 
   pageTitle="HDInsight에서 Storm 토폴로지 배포 및 관리 | Azure" 
   description="HDInsight에서 Apache Storm에 포함된 Storm 대시보드를 사용하여 Storm 토폴로지를 배포, 모니터링 및 관리하는 방법에 대해 배웁니다." 
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
   ms.date="02/18/2015"
   ms.author="larryfr"/>

#HDInsight에서 Apache Storm 토폴로지 배포 및 관리

Storm 대시보드를 사용하면 사용자의 웹 브라우저를 사용하는 HDInsight 클러스터에 Apache Storm 토폴로지를 쉽게 배포 및 실행할 수 있습니다. 실행 중인 토폴로지를 모니터링 및 관리하기 위해 대시보드를 사용할 수도 있습니다. Visual Studio를 사용하는 경우 Visual Studio용 HDInsight 도구는 Visual Studio에서 유사한 기능을 제공합니다.

Storm 대시보드와 HDInsight 도구의 Storm 기능 둘 다 자신만의 모니터링 및 관리 솔루션을 만들기 위해 사용할 수 있는 Storm REST API를 필요로 합니다.

##필수 조건

* **HDInsight의 Apache Storm** - <a href="../hdinsight-storm-getting-started/" target="_blank">HDInsight에서 Apache Storm 시작</a>에서 클러스터를 만드는 단계 참조

* **Storm 대시보드** - HTML5를 지원하는 최신 웹 브라우저

* **Visual Studio** - Azure SDK 2.5.1 이상 및 Visual Studio용 HDInsight 도구 Visual Studio용 HDInsight 도구를 설치하고 구성하려면 <a href="../hdinsight-hadoop-visual-studio-tools-getting-started/" target="_blank">Visual Studio용 HDInsight 도구 사용 시작</a>을 참조하세요.

	다음과 같은 Visual Studio 버전 중 하나:

	* Visual Studio 2012 <a href="http://www.microsoft.com/download/details.aspx?id=39305" target="_blank">업데이트 4</a>

	* Visual Studio 2013 <a href="http://www.microsoft.com/download/details.aspx?id=44921" target="_blank">업데이트 4</a> 또는 <a href="http://go.microsoft.com/fwlink/?LinkId=517284" target="_blank">Visual Studio 2013 Community</a>

	* <a href="http://visualstudio.com/downloads/visual-studio-2015-ctp-vs" target="_blank">Visual Studio 2015 CTP6</a>

	> [AZURE.NOTE] 현재 Visual Studio용 HDInsight 도구는 HDInsight 클러스터 버전 3.2의 Storm만 지원합니다.

##Storm 대시보드

Storm 대시보드는 Storm 클러스터에서 사용할 수 있습니다. URL은 **https://&lt;clustername>.azurehdinsight.net/**이며, **clustername**은 HDInsight 클러스터에서 Storm의 이름입니다. Azure 포털에서 클러스터 대시보드로부터 **Storm 대시보드** 링크를 사용하여 대시보드에 액세스할 수도 있습니다.

![the portal with storm dashboard highlighted][hdinsight-dashboard]

Storm 대시보드의 위쪽에서 **토폴로지 제출**을 선택합니다. 샘플 토폴로지를 실행하거나 사용자가 만든 토폴로지를 업로드 및 실행하려면 페이지의 지침을 따릅니다.

![the submit topology page][storm-dashboard-submit]

###Storm UI

Storm 대시보드에서 **Storm UI** 링크를 선택합니다. 실행 중인 토폴로지뿐 아니라 클러스터의 정보가 표시됩니다.

![the storm ui][storm-dashboard-ui] 

> [AZURE.NOTE] 일부 버전의 Internet Explorer에서는 처음으로 방문한 후 rm UI가 새로 고쳐지지 않습니다. 예를 들어 제출한 새 토폴로지가 표시되지 않거나 이전에 중단한 토폴로지가 활성 상태로 표시됩니다. Microsoft는 이 문제를 인식하고 솔루션을 찾기 위해 노력하고 있습니다.

####기본 페이지

Storm UI의 기본 페이지는 다음 정보를 제공합니다.

* **클러스터 요약** - Storm 클러스터에 대한 기본 정보

* **토폴로지 요약** - 실행 중인 토폴로지 목록. 특정 토폴로지에 대한 자세한 정보를 보려면 이 섹션의 링크를 이용하세요.

* **감독자 요약** - Storm 감독자에 대한 정보

* **Nimbus 구성** - 클러스터에 대한 Nimbus 구성

####토폴로지 요약

**토폴로지 요약** 섹션에서 링크를 선택하면 토폴로지에 대한 다음 정보를 표시합니다.

* **토폴로지 요약** - 토폴로지에 대한 기본 정보

* **토폴로지 동작** - 토폴로지에 대해 수행할 수 있는 관리 동작

	* **활성화** - 비활성화된 토폴로지 처리 다시 시작
	
	* **비활성화** - 실행 중인 토폴로지 일시 중지
	
	* **균형 다시 맞추기** - 토폴로지의 병렬 처리 조정. 클러스터에서 노드 수를 변경한 후 실행 중인 토폴로지의 균형을 다시 맞추어야 합니다. 이렇게 하면 토폴로지가 병렬 처리를 조정하여 클러스터에서 증가/감소한 노드 수를 보충할 수 있습니다.
	
		자세한 내용은 <a href="http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">Storm 토폴로지의 병렬 처리 이해</a>를 참조하세요.
	
	* **Kill** - 지정된 시간 제한 후 Storm 토폴로지를 종료합니다.

* **토폴로지 통계** - 토폴로지에 대한 통계입니다. **Window** 열에 있는 링크를 사용하여 페이지에서 나머지 항목에 대한 시간 프레임을 설정할 수 있습니다.

* **Spout** - 토폴로지에서 사용한 spout입니다. 이 섹션의 링크를 사용하면 특정 spout에 대한 자세한 내용을 볼 수 있습니다.

* **Bolt** - 토폴로지에서 사용한 bolt입니다. 이 섹션의 링크를 사용하면 특정 bolt에 대한 자세한 내용을 볼 수 있습니다.

* **토폴로지 구성** - 선택한 토폴로지의 구성입니다.

####Spout 및 Bolt 요약

**Spout** 또는 **Bolt** 섹션에서 spout를 선택하면 선택한 항목에 대해 다음 정보가 표시됩니다.

* **구성 요소 요약** - spout 또는 bolt에 대한 기본 정보입니다.

* **Spout/Bolt 통계** - spout 또는 bolt에 대한 통계입니다. **Window** 열에 있는 링크를 사용하여 페이지에서 나머지 항목에 대한 시간 프레임을 설정할 수 있습니다.

* **입력 상태**(bolt만 해당) - bolt가 사용하는 입력 스트림에 대한 정보입니다.

* **출력 상태** - 이 spout 또는 bolt가 내보낸 스트림에 대한 정보입니다.

* **Executor** - spout 또는 bolt의 인스턴스에 대한 정보입니다. 특정 실행자에 대한 **Port** 항목을 선택하면 이 인스턴스에 대해 처리된 진단 정보의 로그를 볼 수 있습니다.

* **Error** - 이 spout 또는bolt에 대한 오류 정보입니다.

##Visual Studio용 HDInsight 도구

HDInsight 도구는 Storm 클러스터에 C# 또는 하이브리드 토폴로지를 제출하는 데 사용할 수 있습니다. 다음 단계는 샘플 응용 프로그램을 사용합니다. HDInsight 도구를 사용하여 자신만의 토폴로지 만들기에 대한 자세한 내용은 [Visual Studio용 HDInsight 도구를 사용하여 C# 토폴로지 개발]을 참조하세요(../hdinsight-storm-develop-csharp-visual-studio-topology/).

다음 단계를 통해 HDInsight 클러스터에서 Storm에 샘플을 배포한 다음 토폴로지를 보고 관리할 수 있습니다.

1. 최신 버전의 Visual Studio용 HDInsight 도구를 아직 설치하지 않은 경우 <a href="../hdinsight-hadoop-visual-studio-tools-getting-started/" target="_blank">Visual Studio용 HDInsight 도구 사용 시작</a>을 참조하세요.

2. Visual Studio를 열고 **파일**, **새로 만들기**, **프로젝트**를 차례로 선택합니다.

3. **새 프로젝트** 대화 상자에서 **설치됨**, **템플릿**을 확장하고 **HDInsight**를 선택합니다. 템플릿 목록에서 **Storm 샘플**을 선택합니다. 대화 상자 아래쪽에서 응용 프로그램 이름을 입력합니다.

	![image](./media/hdinsight-storm-deploy-monitor/sample.png)

1. **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **HDInsight에서 Storm에 제출**을 선택합니다.

	> [AZURE.NOTE] 메시지가 표시되면 Azure 구독에 대한 로그인 자격 증명을 입력합니다. 하나 이상의 구독이 있는 경우 HDInsight 클러스터의 Storm을 포함하는 자격 증명으로 로그인합니다.

2. **Storm 클러스터** 드롭다운에서 HDInsight 클러스터의 Storm을 선택한 다음 **제출**을 선택합니다. **출력** 창을 통해 제출의 성공 여부를 모니터링할 수 있습니다.

3. 토폴로지 제출에 성공하면 클러스터에 대한 **Storm 토폴로지**가 나타납니다. 실행 중인 토폴로지에 대한 정보를 보려면 목록에서 토폴로지를 선택합니다.

	![visual studio monitor](./media/hdinsight-storm-deploy-monitor/vsmonitor.png)

	> [AZURE.NOTE] **Azure**, **HDInsight**를 확장한 다음 HDInsight 클러스터의 Storm을 마우스 오른쪽 단추로 클릭하고 **Storm 토폴로지 보기**를 선택하여 **서버 탐색기**에서 **Storm 토폴로지**를 볼 수도 있습니다.

	이러한 구성 요소에 대한 정보를 보려면 spout 또는 bolt 링크를 사용합니다. 선택한 각각의 항목에 대해 새 창이 열립니다.

4. 토폴로지를 중단하려면 **토폴로지 요약** 보기에서 **중단**을 선택합니다.

	> [AZURE.NOTE] Storm 토폴로지는 중단되거나 클러스터가 삭제될 때까지 계속 실행됩니다.

##REST API

Storm UI는 REST API의 맨 위에 기본 제공되므로 API를 사용하여 유사한 관리 및 모니터링 기능을 수행할 수 있습니다. REST API를 사용하여 Storm 토폴로지를 관리 및 모니터링하는 사용자 지정 도구를 만들 수 있습니다.

Storm REST API에 대한 설명은 <a href="https://github.com/apache/storm/blob/master/STORM-UI-REST-API.md" target="_base">https://github.com/apache/storm/blob/master/STORM-UI-REST-API.md</a>에 나와 있습니다. 다음 정보는 HDInsight에서 Apache Storm과 REST API 사용하기에 관한 것입니다.

###기본 URI

HDInsight 클러스터에서 REST API의 기본 URI는 **https://&lt;clustername>.azurehdinsight.net/stormui/api/v1/**이며ㅡ **clustername**은 HDInsight 클러스터에서 Storm의 이름입니다.

###인증

REST API 요청은 HDInsight 클러스터 관리자 이름 및 암호를 이용하는 **기본 인증**을 사용해야 합니다. 

> [AZURE.NOTE] 기본 인증은 일반 텍스트로 전송되기 때문에 클러스터와의 안전한 통신을 위해서는 **항상** HTTPS를 사용해야 합니다.

###반환 값

REST API에서 반환되는 정보는 클러스터와 동일한 Azure 가상 네트워크에 있는 클러스터 또는 컴퓨터 내에서만 사용할 수 있습니다. 예를 들어, Zookeeper 서버에 대해 반환된 FQDN(정규화된 도메인 이름)은 인터넷에서 액세스할 수 없습니다.

##다음 단계

Storm 대시보드를 사용하여 토폴로지를 배포 및 모니터링하는 방법에 대해 배웠음으로 [Visual Studio용 HDInsight 도구를 사용하여 C# 토폴로지를 개발]하는 방법(../hdinsight-storm-develop-csharp-visual-studio-topology/)또는 [Maven을 사용하여 Java 기반 토폴로지를 개발]하는 방법에 대해 알아봅니다(../hdinsight-storm-develop-java-topology/).


[hdinsight-dashboard]: ./media/hdinsight-storm-deploy-monitor/dashboard-link.png
[storm-dashboard-submit]: ./media/hdinsight-storm-deploy-monitor/submit.png
[storm-dashboard-ui]: ./media/hdinsight-storm-deploy-monitor/storm-ui-summary.png

<!--HONumber=47-->
