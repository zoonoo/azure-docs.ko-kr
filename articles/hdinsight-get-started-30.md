<properties linkid="manage-services-hdinsight-get-started-hdinsight-hadoop-3.0" urlDisplayName="Get Started" pageTitle="Get started using Hadoop in HDInsight | Azure" metaKeywords="" description="Get started using Hadoop in HDInsight, a big data solution. Learn how to provision clusters, run hive jobs, and output data to Excel for analysis." metaCanonical="" services="hdinsight" documentationCenter="" title="Get started using Hadoop in HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao"></tags>


# HDInsight에서 Hadoop 2.2 사용 시작

<div class="dev-center-tutorial-selector sublanding">
<a href="../hdinsight-get-started" title="HDInsight에서 Hadoop 2.4 사용 시작">Hadoop 2.4</a>
<a href="../hdinsight-get-started-30" title="HDInsight에서 Hadoop 2.2 사용 시작" class="current">Hadoop 2.2</a>
<a href="../hdinsight-get-started-21" title="Get started using Hadoop 1.2 in HDInsight">Hadoop 1.2</a>
</div>

HDInsight를 통해 더 단순하고 확장 가능하며 비용 효과적인 Azure 환경에서 MapReduce 소프트웨어 프레임워크인 Apache Hadoop을 사용할 수 있습니다. 또한 HDInsight는 Azure Blob 저장소를 사용하여 데이터를 관리 및 저장하는 비용 효과적인 방법을 제공합니다.

> [WACOM.NOTE] Hadoop 및 빅 데이터를 처음 사용하는 경우에는 용어 [Apache Hadoop][], [MapReduce][], [HDFS][] 및 [Hive][]에 대해 좀 더 자세한 정보를 원할 수 있습니다.

Azure HDInsight의 일반적인 가용성과 더불어 Microsoft는 Azure용 HDInsight Emulator(이전의 *Microsoft HDInsight Developer Preview*)도 제공합니다. 이 Emulator는 개발자 시나리오를 대상으로 하기 때문에 단일 노드 배포만 지원합니다. HDInsight Emulator 사용에 대한 자세한 내용은 [HDInsight Emulator 시작][]을 참조하세요.

> [WACOM.NOTE] HBase 클러스터를 프로비전하는 방법에 대한 지침은 [HDInsight에서 HBase 클러스터 프로비전][](영문)을 참조하세요. [Hadoop와 HBase 간 차이점][](영문)을 참조하여 두 가지 기능 중 하나를 선택하게 되는 이유를 이해할 수 있습니다.

## 이 자습서의 목적

구조화되지 않은 대량의 데이터 집합이 있고 쿼리를 실행하여 의미 있는 일부 정보를 추출하려고 한다면 이 자습서를 통해 적절한 도움을 얻을 수 있을 것입니다. 해당 방법은 다음과 같습니다.

   ![HDI.GetStartedFlow][]

이 자습서의 데모 비디오를 시청할 수도 있습니다.

<center><iframe width="560" height="315" src="http://www.youtube.com/embed/v=Y4aNjnoeaHA?list=PLDrz-Fkcb9WWdY-Yp6D4fTC1ll_3lU-QS" frameborder="0" allowfullscreen></iframe></center>

<center><a href="https://www.youtube.com/watch?v=Y4aNjnoeaHA&list=PLDrz-Fkcb9WWdY-Yp6D4fTC1ll_3lU-QS" target = "_blank">![HDI.getstarted.video][img-hdi-getstarted-video]</a></center>

**필수 조건:**

이 자습서를 시작하기 전에 다음이 있어야 합니다.

- Azure 구독. 구독을 얻는 방법에 대한 자세한 내용은 [구매 옵션][], [구성원 제공 항목][] 또는 [무료 평가판][]을 참조하세요.
- Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013 Standalone 또는 Office 2010 Professional Plus가 있는 컴퓨터

**예상 완료 시간:** 30분

## 자습서 내용

