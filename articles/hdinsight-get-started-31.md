<properties linkid="manage-services-hdinsight-get-started-hdinsight-hadoop-3.0" urlDisplayName="Get Started" pageTitle="Get started using Hadoop in HDInsight | Azure" metaKeywords="" description="Get started using Hadoop in HDInsight, a big data solution. Learn how to provision clusters, run hive jobs, and output data to Excel for analysis." metaCanonical="" services="hdinsight" documentationCenter="" title="Get started using Hadoop in HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao"></tags>

# HDInsight(미리 보기)에서 Hadoop 2.4 사용 시작

<div class="dev-center-tutorial-selector sublanding">
<a href="../hdinsight-get-started" title="HDInsight에서 Hadoop 2.2 사용 시작">Hadoop 2.2</a>
<a href="../hdinsight-get-started-31" title="HDInsight에서 Hadoop 2.4 사용 시작" class="current">Hadoop 2.4</a>
<a href="../hdinsight-get-started-21" title="HDInsight에서 Hadoop 1.2 사용 시작">Hadoop 1.2</a>
</div>

HDInsight는 클라우드에서 [Apache Hadoop][Apache Hadoop]을 서비스로 사용할 수 있게 해 줍니다. HDInsight를 통해 더 단순하고 확장 가능하며 비용 효과적인 Azure 환경에서 MapReduce 소프트웨어 프레임워크를 사용할 수 있습니다. 또한 HDInsight는 Azure Blob 저장소를 사용하여 데이터를 관리 및 저장하는 비용 효과적인 방법을 제공합니다.

이 자습서에서는 Azure 관리 포털을 사용하여 HDInsight에서 Hadoop 클러스터를 프로비전하고, HDInsight 클러스터 대시보드를 사용하여 샘플 Hive 테이블을 쿼리하는 Hive 작업을 제출한 후 Hive 작업 출력 데이터를 검토하기 위해 Excel로 가져옵니다.

> [WACOM.NOTE] 이 자습서는 HDInsight(미리 보기)에서 Hadoop 2.4 클러스터를 사용하는 경우를 다룹니다. 지원되는 다른 버전의 경우 페이지 상단에서 선택기를 클릭합니다. 버전 정보는 [HDInsight에서 제공하는 클러스터 버전의 새로운 기능][HDInsight에서 제공하는 클러스터 버전의 새로운 기능](영문)을 참조하세요.

이 문서의 라이브 데모:


<center><a href="https://www.youtube.com/watch?v=Y4aNjnoeaHA&list=PLDrz-Fkcb9WWdY-Yp6D4fTC1ll_3lU-QS" target = "_blank">![HDI.getstarted.video][img-hdi-getstarted-video]</a></center>


Azure HDInsight의 일반적인 가용성과 더불어 Microsoft는 Azure용 HDInsight Emulator(이전의 *Microsoft HDInsight Developer Preview*)도 릴리스했습니다. 이 제품은 개발자 시나리오를 대상으로 하기 때문에 단일 노드 배포만 지원합니다. HDInsight Emulator 사용에 대한 자세한 내용은 [HDInsight Emulator 시작][HDInsight Emulator 시작]을 참조하세요.

**필수 조건:**

이 자습서를 시작하기 전에 다음이 있어야 합니다.

-   Azure 구독. 구독을 얻는 방법에 대한 자세한 내용은 [구매 옵션][구매 옵션], [구성원 제공 항목][구성원 제공 항목] 또는 [무료 평가판][무료 평가판]을 참조하세요.
-   Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013 Standalone 또는 Office 2010 Professional Plus가 있는 컴퓨터
-   최신 HDInsight 릴리스 정보에 대해서는 [HDInsight 릴리스 정보][HDInsight 릴리스 정보](영문)를 참조하세요.

**예상 완료 시간:** 30분

## 자습서 내용

-   [HDInsight 클러스터 프로비전][HDInsight 클러스터 프로비전]
-   [Hive 작업 실행][Hive 작업 실행]
-   [Microsoft 비즈니스 인텔리전스 도구에 연결][Microsoft 비즈니스 인텔리전스 도구에 연결]
-   [다음 단계][다음 단계]

## <a name="provision"></a>HDInsight 클러스터 프로비전

