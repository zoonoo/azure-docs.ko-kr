<properties linkid="manage-services-hdinsight-get-started-hdinsight-hadoop" urlDisplayName="Get Started" pageTitle="HDInsight에서 Hive와 함께 Hadoop 사용 시작 | Azure" metaKeywords="" description="클라우드에서 빅 데이터 솔루션인 HDInsight의 Hadoop 사용합니다. 클러스터를 프로비전하고, Hive으로 데이터를 쿼리하며, 분석을 위해 Excel로 출력하는 방법에 대해 알아봅니다." metaCanonical="" services="hdinsight" documentationCenter="" title="Get started using Hadoop with Hive in HDInsight to analyze mobile handset use" authors="nitinme" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/14/2014" ms.author="nitinme" />


# 휴대폰 사용을 분석하기 위해 HDInsight에서 Hive와 함께 Hadoop 사용 시작

<!--div class="dev-center-tutorial-selector sublanding">
<a href="../hdinsight-get-started" title="Get started using Hadoop 2.4 in HDInsight" class="current">Hadoop 2.4</a>
<a href="../hdinsight-get-started-30" title="Get started using Hadoop 2.2 in HDInsight">Hadoop 2.2</a>
<!--a href="../hdinsight-get-started-21" title="Get started using Hadoop 1.2 in HDInsight">Hadoop 1.2</a>
</div-->

HDInsight 사용을 빠르게 시작하기 위해 이 자습서에서는 Hive 쿼리를 사용하여 Hadoop 클러스터의 구조화되지 않은 데이터에서 의미 있는 정보를 추출하는 방법을 보여 줍니다. 그리고 Microsoft Excel에서 결과를 분석합니다.


> [WACOM.NOTE] Hadoop과 빅 데이터를 처음 사용하는 경우 [Apache Hadoop][apache-hadoop], [MapReduce][apache-mapreduce], [HDFS][apache-hdfs] 및 [Hive][apache-hive] 용어에 대해 자세히 알아볼 수 있습니다. HDInsight를 통해 Azure에서 Hadoop을 사용하도록 설정하는 방법을 이해하려면 [HDInsight의 Hadoop 소개][hadoop-hdinsight-intro](영문)를 참조하세요.

Azure HDInsight의 일반적인 가용성과 더불어 Microsoft는 Azure용 HDInsight Emulator(이전의 *Microsoft HDInsight Developer Preview*)도 제공합니다. 이 Emulator는 개발자 시나리오를 대상으로 하기 때문에 단일 노드 배포만 지원합니다. HDInsight Emulator 사용에 대한 자세한 내용은 [HDInsight Emulator 시작][hdinsight-emulator]을 참조하세요.

> [WACOM.NOTE] HBase 클러스터를 프로비전하는 방법에 대한 지침은 [HDInsight에서 HBase 클러스터 프로비전][hdinsight-hbase-custom-provision](영문)을 참조하세요. 어떤 항목을 선택하는 이유를 이해하려면 <a href="http://go.microsoft.com/fwlink/?LinkId=510237">Hadoop과 HBase 의 차이는?</a> 을 참조하세요.   

## 이 자습서의 목적 ##

구조화되지 않은 대량의 데이터 집합이 있고 쿼리를 실행하여 의미 있는 일부 정보를 추출하려고 한다면 이 자습서를 통해 적절한 도움을 얻을 수 있을 것입니다. 해당 방법은 다음과 같습니다.

   !["Get started using Hadoop with Hive in HDInsight" tutorial steps illustrated: create an account; provision a cluster; query data; and analyze in Excel.][image-hdi-getstarted-flow]

이 자습서의 데모 비디오를 시청할 수도 있습니다.

<center><iframe width="560" height="315" src="http://www.youtube.com/embed/v=Y4aNjnoeaHA?list=PLDrz-Fkcb9WWdY-Yp6D4fTC1ll_3lU-QS" frameborder="0" allowfullscreen></iframe></center>

<!--center><a href="https://www.youtube.com/watch?v=Y4aNjnoeaHA&list=PLDrz-Fkcb9WWdY-Yp6D4fTC1ll_3lU-QS" target="_blank">![HDI.getstarted.video][img-hdi-getstarted-video]</a></center-->



**필수 조건:**

이 자습서를 시작하기 전에 다음이 있어야 합니다.