* [Azure 저장소 계정 만들기][]
* [HDInsight 클러스터 프로비전][]
* [Hive 작업 실행][]
* [Microsoft 비즈니스 인텔리전스 도구에 연결][]
* [다음 단계][]

## <a name="storage"></a>Azure 저장소 계정 만들기

HDInsight는 데이터 저장을 위해 Azure Blob 저장소를 사용합니다. 이를 *WASB* 또는 *Azure 저장소 - Blob*이라고 합니다. WASB는 Azure Blob 저장소에 구현한 Microsoft의 HDFS입니다. 자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용][]을 참조하세요.

HDInsight 클러스터를 프로비전할 때 Azure 저장소 계정을 지정합니다. HDFS의 경우처럼 해당 계정의 특정 Blob 저장소 컨테이너는 기본 파일 시스템으로 지정됩니다. HDInsight 클러스터는 기본적으로 사용자가 지정한 저장소 계정과 동일한 데이터 센터에 프로비전됩니다.

사용자 지정 방식으로 HDInsight 클러스터를 구성할 때는 이 저장소 계정 외에 다른 저장소 계정을 추가할 수 있습니다. 이 추가 저장소 계정은 동일한 Azure 구독에서 가져오거나 다른 Azure 구독에서 가져올 수 있습니다. 지침에 대해서는 [사용자 지정 옵션을 사용하여 HDInsight 클러스터 프로비전][]을 참조하세요.

이 자습서와 과정을 간소화하기 위해 기본 Blob 컨테이너와 기본 저장소 계정만 사용됩니다. 실제로는 데이터 파일이 보통 지정된 저장소 계정에 저장됩니다.

**Azure 저장소 계정을 만들려면**

1. [Azure 관리 포털][]에 로그인합니다.
2. 왼쪽 아래에서 **새로 만들기**를 클릭하고 **데이터 서비스**, **저장소**를 차례로 가리킨 후 **빠른 생성**을 클릭합니다.

    ![HDI.StorageAccount.QuickCreate][]

3. **URL**, **위치** 및 **복제**를 입력하고 **저장소 계정 만들기**를 클릭합니다. 선호도 그룹은 지원되지 않습니다. 저장소 목록에 새 저장소 계정이 표시됩니다.


    > [WACOM.NOTE] HDInsight 클러스터 및 연결된 Azure 저장소 계정은 동일한 데이터 센터에 있어야 합니다. 따라서 클러스터에 대해 지원되는 위치인 **동아시아**, **동남아시아**, **북유럽**, **서유럽**, **미국 동부**, **미국 서부**, **미국 북중부**, **미국 남중부**에 저장소 계정을 만들어야 합니다.


4. 새 저장소 계정의 **상태**가 **온라인**으로 변경될 때까지 기다립니다.
5. 목록에서 새 저장소 계정을 선택하고 페이지 아래쪽에서 **액세스 키 관리**를 클릭합니다.
6. **저장소 계정 이름** 및 **기본 액세스 키**(또는 **보조 액세스 키**)를 적어 둡니다. 키 중 하나가 작동합니다. 이 정보는 자습서의 뒷부분에서 필요합니다.


자세한 내용은
[저장소 계정을 만드는 방법][] 및 [HDInsight에서 Azure Blob 저장소 사용][]을 참조하세요.

## <a name="provision"></a>HDInsight 클러스터 프로비전

HDInsight 클러스터를 프로비전할 때는 Hadoop과 관련 응용 프로그램을 포함하는 Azure 계산 리소스를 프로비전합니다. 이 섹션에서는 Hadoop 버전 2.2를 기반으로 하는 HDInsight 클러스터 버전 3.0을 프로비전합니다. Hadoop 버전 2.4를 사용하여 HDInsight 클러스터를 프로비전하려면 이 문서 맨 앞에 나오는 특정 버전 탭을 클릭하세요. HDInsight PowerShell cmdlet 또는 HDInsight .NET SDK를 사용하여 다른 버전용 Hadoop 클러스터를 만들 수도 있습니다. 지침에 대해서는 [사용자 지정 옵션을 사용하여 HDInsight 클러스터 프로비전][]을 참조하세요. 여러 다른 HDInsight 버전 및 해당 SLA에 대한 [HDInsight 구성 요소 버전 관리][] 페이지를 참조하세요.