HDInsight는 데이터 저장을 위해 Azure Blob 저장소를 사용합니다. 이를 *WASB* 또는 *Azure 저장소 - Blob*이라고 합니다. WASB는 Azure Blob 저장소에 구현한 Microsoft의 HDFS입니다. 자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용][HDInsight에서 Azure Blob 저장소 사용]을 참조하세요.

HDInsight 클러스터를 프로비전할 때 Azure 저장소 계정 및 이 계정에서 오는 특정 Blob 저장소 컨테이너가 HDFS의 경우와 같이 기본 파일 시스템으로 지정됩니다. 저장소 계정은 HDInsight 계산 리소스와 동일한 데이터 센터에 있어야 합니다. 현재 다음 데이터 센터에서만 HDInsight 클러스터를 프로비전할 수 있습니다.

-   동남아시아
-   북유럽
-   서유럽
-   미국 동부
-   미국 서부

이 저장소 계정 외에도 동일한 Azure 구독 또는 다른 Azure 구독에서 저장소 계정을 추가할 수 있습니다. 저장소 계정 추가에 대한 지침은 [HDInsight 클러스터 프로비전][1]을 참조하세요.

이 자습서를 간소화하기 위해 기본 Blob 컨테이너 및 기본 저장소 계정만 사용하며 모든 파일은 */tutorials/getstarted/*에 있는 기본 파일 시스템 컨테이너에 저장됩니다. 실제로는 데이터 파일이 보통 지정된 저장소 계정에 저장됩니다.

**Azure 저장소 계정을 만들려면**

1.  [Azure 관리 포털][Azure 관리 포털]에 로그인합니다.
2.  왼쪽 아래에서 **새로 만들기**를 클릭하고 **데이터 서비스**, **저장소**를 차례로 가리킨 후 **빠른 생성**을 클릭합니다.

    ![HDI.StorageAccount.QuickCreate][HDI.StorageAccount.QuickCreate]

3.  **URL**, **위치** 및 **복제**를 입력하고 **저장소 계정 만들기**를 클릭합니다. 선호도 그룹은 지원되지 않습니다. 저장소 목록에 새 저장소 계정이 표시됩니다.
4.  새 저장소 계정의 **상태**가 **온라인**으로 변경될 때까지 기다립니다.
5.  목록에서 새 저장소 계정을 클릭하여 선택합니다.
6.  페이지 맨 아래에서 **액세스 키 관리**를 클릭합니다.
7.  **저장소 계정 이름** 및 **기본 액세스 키**(또는 **보조 액세스 키**)를 적어 둡니다. 키 중 하나가 작동합니다. 이 정보는 자습서의 뒷부분에서 필요합니다.

자세한 내용은
[저장소 계정을 만드는 방법][저장소 계정을 만드는 방법] 및 [HDInsight에서 Azure Blob 저장소 사용][HDInsight에서 Azure Blob 저장소 사용]을 참조하세요.

**HDInsight 클러스터를 프로비전하려면**

1.  [Azure 관리 포털][Azure 관리 포털]에 로그인합니다.

2.  왼쪽에서 **HDINSIGHT**를 클릭하여 계정에 있는 HDInsight 클러스터를 나열합니다. 다음 스크린샷에는 기존 HDInsight 클러스터가 없습니다.

    ![HDI.ClusterStatus][HDI.ClusterStatus]

3.  왼쪽 아래에서 **새로 만들기**를 클릭한 후 **데이터 서비스**, **HDInsight**를 차례로 클릭한 후 **사용자 지정 만들기**를 클릭합니다.

    ![HDI.CustomCreateCluster][HDI.CustomCreateCluster]

4.  클러스터 세부 정보 탭에서 다음 값을 입력하거나 선택합니다.


	<table border="1">
	<tr><th>이름</th><th>값</th></tr>
	<tr><td><strong>클러스터 이름</strong></td><td>클러스터의 이름입니다.</td></tr>
	<tr><td><strong>데이터 노드</strong></td><td>배포하려는 데이터 노드 수입니다. 테스트 목적으로 단일 노드 클러스터를 만듭니다. <br />클러스터 크기 제한은 Azure 구독에 따라 다릅니다. 제한을 늘리려면 Azure 청구 지원 팀에 문의하세요.</td></tr>
	<tr><td><strong>HDInsight 버전</strong></td><td>HDInsight에서 Hadoop 2.4 클러스터를 만들려면 <strong>3.1</strong>을 선택합니다.</td></tr>
	<tr><td><strong>지역</strong></td><td>마지막 절차에서 만든 저장소 계정과 동일한 지역을 선택합니다. HDInsight는 저장소 계정이 동일한 지역에 있어야 합니다. 구성의 뒷부분에서 여기서 지정한 지역과 동일한 지역에 있는 저장소 계정만 선택할 수 있습니다.
	</td></tr>
	</table>


5.  오른쪽 아래에서 오른쪽 화살표를 클릭하여 클러스터 사용자를 구성합니다.
6.  클러스터 사용자 구성 탭에서 HDInsight 클러스터 사용자 계정의 **사용자 이름**과 **암호**를 입력합니다. 이 계정 외에도 클러스터를 프로비전한 후 RDP 사용자 계정을 만들어 클러스터에 원격 데스크톱으로 액세스할 수 있습니다. 지침에 대해서는 [관리 포털을 사용하여 HDInsight 관리][hdinsight-admin-portal]를 참조하세요.
7.  오른쪽 아래에서 오른쪽 화살표를 클릭하여 저장소 계정을 구성합니다.
8.  저장소 계정 태그에서 다음 값을 입력하거나 선택합니다.

    | 이름             | 값                                                                                                                              |
    |------------------|---------------------------------------------------------------------------------------------------------------------------------|
    | 저장소 계정      | **기존 저장소 사용**을 선택합니다. 또한 만들어진 저장소 계정이 없는 경우 관리 포털에서 새 저장소 계정을 만드는 옵션도 있습니다. |
    | 계정 이름        | 이 자습서의 마지막 절차에서 만든 저장소 계정을 지정합니다. 동일한 지역에 있는 저장소 계정만 목록 상자에 표시됩니다.             |
    | 기본 컨테이너    | **기본 컨테이너 만들기**를 선택합니다. 이 옵션을 선택하면 기본 컨테이너 이름은 클러스터 이름과 동일합니다.                      |
    | 추가 저장소 계정 | **0**을 선택합니다. 옵션으로 클러스터를 최대 7개의 추가 저장소 계정에 연결할 수 있습니다.                                       |

9.  오른쪽 아래에 있는 확인 표시를 클릭하여 클러스터를 만듭니다. 프로비전 프로세스가 완료되면 상태 열에 **실행 중**이 표시됩니다.

## <a name="sample"></a>Hive 작업 실행

이제 HDInsight 클러스터가 프로비전되었습니다. 다음 단계는 Hive 작업을 실행하여 HDInsight 클러스터와 함께 제공되는 샘플 Hive 테이블을 쿼리하는 것입니다. 테이블 이름은 *hivesampletable*입니다.

**HDInsight 클러스터 대시보드를 열려면**

1.  [Azure 관리 포털][Azure 관리 포털]에 로그인합니다.
2.  왼쪽 창에서 **HDINSIGHT**를 클릭합니다. 지난 섹션에서 만든 클러스터를 포함하여 생성된 클러스터의 목록이 표시됩니다.
3.  Hive 작업을 실행할 클러스터의 이름을 클릭합니다.
4.  페이지 아래에서 **클러스터 관리**를 클릭하여 HDInsight 클러스터 대시보드를 엽니다. 다른 브라우저 탭에서 웹 페이지가 열립니다.
5.  Hadoop 사용자 계정의 사용자 이름 및 암호를 입력합니다. 기본 사용자 이름은 **admin**이고 암호는 프로비전 프로세스에서 입력한 암호입니다. 대시보드는 다음과 유사하게 표시됩니다.

    ![hdi.dashboard][hdi.dashboard]

    맨 위에 몇 개의 탭이 있습니다. 기본 탭은 *Hive 편집기*이며, 다른 탭에는 작업 및 파일이 포함되어 있습니다. 대시보드를 사용하여 Hive 쿼리를 제출하고, Hadoop 작업 로그를 확인하고, WASB 파일을 찾아볼 수 있습니다.

> [wacom.note] URL은 *\<ClusterName\>.azurehdinsight.net*입니다. 관리 포털에서 대시보드를 여는 대신, URL을 사용하여 웹 브라우저에서 대시보드를 열 수도 있습니다.

**Hive 쿼리를 실행하려면**

1.  HDInsight 클러스터 대시보드의 상단에서 **Hive 편집기**를 클릭합니다.
2.  **쿼리 이름**에 **HTC20**을 입력합니다. 쿼리 이름은 작업 제목입니다.
3.  쿼리 창에 다음 쿼리를 입력합니다.

        SELECT * FROM hivesampletable
            WHERE devicemake LIKE "HTC%"
            LIMIT 20;

    ![hdi.dashboard.query.select][hdi.dashboard.query.select]

4.  **Submit**를 클릭합니다. 잠시 후에 결과가 표시됩니다. 화면은 30초마다 한 번씩 새로 고쳐집니다. **새로 고침**을 클릭하여 화면을 새로 고칠 수도 있습니다.

    완료되면 화면은 다음과 같습니다.

    ![hdi.dashboard.query.select.result][hdi.dashboard.query.select.result]

    **작업 시작 시간(UTC)**을 적어둡니다. 이 시간은 나중에 필요합니다.

    약간 아래로 스크롤하면 **작업 로그**가 표시됩니다. 작업 출력은 stdout이고, 작업 로그는 stderr입니다.

5.  나중에 로그 파일을 다시 열려면 화면 상단에서 **작업**을 클릭한 다음, 작업 제목(쿼리 이름)을 클릭하면 됩니다. 예를 들어 이 경우에는 **HTC20**입니다.

**출력 파일을 찾아보려면**

1.  HDInsight 클러스터 대시보드의 상단에서 **파일**을 클릭합니다.
2.  **Templeton-Job-Status**를 클릭합니다.
3.  마지막으로 수정한 시간이 앞에서 기록한 작업 시작 시간보다 약간 늦은 GUID 번호를 클릭합니다. 이 GUID를 기록해 둡니다. 다음 섹션에서 필요합니다.
4.  **stdout** 파일에는 다음 섹션에 필요한 데이터가 있습니다. 원하는 경우 **stdout**를 클릭하여 데이터 파일 복사본을 다운로드합니다.

## <a name="powerquery"></a>Microsoft 비즈니스 인텔리전스 도구에 연결

Excel의 파워 쿼리 추가 기능을 사용하여 HDInsight의 출력을 Excel로 내보낼 수 있습니다. 그런 다음 Excel에서 Microsoft BI(비즈니스 인텔리전스) 도구를 사용하여 추가로 처리하거나 결과를 표시할 수 있습니다.

이 자습서 부분을 완료하려면 Excel 2010 또는 2013이 설치되어 있어야 합니다.

**Excel용 Microsoft 파워 쿼리를 다운로드하려면**

-   [Microsoft 다운로드 센터][Microsoft 다운로드 센터]에서 Excel용 Microsoft 파워 쿼리를 다운로드하여 설치합니다.

**HDInsight 데이터를 가져오려면**

1.  Excel을 열고 새로운 빈 통합 문서를 만듭니다.
2.  **파워 쿼리** 메뉴를 클릭하고 **기타 원본**을 클릭한 후 **Azure HDInsight에서**를 클릭합니다.

    ![HDI.GettingStarted.PowerQuery.ImportData][HDI.GettingStarted.PowerQuery.ImportData]

3.  클러스터와 연결된 Azure Blob 저장소 계정의 **계정 이름**을 입력하고 **확인**을 클릭합니다. 이 계정은 자습서의 앞부분에서 만든 저장소 계정입니다.
4.  Azure Blob 저장소 계정의 **계정 키**를 입력하고 **저장**을 클릭합니다.
5.  오른쪽의 탐색 창에서 Blob 저장소 컨테이너 이름을 두 번 클릭합니다. 기본적으로 컨테이너 이름은 클러스터 이름과 같습니다.

6.  **Name** 열에서 **stdout**(경로 *.../Templeton-Job-Status/<guid>*)을 찾은 후 **stdout** 왼쪽에 있는 **Binary**를 클릭합니다. <guid>는 마지막 섹션에서 작성한 것과 일치해야 합니다.

    ![HDI.GettingStarted.PowerQuery.ImportData2][HDI.GettingStarted.PowerQuery.ImportData2]

7.  왼쪽 위에서 **적용 후 닫기**를 클릭합니다. 그러면 쿼리에서 Hive 작업 출력을 Excel로 가져옵니다.

## <a name="nextsteps"></a>다음 단계

이 자습서에서는 HDInsight를 사용하여 클러스터를 프로비전하고 클러스터에서 MapReduce 작업을 실행한 후 BI 도구를 사용하여 추가 처리하고 그래픽으로 표시할 수 있는 Excel로 결과를 가져오는 방법을 배웠습니다. 자세한 내용은 다음 문서를 참조하세요.

-   [HDInsight Emulator 시작][HDInsight Emulator 시작]
-   [HDInsight에서 Azure Blob 저장소 사용][HDInsight에서 Azure Blob 저장소 사용]
-   [PowerShell을 사용하여 HDInsight 관리][PowerShell을 사용하여 HDInsight 관리]
-   [HDInsight에 데이터 업로드][HDInsight에 데이터 업로드]
-   [HDInsight와 함께 MapReduce 사용][HDInsight와 함께 MapReduce 사용]
-   [HDInsight에서 Hive 사용][HDInsight에서 Hive 사용]
-   [HDInsight에서 Pig 사용][HDInsight에서 Pig 사용]
-   [HDInsight에서 Oozie 사용][HDInsight에서 Oozie 사용]
-   [HDInsight용 C\# Hadoop 스트리밍 프로그램 개발][HDInsight용 C\# Hadoop 스트리밍 프로그램 개발]
-   [HDInsight용 Java MapReduce 프로그램 개발][HDInsight용 Java MapReduce 프로그램 개발]

  [Hadoop 2.2]: ../hdinsight-get-started "HDInsight에서 Hadoop 2.2 사용 시작"
  [Hadoop 2.4]: ../hdinsight-get-started-31 "HDInsight에서 Hadoop 2.4 사용 시작"
  [Hadoop 1.2]: ../hdinsight-get-started-21 "HDInsight에서 Hadoop 1.2 사용 시작"
  [Apache Hadoop]: http://hadoop.apache.org/
  [HDInsight에서 제공하는 클러스터 버전의 새로운 기능]: ../hdinsight-component-versioning/
  [HDInsight Emulator 시작]: ../hdinsight-get-started-emulator/
  [구매 옵션]: http://azure.microsoft.com/en-us/pricing/purchase-options/
  [구성원 제공 항목]: http://azure.microsoft.com/en-us/pricing/member-offers/
  [무료 평가판]: http://azure.microsoft.com/en-us/pricing/free-trial/
  [HDInsight 릴리스 정보]: http://azure.microsoft.com/ko-kr/documentation/articles/hdinsight-release-notes/
  [HDInsight 클러스터 프로비전]: #provision
  [Hive 작업 실행]: #sample
  [Microsoft 비즈니스 인텔리전스 도구에 연결]: #powerquery
  [다음 단계]: #nextsteps
  [HDInsight에서 Azure Blob 저장소 사용]: ../hdinsight-use-blob-storage/
  [1]: ../hdinsight-provision-clusters/
  [Azure 관리 포털]: https://manage.windowsazure.com/
  [HDI.StorageAccount.QuickCreate]: ./media/hdinsight-get-started/HDI.StorageAccount.QuickCreate.png
  [저장소 계정을 만드는 방법]: ../storage-create-storage-account/
  [HDI.ClusterStatus]: ./media/hdinsight-get-started/HDI.ClusterStatus.png
  [HDI.CustomCreateCluster]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
  [hdi.dashboard]: ./media/hdinsight-get-started/HDI.dashboard.png
  [hdi.dashboard.query.select]: ./media/hdinsight-get-started/HDI.dashboard.query.select.png
  [hdi.dashboard.query.select.result]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.png
  [Microsoft 다운로드 센터]: http://www.microsoft.com/en-us/download/details.aspx?id=39379
  [HDI.GettingStarted.PowerQuery.ImportData]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData.png
  [HDI.GettingStarted.PowerQuery.ImportData2]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData2.png
  [PowerShell을 사용하여 HDInsight 관리]: ../hdinsight-administer-use-powershell/
  [HDInsight에 데이터 업로드]: ../hdinsight-upload-data/
  [HDInsight와 함께 MapReduce 사용]: ../hdinsight-use-mapreduce
  [HDInsight에서 Hive 사용]: ../hdinsight-use-hive/
  [HDInsight에서 Pig 사용]: ../hdinsight-use-pig/
  [HDInsight에서 Oozie 사용]: ../hdinsight-use-oozie/
  [HDInsight용 C\# Hadoop 스트리밍 프로그램 개발]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
  [HDInsight용 Java MapReduce 프로그램 개발]: ../hdinsight-develop-deploy-java-mapreduce/
  [img-hdi-getstarted-video]: ./media/hdinsight-get-started/HDI.GetStarted.Video.png