- Azure 구독. 구독을 얻는 방법에 대한 자세한 내용은 [구매 옵션][azure-purchase-options], [구성원 제공 항목][azure-member-offers] 또는 [무료 평가판][azure-free-trial]을 참조하세요.
- Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013 Standalone 또는 Office 2010 Professional Plus가 있는 컴퓨터

**예상 완료 시간:** 30분

##자습서 내용

* [Azure 저장소 계정 만들기](#storage)
* [HDInsight 클러스터 프로비전](#provision)
* [포털에서 샘플 실행](#sample)
* [Hive 작업 실행](#hivequery)
* [다음 단계](#nextsteps)

##<a name="storage"></a>Azure 저장소 계정 만들기

HDInsight는 데이터 저장을 위해 Azure Blob 저장소를 사용합니다. 이를 *WASB* 또는 *Azure 저장소 - Blob*이라고 합니다. WASB는 Azure Blob 저장소에 구현한 Microsoft의 HDFS입니다. 자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용][hdinsight-storage]을 참조하세요.

HDInsight 클러스터를 프로비전할 때 Azure 저장소 계정을 지정합니다. HDFS의 경우처럼 해당 계정의 특정 Blob 저장소 컨테이너는 기본 파일 시스템으로 지정됩니다. HDInsight 클러스터는 기본적으로 사용자가 지정한 저장소 계정과 동일한 데이터 센터에 프로비전됩니다.

사용자 지정 방식으로 HDInsight 클러스터를 구성할 때는 이 저장소 계정 외에 다른 저장소 계정을 추가할 수 있습니다. 이 추가 저장소 계정은 동일한 Azure 구독에서 가져오거나 다른 Azure 구독에서 가져올 수 있습니다. 지침에 대해서는 [사용자 지정 옵션을 사용하여 HDInsight 클러스터 프로비전][hdinsight-provision]을 참조하세요. 

이 자습서와 과정을 간소화하기 위해 기본 Blob 컨테이너와 기본 저장소 계정만 사용됩니다. 실제로는 데이터 파일이 보통 지정된 저장소 계정에 저장됩니다.

**Azure 저장소 계정을 만들려면**

1. [Azure 관리 포털][azure-management-portal]에 로그인합니다.
2. 왼쪽 아래에서 **새로 만들기**를 클릭하고 **데이터 서비스**, **저장소**를 차례로 가리키고 나서 **빠른 생성**을 클릭합니다.

	![Azure portal where you can use Quick Create to set up a new storage account.][image-hdi-storageaccount-quickcreate]

3. **URL**, **위치** 및 **복제**를 입력하고 **저장소 계정 만들기**를 클릭합니다. 선호도 그룹은 지원되지 않습니다. 저장소 목록에 새 저장소 계정이 표시됩니다.

	>[WACOM.NOTE]  이 자습서에서 사용하게 되는 것과 같은 HDInsight 클러스터를 프로비전하기 위한 빠른 생성 옵션은 클러스터를 프로비전할 때 위치를 묻지 않습니다. 대신 기본적으로 저장소 계정이 있는 동일한 데이터 센터에 클러스터를 배치합니다. 따라서 클러스터에 대해 지원되는 위치인  **동아시아**, **동남아시아**, **북유럽**, **서유럽**, **미국 동부**, **미국 서부**, **미국 중북부**, **미국 중남부**에서 저장소 계정을 만들어야 합니다.

4. 새 저장소 계정의 **상태**가 **온라인**으로 변경될 때까지 기다립니다.
5. 목록에서 새 저장소 계정을 선택하고 페이지 아래쪽에서 **액세스 키 관리**를 클릭합니다.
7. **저장소 계정 이름** 및 **주 액세스 키**(또는 **보조 액세스 키**)를 적어 둡니다. 키 중 하나가 작동합니다.  이 정보는 자습서의 뒷부분에서 필요합니다.


자세한 내용은
[저장소 계정을 만드는 방법][azure-create-storageaccount] 및 [HDInsight에서 Azure Blob 저장소 사용][hdinsight-storage]을 참조하세요.
	
##<a name="provision"></a>HDInsight 클러스터 프로비전

HDInsight 클러스터를 프로비전할 때는 Hadoop과 관련 응용 프로그램을 포함하는 Azure 계산 리소스를 프로비전합니다. 이 섹션에서는 Hadoop 버전 2.4를 기반으로 하는 HDInsight 클러스터 버전 3.1을 프로비전합니다. Azure 포털, HDInsight PowerShell cmdlet 또는 HDInsight .NET SDK를 사용하여 다른 버전용 Hadoop 클러스터를 만들 수도 있습니다. 지침에 대해서는 [사용자 지정 옵션을 사용하여 HDInsight 클러스터 프로비전][hdinsight-provision]을 참조하세요. 여러 다른 HDInsight 버전 및 해당 SLA에 대한 [HDInsight 구성 요소 버전 관리](http://azure.microsoft.com/ko-kr/documentation/articles/hdinsight-component-versioning/) 페이지를 참조하세요.

[WACOM.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]


**HDInsight 클러스터를 프로비전하려면** 

1. [Azure 관리 포털][azure-management-portal]에 로그인합니다. 

2. 왼쪽에서 **HDInsight**를 클릭하여 계정의 클러스터 상태를 나열합니다. 다음 스크린샷에는 기존 HDInsight 클러스터가 없습니다.

	![Status of HDInsight clusters in the Azure portal.][image-hdi-clusterstatus]

3. 왼쪽 아래에서 **새로 만들기**를 클릭하고 **데이터 서비스**, **HDInsight**, **Hadoop**을 차례로 클릭합니다.

	![Creation of a Hadoop cluster in HDInsight.][image-hdi-quickcreatecluster]

4. 다음 값을 입력하거나 선택합니다.

	<table border="1">
	<tr><th>이름</th><th>값</th></tr>
	<tr><td>클러스터 이름</td><td>클러스터의 이름입니다.</td></tr>
	<tr><td>클러스터 크기</td><td>배포하려는 데이터 노드 수입니다. 기본값은 4입니다. 그러나 드롭다운 메뉴에서 1 또는 2개의 데이터 노드를 사용하는 옵션을 사용할 수도 있습니다.  <strong>사용자 지정 만들기</strong> 옵션을 사용하면 클러스터 노드 수를 임의로 지정할 수 있습니다. 다양한 클러스터 크기의 청구 요금에 대한 가격 책정 정보를 사용할 수 있습니다. 드롭다운 상자 위의 <strong>?</strong> 기호를 클릭하고 팝업의 링크를 따르세요.</td></tr>
	<tr><td>암호</td><td> <i>admin</i> 계정의 암호입니다.  <strong>사용자 지정 만들기</strong> 옵션을 사용하지 않을 때는 클러스터 사용자 이름 "admin"이 지정됩니다. 이 계정은 클러스터가 프로비전되는 VM에 대한 Windows 관리자 계정이 아닙니다. 계정 이름은 <strong>사용자 지정 만들기</strong> 마법사를 사용하여 변경할 수 있습니다.</td></tr>
	<tr><td>저장소 계정</td><td>드롭다운 상자에서 직접 만든 저장소 계정을 선택합니다. <br/>

	저장소 계정을 선택한 후에는 변경할 수 없습니다. 저장소 계정을 제거하면 더 이상 클러스터를 사용할 수 없습니다.

	HDInsight 클러스터는 저장소 계정이 있는 동일한 데이터 센터에 배치됩니다. 
	</td></tr>
	</table>

	클러스터 이름을 복사해 둡니다.  이 이름은 자습서의 뒷부분에서 필요합니다.

	
5. **HDInsight 클러스터 만들기**를 클릭합니다. 프로비전이 완료되면 상태 열에 **실행 중**이 표시됩니다.

	>[WACOM.NOTE] 위의 절차에서는 HDInsight 클러스터 버전 3.1을 사용하여 클러스터를 만듭니다. 다른 클러스터 버전을 만들려면 관리 포털에서 사용자 지정 만들기 방법을 사용하거나 Azure PowerShell을 사용합니다. 각 클러스터 버전 간 차이에 대한 자세한 내용은 [HDInsight에서 제공하는 클러스터 버전의 새로운 기능][hdinsight-versions](영문)을 참조하세요. **사용자 지정 만들기** 옵션 사용에 대한 자세한 내용은 [사용자 지정 옵션을 사용하여 HDInsight 클러스터 프로비전][hdinsight-provision]을 참조하세요.


##<a name="sample"></a>포털에서 샘플 실행

올바르게 프로비전된 HDInsight 클러스터는 포털에서 샘플을 직접 실행하기 위한 쿼리 콘솔을 제공합니다. 샘플을 통해 몇 가지 기본 시나리오를 단계별로 수행하여 HDInsight를 사용하는 방법을 익힐 수 있습니다. 이러한 샘플에는 분석할 데이터, 데이터에 대해 실행할 쿼리 등의 필요한 구성 요소가 모두 함께 제공됩니다. 

**샘플을 실행하려면** Azure 관리 포털에서 샘플을 실행할 클러스터 이름을 클릭한 다음 페이지 아래쪽에서 **쿼리 콘솔**을 클릭합니다. 그러면 열리는 웹 페이지에서 **시작 갤러리** 탭을 클릭하고 **샘플** 범주에서 실행할 샘플을 클릭합니다. 그런 다음 웹 페이지의 지침에 따라 샘플을 완료합니다. 각 샘플에서 수행하는 작업에 대해 자세히 알아보려면 아래 링크를 클릭하세요.

샘플 | 수행하는 작업
------ | ---------------
[센서 데이터 분석][hdinsight-sensor-data-sample] | HDInsight를 사용하여 HVAC(공기조화 냉난방) 시스템에서 생성하는 기록 데이터를 처리해 설정된 온도를 안정적으로 유지할 수 없는 시스템을 식별하는 방법에 대해 알아봅니다.
[웹 사이트 로그 분석][hdinsight-weblogs-sample] | HDInsight를 사용하여 웹 사이트 로그 파일을 분석해 외부 웹 사이트로부터의 일일 웹 사이트 방문 빈도를 파악하고 사용자에게 발생하는 웹 사이트 오류의 요약을 확인하는 방법에 대해 알아봅니다.


##<a name="hivequery"></a>포털에서 HIVE 쿼리 실행
HDInsight 클러스터를 프로비전했으므로 다음 단계는 Hive 작업을 실행하여 HDInsight 클러스터와 함께 제공되는 샘플 Hive 테이블인 *hivesampletable*을 쿼리하는 것입니다. 이 테이블에는 모바일 장치 제조업체, 플랫폼 및 모델에 대한 데이터가 들어 있습니다. 이 테이블을 쿼리하여 특정 제조업체별로 모바일 장치의 데이터를 검색합니다.

> [WACOM.NOTE] Visual Studio용 HDInsight 도구는 Azure SDK for .NET 버전 2.5 이상과 함께 제공됩니다.  Visual Studio에서 이 도구를 사용하여 HDInsight 클러스터에 연결하고, Hive 테이블을 만들고, Hive 쿼리를 실행할 수 있습니다.  자세한 내용은 [Visual Studio용 HDInsight Hadoop 도구 사용 시작][1]을 참조하세요.

**클러스터 대시보드에서 Hive 작업을 실행하려면**

1. [Azure 관리 포털][azure-management-portal]에 로그인합니다. 
2. 왼쪽 창에서 **HDINSIGHT**를 클릭합니다. 마지막 섹션에서 방금 만든 클러스터를 비롯하여 만들어진 클러스터의 목록이 표시됩니다.
3. Hive 작업을 실행할 클러스터 이름을 클릭하고 페이지 아래쪽에서 **쿼리 콘솔**을 클릭합니다. 
4. 다른 브라우저 탭에서 웹 페이지가 열립니다. Hadoop 사용자 계정과 암호를 입력합니다.  기본 사용자 이름은 **admin**이고 암호는 클러스터를 프로비전하는 동안 입력한 암호입니다.  대시보드는 다음과 같이 표시됩니다.

	![Hive Editor tab in the HDInsight cluster dashboard.][img-hdi-dashboard]

	맨 위에 몇 개의 탭이 있습니다.  기본 탭은 **Hive 편집기**이지만 **작업 기록** 및 **파일 브라우저** 탭도 있습니다.  대시보드를 사용하여 Hive 쿼리를 제출하고, Hadoop 작업 로그를 확인하고, WASB 파일을 찾아볼 수 있습니다.

	> [WACOM.NOTE] 웹 페이지의 URL은 *&lt;ClusterName&gt;.azurehdinsight.net*입니다. 따라서 관리 포털에서 대시보드를 여는 대신, URL을 사용하여 웹 브라우저에서 대시보드를 열 수도 있습니다.

6. **Hive 편집기** 탭에서 **쿼리 이름**으로 **HTC20**을 입력합니다.  쿼리 이름은 작업 제목입니다.

7. 쿼리 창에 다음 쿼리를 입력합니다. 

		SELECT * FROM hivesampletable
			WHERE devicemake LIKE "HTC%"
			LIMIT 20;

	![Query entered in the query pane of the Hive Editor.][img-hdi-dashboard-query-select]

4. **제출**을 클릭합니다. 잠시 후에 결과가 표시됩니다. 화면은 30초마다 한 번씩 새로 고쳐집니다. **새로 고침**을 클릭하여 화면을 새로 고칠 수도 있습니다.

    완료되면 화면은 다음과 같습니다.

	![Results from a Hive query in listed at the bottom of the cluster dashboard.][img-hdi-dashboard-query-select-result]

5. 화면에서 쿼리 이름을 클릭하여 출력을 확인합니다. **작업 시작 시간(UTC)**을 적어둡니다. 이 시간은 나중에 필요합니다. 

    ![Job Start Time listed in the Job History tab of the HDInsight cluster dashboard.][img-hdi-dashboard-query-select-result-output]

    페이지에는 **작업 출력**과 **작업 로그**도 표시됩니다. 출력 파일(\_stdout)과 로그 파일\(_stderr)을 다운로드하기 위한 옵션도 제공됩니다.


	> [WACOM.NOTE] **Hive 편집기** 탭의 **작업 세션** 테이블에는 해당 탭을 벗어나지 않는 경우 완료되었거나 실행 중인 작업이 표시됩니다. 이 페이지를 벗어나는 경우에는 테이블에 작업이 표시되지 않습니다. **작업 기록** 탭에는 완료되었거나 실행 중인 모든 작업 목록이 그대로 표시됩니다.
 

**출력 파일을 찾아보려면**

1. 클러스터 대시보드에서 맨 위에 있는 **파일 브라우저**를 클릭합니다. 
2. 저장소 계정 이름을 클릭하고 컨테이너 이름(클러스터 이름과 동일)을 클릭한 후 **사용자**를 클릭합니다.
3. **admin**을 클릭하고 마지막으로 수정한 시간이 앞서 기록한 작업 시작 시간보다 약간 나중인 GUID를 클릭합니다. 이 GUID를 기록해 둡니다. 다음 섹션에서 필요합니다.


   	![The output file GUID listed in the File Browser tab.][img-hdi-dashboard-query-browse-output]


###<a name="powerquery"></a>Microsoft 비즈니스 인텔리전스 도구에 연결 

Microsoft Excel용 파워 쿼리 추가 기능을 사용하여 HDInsight의 작업 출력을 Excel로 내보낼 수 있습니다. 그런 다음 Microsoft BI(비즈니스 인텔리전스) 도구를 사용하여 결과를 추가적으로 분석할 수 있습니다. 

이 자습서 부분을 완료하려면 Excel 2010 또는 2013이 설치되어 있어야 합니다. 

**Excel용 Microsoft 파워 쿼리를 다운로드하려면**

- [Microsoft 다운로드 센터](http://www.microsoft.com/ko-kr/download/details.aspx?id=39379) 에서 Microsoft Excel용 Microsoft 파워 쿼리를 다운로드하여 설치합니다.

**HDInsight 데이터를 가져오려면**

1. Excel을 열고 새로운 빈 통합 문서를 만듭니다.
3. **파워 쿼리** 메뉴를 클릭하고 **기타 원본**을 클릭한 후 **Azure HDInsight에서**를 클릭합니다.

	![Excel PowerQuery Import menu open for Azure HDInsight.][image-hdi-gettingstarted-powerquery-importdata]

3. 클러스터와 연결된 Azure Blob 저장소 계정의 **계정 이름**을 입력하고 **확인**을 클릭합니다. 이 계정은 자습서의 앞부분에서 만든 저장소 계정입니다.
4. Azure Blob 저장소 계정의 **계정 키**를 입력하고 **저장**을 클릭합니다. 
5. 오른쪽의 탐색 창에서 Blob 저장소 컨테이너 이름을 두 번 클릭합니다. 기본적으로 컨테이너 이름은 클러스터 이름과 같습니다. 

6. **이름** 열에서 **stdout**를 찾습니다. 해당 **폴더 경로** 열의 GUID가 이전에 적어둔 GUID와 일치하는지 확인합니다. 두 GUID가 일치하는 경우 출력 데이터가 제출한 작업에 해당하는 것입니다. **stdout** 왼쪽의 **이진**을 클릭합니다.

	![Finding the data output by GUID in the list of content.][image-hdi-gettingstarted-powerquery-importdata2]

9. 왼쪽 위에서 **닫은 후 로드**를 클릭하여 Hive 작업 출력을 Excel로 가져옵니다.


##<a name="nextsteps"></a>다음 단계
이 자습서에서는 HDInsight를 사용하여 클러스터를 프로비전하고 클러스터에서 MapReduce 작업을 실행한 후 BI 도구를 사용하여 추가 처리하고 그래픽으로 표시할 수 있는 Excel로 결과를 가져오는 방법을 배웠습니다. 자세한 내용은 다음 문서를 참조하세요.

- [Visual Studio용 HDInsight Hadoop 도구 사용 시작][1]
- [HDInsight Emulator 시작][hdinsight-emulator]
- [HDInsight에서 Azure Blob 저장소 사용][hdinsight-storage]
- [PowerShell을 사용하여 HDInsight 관리][hdinsight-admin-powershell]
- [HDInsight에 데이터 업로드][hdinsight-upload-data]
- [HDInsight와 함께 MapReduce 사용][hdinsight-use-mapreduce]
- [HDInsight에서 Hive 사용][hdinsight-use-hive]
- [HDInsight에서 Pig 사용][hdinsight-use-pig]
- [HDInsight에서 Oozie 사용][hdinsight-use-oozie]
- [HDInsight용 C# Hadoop 스트리밍 프로그램 개발][hdinsight-develop-streaming]
- [HDInsight용 Java MapReduce 프로그램 개발][hdinsight-develop-mapreduce]


[1]: ../hdinsight-hadoop-visual-studio-tools-get-started/

[hdinsight-versions]: ../hdinsight-component-versioning/

[hdinsight-get-started-30]: ../hdinsight-get-started-30/
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-use-mapreduce]: ../hdinsight-use-mapreduce
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/
[hdinsight-use-oozie]: ../hdinsight-use-oozie/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-emulator]: ../hdinsight-get-started-emulator/
[hdinsight-develop-streaming]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
[hdinsight-develop-mapreduce]: ../hdinsight-develop-deploy-java-mapreduce/
[hadoop-hdinsight-intro]: ../hdinsight-hadoop-introduction/
[hdinsight-weblogs-sample]: ../hdinsight-hive-analyze-website-log/
[hdinsight-sensor-data-sample]: ../hdinsight-hive-analyze-sensor-data/

[azure-purchase-options]: http://azure.microsoft.com/ko-kr/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/ko-kr/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/ko-kr/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/ 

[apache-hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
[apache-hive]: http://go.microsoft.com/fwlink/?LinkId=510085
[apache-mapreduce]: http://go.microsoft.com/fwlink/?LinkId=510086
[apache-hdfs]: http://go.microsoft.com/fwlink/?LinkId=510087
[hdinsight-hbase-custom-provision]: http://azure.microsoft.com/ko-kr/documentation/articles/hdinsight-hbase-get-started/


[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: ../install-configure-powershell/
[powershell-open]: ../install-configure-powershell/#Install


[img-hdi-dashboard]: ./media/hdinsight-get-started/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-get-started/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.png
[img-hdi-dashboard-query-select-result-output]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.output.png
[img-hdi-dashboard-query-browse-output]: ./media/hdinsight-get-started/HDI.dashboard.query.browse.output.png

[img-hdi-getstarted-video]: ./media/hdinsight-get-started/HDI.GetStarted.Video.png


[image-hdi-storageaccount-quickcreate]: ./media/hdinsight-get-started/HDI.StorageAccount.QuickCreate.png
[image-hdi-clusterstatus]: ./media/hdinsight-get-started/HDI.ClusterStatus.png
[image-hdi-quickcreatecluster]: ./media/hdinsight-get-started/HDI.QuickCreateCluster.png
[image-hdi-getstarted-flow]: ./media/hdinsight-get-started/HDI.GetStartedFlow.png

[image-hdi-gettingstarted-powerquery-importdata]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData.png
[image-hdi-gettingstarted-powerquery-importdata2]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData2.png

<!--HONumber=35.1-->