**HDInsight 클러스터를 프로비전하려면**

1. [Azure 관리 포털][]에 로그인합니다.

2. 왼쪽에서 **HDInsight**를 클릭하여 계정의 클러스터 상태를 나열합니다. 다음 스크린샷에는 기존 HDInsight 클러스터가 없습니다.

    ![HDI.ClusterStatus][]

3. 왼쪽 아래에서 **새로 만들기**를 클릭한 후 **데이터 서비스**, **HDInsight**, **사용자 지정 만들기**를 차례로 클릭합니다.

    ![HDI.CustomCreateCluster][]


    위 이미지에 표시되는 대로 값을 입력하거나 선택하고 오른쪽 화살표를 클릭합니다.

4. **클러스터 구성** 페이지에서 다음 값을 입력하거나 선택합니다.

	<table border="1">
	<tr><th>이름</th><th>값</th></tr>
	<tr><td>데이터 노드</td><td>배포하려는 데이터 노드 수입니다. 테스트 목적으로 단일 노드 클러스터를 만듭니다. <br/>클러스터 크기 제한은 Azure 구독에 따라 다릅니다. 제한을 늘리려면 Azure 청구 지원 팀에 문의하세요.</td></tr>
	<tr><td>지역/가상 네트워크</td><td>마지막 절차에서 만든 저장소 계정과 동일한 지역을 선택합니다. HDInsight는 저장소 계정이 동일한 지역에 있어야 합니다. 구성의 뒷부분에서 여기서 지정한 지역과 동일한 지역에 있는 저장소 계정만 선택할 수 있습니다. 사용 가능한 지역은 <strong>동아시아</strong>, <strong>동남아시아</strong>, <strong>북유럽</strong>, <strong>서유럽</strong>, <strong>미국 동부</strong>, <strong>미국 서부</strong>, <strong>미국 북중부</strong>, <strong>미국 남중부입니다.</strong></td></tr>
	</table>

    오른쪽 화살표를 클릭합니다.

5. **클러스터 사용자 구성** 페이지에서 다음 값을 제공합니다.

    ![HDI.CustomCreateCluster.ClusterUser][]

    <table border='1'>
		<tr><th>속성</th><th>값</th></tr>
		<tr><td>사용자 이름</td>
			<td>HDInsight 클러스터 사용자 이름 지정</td></tr>
		<tr><td>암호/암호 확인</td>
			<td>HDInsight 클러스터 사용자 암호 지정</td></tr>
		<tr><td>Hive/Oozie Metastore 입력</td>
			<td>클러스터와 동일한 데이터 센터에서 Hive/Oozie 메타스토어로 사용할 SQL 데이터베이스를 지정하려면 이 확인란을 선택합니다. 이 확인란은 클러스터가 삭제된 후에도 Hive/Oozie 작업에 대한 메타데이터를 유지하려는 경우에 유용합니다.</td></tr>
		<tr><td>메타스토어 데이터베이스</td>
			<td>Hive/OOzie용 메타스토어로 사용할 Azure SQL 데이터베이스 지정. 이 SQL 데이터베이스는 HDInsight 클러스터와 동일한 데이터 센터에 있어야 합니다. 목록 상자에는 <strong>클러스터 세부 정보</strong> 페이지에서 지정한 것과 동일한 데이터 센터에 있는 SQL 데이터베이스만 나열됩니다.</td></tr>
		<tr><td>데이터베이스 사용자</td>
			<td>데이터베이스에 연결하는 데 사용할 SQL 데이터베이스 사용자 지정</td></tr>
		<tr><td>데이터베이스 사용자 암호</td>
			<td>SQL 데이터베이스 사용자 암호 지정</td></tr>
	</table>


    > [WACOM.NOTE] 메타스토어에 사용되는 Azure SQL 데이터베이스는 Azure HDInsight를 비롯한 다른 Azure 서비스로의 연결을 허용해야 합니다. Azure SQL 데이터베이스 대시보드의 오른쪽에서 서버 이름을 클릭합니다. 이 서버는 SQL 데이터베이스 인스턴스가 실행되는 서버입니다. 서버 보기에서 **구성**을 클릭하고 **Microsoft Azure 서비스**에 대해 **예**를 클릭한 다음 **저장**을 클릭합니다.


    오른쪽 화살표를 클릭합니다.

6. **저장소 계정** 페이지에서 다음 값을 제공합니다.

    ![HDI.CustomCreateCluster.StorageAccount][]

	<table border='1'>
		<tr><th>속성</th><th>값</th></tr>
		<tr><td>저장소 계정</td>
			<td>HDInsight 클러스터의 기본 파일 시스템으로 사용할 Azure 저장소 계정<ul>
				<li>기존 저장소 사용</li>
				<li>새 저장소 만들기</li>
				<li>다른 구독의 저장소 사용</li>
			</ul>
			</td></tr>
		<tr><td>계정 이름</td>
			<td><ul>
				<li>기존 저장소를 사용하도록 선택한 경우 <strong>계정 이름</strong>에 대해 기존 저장소 계정을 선택합니다. 드롭다운에는 클러스터를 프로비전하도록 선택한 동일한 데이터 센터에 있는 저장소 계정만 표시됩니다.</li>
    <li><strong>새 저장소 만들기</strong> 또는 <strong>다른 구독의 저장소 사용</strong> 옵션을 선택한 경우에는 저장소 계정 이름을 제공해야 합니다.</li>
				<li>계정 키</td>
    <td align="left"><strong>다른 구독의 저장소 사용</strong> 옵션을 선택한 경우 해당 저장소 계정의 계정 키를 지정합니다.</td>
    </tr>
    <tr class="even">
    <td align="left">기본 컨테이너</td>
    <td align="left"><p>HDInsight 클러스터의 기본 파일 시스템으로 사용할 저장소 계정의 기본 컨테이너를 지정합니다. <strong>저장소 계정</strong> 필드에 대해 <strong>기존 저장소 사용</strong>을 선택했으며 해당 계정에 기존 컨테이너가 없는 경우 기본적으로 클러스터 이름과 같은 이름의 컨테이너가 만들어집니다. 클러스터의 이름을 가진 컨테이너가 이미 있는 경우에는 컨테이너 이름에 시퀀스 번호가 추가됩니다. 예를 들어 mycontainer1, mycontainer2 등과 같습니다. 그렇지만 기존 저장소 계정에 지정한 클러스터 이름과는 다른 이름의 컨테이너가 들어 있으면 해당 컨테이너도 사용할 수 있습니다.</p></td>
    </tr>
    <tr class="odd">
    <td align="left">추가 저장소 계정</td>
    <td align="left">HDInsight는 여러 저장소 계정을 지원합니다. 클러스터에서 사용할 수 있는 추가 저장소 계정의 수에는 제한이 없습니다. 하지만 관리 포털을 사용하여 클러스터를 만드는 경우에는 UI 제약으로 인해 7개로 제한됩니다. 지정하는 각 추가 저장소 계정은 마법사에 계정 정보를 지정할 수 있는 저장소 계정 페이지를 더합니다. 예를 들어 위의 스크린샷에서 하나의 추가 저장소 계정이 선택되면 5페이지가 대화 상자에 추가됩니다.</td>
    </tr>
    </tbody>
    </table>

    추가 저장소 계정을 선택한 경우에는 오른쪽 화살표를 클릭합니다. 그렇지 않은 경우에는 확인 표시를 클릭하여 클러스터 프로비전을 시작합니다. 프로비전이 완료되면 상태 열에 **실행 중**이 표시됩니다.

7. **저장소 계정** 페이지에서 추가 저장소 계정을 선택한 경우 해당 계정 정보를 입력합니다.

    ![HDI.CustomCreateCluster.AddOnStorage][]

    여기서 다시 기존 저장소에서 선택하거나, 새 저장소를 만들거나, 다른 Azure 구독의 저장소를 사용하기 위한 옵션이 제공됩니다. 값을 제공하는 절차는 이전 단계와 비슷합니다.

    확인 표시를 클릭하여 클러스터 프로비전을 시작합니다. 프로비전이 완료되면 상태 열에 **실행 중**이 표시됩니다.

## <a name="sample"></a>Hive 작업 실행

HDInsight 클러스터를 프로비전헀으므로 다음 단계는 Hive 작업을 실행하여 HDInsight 클러스터와 함께 제공되는 샘플 Hive 테이블인 *hivesampletable*을 쿼리하는 것입니다. 이 테이블에는 모바일 장치 제조업체, 플랫폼 및 모델에 대한 데이터가 들어 있습니다. 이 테이블을 쿼리하여 특정 제조업체별로 모바일 장치의 데이터를 검색합니다.

**클러스터 대시보드에서 Hive 작업을 실행하려면**

1. [Azure 관리 포털][]에 로그인합니다.
2. 왼쪽 창에서 **HDINSIGHT**를 클릭합니다. 마지막 섹션에서 방금 만든 클러스터를 비롯하여 만들어진 클러스터의 목록이 표시됩니다.
3. Hive 작업을 실행할 클러스터 이름을 클릭하고 페이지 아래쪽에서 **클러스터 관리**를 클릭합니다.
4. 다른 브라우저 탭에서 웹 페이지가 열립니다. Hadoop 사용자 계정과 암호를 입력합니다. 기본 사용자 이름은 **admin**이고 암호는 클러스터를 프로비전하는 동안 입력한 암호입니다. 대시보드는 다음과 유사하게 표시됩니다.

    ![hdi.dashboard][]

    맨 위에 몇 개의 탭이 있습니다. 기본 탭은 **Hive 편집기**이지만 **작업 기록** 및 **파일 브라우저** 탭도 있습니다. 대시보드를 사용하여 Hive 쿼리를 제출하고, Hadoop 작업 로그를 확인하고, WASB 파일을 찾아볼 수 있습니다.

    > [WACOM.NOTE] 웹 페이지의 URL은 *&lt;ClusterName&gt;.azurehdinsight.net*입니다. 따라서 관리 포털에서 대시보드를 여는 대신, URL을 사용하여 웹 브라우저에서 대시보드를 열 수도 있습니다.

5. **Hive 편집기** 탭에서 **쿼리 이름**으로 **HTC20**을 입력합니다. 쿼리 이름은 작업 제목입니다.

6. 쿼리 창에 다음 쿼리를 입력합니다.

        SELECT * FROM hivesampletable
            WHERE devicemake LIKE "HTC%"
            LIMIT 20;

    ![hdi.dashboard.query.select][]

7. **Submit**를 클릭합니다. 잠시 후에 결과가 표시됩니다. 화면은 30초마다 한 번씩 새로 고쳐집니다. **새로 고침**을 클릭하여 화면을 새로 고칠 수도 있습니다.

    완료되면 화면은 다음과 같습니다.

    ![hdi.dashboard.query.select.result][]

8. 화면에서 쿼리 이름을 클릭하여 출력을 확인합니다. **작업 시작 시간(UTC)**을 적어둡니다. 이 시간은 나중에 필요합니다.

    ![hdi.dashboard.query.select.result.output][]

    페이지에는 **작업 출력**과 **작업 로그**도 표시됩니다. 출력 파일(_stdout)과 로그 파일(_stderr)을 다운로드하기 위한 옵션도 제공됩니다.

    > [WACOM.NOTE] **Hive 편집기** 탭의 **작업 세션** 테이블에는 해당 탭을 벗어나지 않는 경우 완료되었거나 실행 중인 작업이 표시됩니다. 이 페이지를 벗어나는 경우에는 테이블에 작업이 표시되지 않습니다. **작업 기록** 탭에는 완료되었거나 실행 중인 모든 작업 목록이 그대로 표시됩니다.

**출력 파일을 찾아보려면**

1. 클러스터 대시보드에서 맨 위에 있는 **파일 브라우저**를 클릭합니다.
2. 저장소 계정 이름을 클릭하고 컨테이너 이름(클러스터 이름과 동일)을 클릭한 후 **사용자**를 클릭합니다.
3. admin을 클릭하고 마지막으로 수정한 시간이 앞서 기록한 작업 시작 시간보다 약간 나중인 GUID 번호를 클릭합니다. 이 GUID를 기록해 둡니다. 다음 섹션에서 필요합니다.


    ![hdi.dashboard.query.browse.output][]


## <a name="powerquery"></a>Microsoft 비즈니스 인텔리전스 도구에 연결

Microsoft Excel용 파워 쿼리 추가 기능을 사용하여 HDInsight의 작업 출력을 Excel로 내보낼 수 있습니다. 그런 다음 Microsoft BI(비즈니스 인텔리전스) 도구를 사용하여 결과를 추가적으로 분석할 수 있습니다.

이 자습서 부분을 완료하려면 Excel 2010 또는 2013이 설치되어 있어야 합니다.

**Excel용 Microsoft 파워 쿼리를 다운로드하려면**

- [Microsoft 다운로드 센터][]에서 Microsoft Excel용 Microsoft 파워 쿼리를 다운로드하여 설치합니다.

**HDInsight 데이터를 가져오려면**

1. Excel을 열고 새로운 빈 통합 문서를 만듭니다.
2. **파워 쿼리** 메뉴를 클릭하고 **기타 원본**을 클릭한 후 **Azure HDInsight에서**를 클릭합니다.

    ![HDI.GettingStarted.PowerQuery.ImportData][]

3. 클러스터와 연결된 Azure Blob 저장소 계정의 **계정 이름**을 입력하고 **확인**을 클릭합니다. 이 계정은 자습서의 앞부분에서 만든 저장소 계정입니다.
4. Azure Blob 저장소 계정의 **계정 키**를 입력하고 **저장**을 클릭합니다.
5. 오른쪽의 탐색 창에서 Blob 저장소 컨테이너 이름을 두 번 클릭합니다. 기본적으로 컨테이너 이름은 클러스터 이름과 같습니다.

6. **이름** 열에서 **stdout**을 찾습니다. 해당 폴더 경로 열의 GUID가 이전에 적어둔 GUID와 일치하는지 확인합니다. **stdout** 왼쪽의 **이진**을 클릭합니다.

    ![HDI.GettingStarted.PowerQuery.ImportData2][]

7. 왼쪽 위 구석에서 **닫은 후 로드**를 클릭하여 Hive 작업 출력을 Excel로 가져옵니다.

## <a name="nextsteps"></a>다음 단계
이 자습서에서는 HDInsight를 사용하여 클러스터를 프로비전하고 클러스터에서 MapReduce 작업을 실행한 후 BI 도구를 사용하여 추가 처리하고 그래픽으로 표시할 수 있는 Excel로 결과를 가져오는 방법을 배웠습니다. 자세한 내용은 다음 문서를 참조하세요.

- [HDInsight Emulator 시작][]
- [HDInsight에서 Azure Blob 저장소 사용][]
- [PowerShell을 사용하여 HDInsight 관리][]
- [HDInsight에 데이터 업로드][]
- [HDInsight와 함께 MapReduce 사용][]
- [HDInsight에서 Hive 사용][]
- [HDInsight에서 Pig 사용][]
- [HDInsight에서 Oozie 사용][]
- [HDInsight용 C# Hadoop 스트리밍 프로그램 개발][]
- [HDInsight용 Java MapReduce 프로그램 개발][]

  [Hadoop 2.4]: ../hdinsight-get-started "HDInsight에서 Hadoop 2.4 사용 시작"
  [Hadoop 2.2]: ../hdinsight-get-started-30 "HDInsight에서 Hadoop 2.2 사용 시작"
  [Apache Hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
  [MapReduce]: http://go.microsoft.com/fwlink/?LinkId=510086
  [HDFS]: http://go.microsoft.com/fwlink/?LinkId=510087
  [Hive]: http://go.microsoft.com/fwlink/?LinkId=510085
  [HDInsight Emulator 시작]: ../hdinsight-get-started-emulator/
  [HDInsight에서 HBase 클러스터 프로비전]: http://azure.microsoft.com/ko-kr/documentation/articles/hdinsight-hbase-get-started/
  [Hadoop와 HBase 간 차이점]: http://go.microsoft.com/fwlink/?LinkId=510237
  [HDI.GetStartedFlow]: ./media/hdinsight-get-started/HDI.GetStartedFlow.png
  [구매 옵션]: http://azure.microsoft.com/en-us/pricing/purchase-options/
  [구성원 제공 항목]: http://azure.microsoft.com/en-us/pricing/member-offers/
  [무료 평가판]: http://azure.microsoft.com/en-us/pricing/free-trial/
  [Azure 저장소 계정 만들기]: #storage
  [HDInsight 클러스터 프로비전]: #provision
  [Hive 작업 실행]: #sample
  [Microsoft 비즈니스 인텔리전스 도구에 연결]: #powerquery
  [다음 단계]: #nextsteps
  [HDInsight에서 Azure Blob 저장소 사용]: ../hdinsight-use-blob-storage/
  [사용자 지정 옵션을 사용하여 HDInsight 클러스터 프로비전]: ../hdinsight-provision-clusters/
  [Azure 관리 포털]: https://manage.windowsazure.com/
  [HDI.StorageAccount.QuickCreate]: ./media/hdinsight-get-started/HDI.StorageAccount.QuickCreate.png
  [저장소 계정을 만드는 방법]: ../storage-create-storage-account/
  [HDInsight 구성 요소 버전 관리]: http://azure.microsoft.com/ko-kr/documentation/articles/hdinsight-component-versioning/
  [HDI.ClusterStatus]: ./media/hdinsight-get-started/HDI.ClusterStatus.png
  [HDI.CustomCreateCluster]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
  [HDI.CustomCreateCluster.ClusterUser]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.ClusterUser.png
  [HDI.CustomCreateCluster.StorageAccount]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.StorageAccount.png
  [HDI.CustomCreateCluster.AddOnStorage]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.AddOnStorage.png
  [hdi.dashboard]: ./media/hdinsight-get-started/HDI.dashboard.png
  [hdi.dashboard.query.select]: ./media/hdinsight-get-started/HDI.dashboard.query.select.png
  [hdi.dashboard.query.select.result]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.png
  [hdi.dashboard.query.select.result.output]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.output.png
  [hdi.dashboard.query.browse.output]: ./media/hdinsight-get-started/HDI.dashboard.query.browse.output.png
  [Microsoft 다운로드 센터]: http://www.microsoft.com/en-us/download/details.aspx?id=39379
  [HDI.GettingStarted.PowerQuery.ImportData]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData.png
  [HDI.GettingStarted.PowerQuery.ImportData2]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData2.png
  [PowerShell을 사용하여 HDInsight 관리]: ../hdinsight-administer-use-powershell/
  [HDInsight에 데이터 업로드]: ../hdinsight-upload-data/
  [HDInsight와 함께 MapReduce 사용]: ../hdinsight-use-mapreduce
  [HDInsight에서 Hive 사용]: ../hdinsight-use-hive/
  [HDInsight에서 Pig 사용]: ../hdinsight-use-pig/
  [HDInsight에서 Oozie 사용]: ../hdinsight-use-oozie/
  [HDInsight용 C# Hadoop 스트리밍 프로그램 개발]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
  [HDInsight용 Java MapReduce 프로그램 개발]: ../hdinsight-develop-deploy-java-mapreduce/
 