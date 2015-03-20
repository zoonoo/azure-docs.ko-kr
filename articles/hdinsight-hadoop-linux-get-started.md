<properties 
   pageTitle="Linux의 HDInsight에서 Hive와 Hadoop 사용 시작|  Azure" 
   description="Linux에서 HDInsight의 Hadoop 사용 시작 Linux에서 실행하는 HDInsight Hadoop 클러스터를 프로비전하고 Hive를 사용하여 데이터를 쿼리하는 방법을 알아봅니다." 
   services="hdinsight" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="02/18/2015"
   ms.author="nitinme"/>

# Linux(미리 보기)의 HDInsight 에서 Hive와 Hadoop 사용 시작

이 자습서는 Linux에서 HDInsight Hadoop 클러스터를 프로비전하고 구조화 되지 않은 데이터에서 의미 있는 정보를 추출할 Hive 쿼리를 실행하는 방법을 표시하여 Linux의 HDInsight 에서 신속하게 시작할 수 있도록 해줍니다. 그런 다음 Tableau와 같은 BI (비즈니스 인텔리전스) 도구 내에서 결과를 분석할 수 있습니다.


> [AZURE.NOTE] Hadoop과 빅데이터를 처음 사용하는 경우 <a href="http://go.microsoft.com/fwlink/?LinkId=510084" target="_blank">Apache Hadoop</a>, <a href="http://go.microsoft.com/fwlink/?LinkId=510086" target="_blank">MapReduce</a>, <a href="http://go.microsoft.com/fwlink/?LinkId=510087" target="_blank">HDFS</a> 및 <a href="http://go.microsoft.com/fwlink/?LinkId=510085" target="_blank">Hive</a> 용어에 대해 자세히 알아볼 수 있습니다. HDInsight를 통해 Azure에서 Hadoop을 사용하도록 설정하는 방법을 이해하려면[HDInsight의 Hadoop 소개](../hdinsight-hadoop-introduction/)(영문)를 참조하세요.


## 이 자습서의 목적 ##

구조화되지 않은 대량의 데이터 집합이 있고 쿼리를 실행하여 의미 있는 일부 정보를 추출하려고 한다면 해당 방법은 다음과 같습니다.

   !["Get started using Hadoop with Hive in HDInsight" tutorial steps illustrated: create an account; provision a cluster; query data; and analyze in Tableau.](./media/hdinsight-hadoop-linux-get-started/HDI.Linux.GetStartedFlow.png)


**필수 조건:**

이 자습서를 시작하기 전에 다음이 있어야 합니다.


- Azure 구독. 구독을 예약하는 방법에 대한 자세한 내용은 <a href="http://azure.microsoft.com/pricing/purchase-options/" target="_blank">구매 옵션</a>, <a href="http://azure.microsoft.com/pricing/member-offers/" target="_blank">구성원 제공 항목</a> 또는 <a href="http://azure.microsoft.com/pricing/free-trial/" target="_blank">무료 평가판</a>을 참조하세요.

**완료 예상 시간:** 30분

## 자습서 내용

* [Azure 저장소 계정 만들기](#storage)
* [HDInsight 클러스터 프로비전](#provision)
* [Hive 작업 제출](#hivequery)
* [다음 단계](#nextsteps)

## <a name="storage"></a>Azure 저장소 계정 만들기

HDInsight는 데이터 저장을 위해 Azure Blob 저장소를 사용합니다. 이를  *WASB* 또는 *Azure 저장소 - Blob*이라고 합니다. WASB는 Azure Blob 저장소에 구현한 Microsoft의 HDFS입니다. 자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용](../hdinsight-use-blob-storage/)을 참조하세요.

HDInsight 클러스터를 프로비전할 때 Azure 저장소 계정을 지정합니다. HDFS의 경우처럼 해당 계정의 특정 Blob 저장소 컨테이너는 기본 파일 시스템으로 지정됩니다. HDInsight 클러스터는 기본적으로 사용자가 지정한 저장소 계정과 동일한 데이터 센터에 프로비전됩니다.

사용자 지정 방식으로 HDInsight 클러스터를 구성할 때는 이 저장소 계정 외에 다른 저장소 계정을 추가할 수 있습니다. 이 추가 저장소 계정은 동일한 Azure 구독에서 가져오거나 다른 Azure 구독에서 가져올 수 있습니다. 자세한 내용은 [사용자 지정 옵션을 사용하여 HDInsight 클러스터 프로비전](../hdinsight-hadoop-provision-linux-clusters)을 참조하세요. 

이 자습서와 과정을 간소화하기 위해 기본 Blob 컨테이너와 기본 저장소 계정만 사용됩니다. 실제로는 데이터 파일이 보통 지정된 저장소 계정에 저장됩니다.

**Azure 저장소 계정을 만들려면**

1. <a href="https://manage.windowsazure.com/" target="_blank">Azure 관리 포털</a>에 로그인합니다.
2. 왼쪽 아래에서 **새로 만들기**를 클릭하고 **데이터 서비스**, **저장소**를 차례로 가리킨 후 **빠른 생성**을 클릭합니다.

	![Azure portal where you can use Quick Create to set up a new storage account.](./media/hdinsight-hadoop-linux-get-started/HDI.StorageAccount.QuickCreate.png)

3. **URL**, **위치** 및 **복제**를 입력하고 **저장소 계정 만들기**를 클릭합니다. 선호도 그룹은 지원되지 않습니다. 저장소 목록에 새 저장소 계정이 표시됩니다.

	>[AZURE.NOTE]  이 자습서에서 사용하게 되는 것과 같은 HDInsight Linux 클러스터를 프로비전하기 위한 빠른 생성 옵션은 클러스터를 프로비전할 때 위치를 묻지 않습니다. 기본적으로 저장소 계정이 있는 동일한 데이터 센터에 클러스터를 배치합니다. 따라서 클러스터에 대해 지원되는 위치인  **동아시아**, **동남 아시아**, **북유럽**, **서유럽**, **미국 동부**, **미국 서부**, **미국 중북부**, **미국 남중부**에 저장소 계정을 만들어야 합니다.

4. 새 저장소 계정의 **상태**가 **온라인**으로 변경될 때까지 기다립니다.
5. 목록에서 새 저장소 계정을 선택하고 페이지 아래쪽에서 **액세스 키 관리**를 클릭합니다.
7. **저장소 계정 이름** 및 **기본 액세스 키**(또는 **보조 액세스 키**)를 적어 둡니다. 키 중 하나가 작동합니다.  이 정보는 자습서의 뒷부분에서 필요합니다.


자세한 내용은
[저장소 계정을 만드는 방법](../storage-create-storage-account/) 및 [HDInsight에서 Azure Blob 저장소 사용](../hdinsight-use-blob-storage/)을 참조하세요.
	
## <a name="provision"></a>Linux에서 HDInsight 클러스터 프로비전

HDInsight 클러스터를 프로비전할 때는 Hadoop과 관련 응용 프로그램을 포함하는 Azure 계산 리소스를 프로비전합니다. 이 섹션에서는 quick-create 옵션을 사용하여 Linux에서 HDInsight 클러스터를 프로비전합니다. 이 옵션 기본 사용자 이름 및 Azure 저장소 컨테이너를 사용 하 여 HDInsight 버전 3.2 (Hadoop 버전 2.5, HDP 버전 2.2)는 클러스터를 구성하고 Ubuntu 12.04 LTS를 실행 합니다. 여러 다른 HDInsight 버전 및 해당 SLA에 대한 [HDInsight 구성 요소 버전 관리](http://azure.microsoft.com/documentation/articles/hdinsight-component-versioning/) 페이지를 참조하세요.

>[AZURE.NOTE]  Windows Server OS를 실행하는 Hadoop 클러스터를 만들 수 있습니다. 자세한 지침은 [Windows에서 HDInsight 사용 시작](../hdinsight-get-started/)을 참조하세요.


**HDInsight 클러스터를 프로비전하려면**

1. <a href="https://manage.windowsazure.com/" target="_blank">Azure 관리 포털</a>에 로그인합니다. 

2. 왼쪽 아래에서 **신규**를 클릭하고, **데이터 서비스**를 클릭하고, **HDInsight**를 클릭한 다음, **Linux의 Hadoop**을 클릭합니다.

	![Creation of a Hadoop cluster in HDInsight.](./media/hdinsight-hadoop-linux-get-started/HDI.QuickCreateCluster.png)

4. 다음 값을 입력하거나 선택합니다.

	<table border="1">
	<tr><th>이름</th><th>값</th></tr>
	<tr><td>클러스터 이름</td><td>클러스터의 이름입니다.</td></tr>
	<tr><td>클러스터 크기</td><td>배포하려는 데이터 노드 수입니다. 기본값은 4입니다. 그러나 드롭다운 메뉴에서 1 또는 2개의 데이터 노드를 사용하는 옵션을 사용할 수도 있습니다. <strong>사용자 지정 만들기</strong> 옵션을 사용하면 클러스터 노드 수를 임의로 지정할 수 있습니다. 다양한 클러스터 크기의 청구 요금에 대한 가격 책정 정보를 사용할 수 있습니다. 드롭다운 상자 바로 위의 <strong>?</strong> 기호를 클릭하고 팝업의 링크를 따르세요.</td></tr>
	<tr><td>암호</td><td><i>HTTP</i> 계정(기본 사용자 이름: admin) 및 <i>SSH</i> 계정(기본 사용자 이름: hdiuser)에 대한 암호입니다. 이 계정은 클러스터가 프로비전되는 VM에 대한 Windows 관리자 계정이 아닙니다. </td></tr>
	
	<tr><td>저장소 계정</td><td>드롭다운 상자에서 직접 만든 저장소 계정을 선택합니다. <br/>

	저장소 계정을 선택한 후에는 변경할 수 없습니다. 저장소 계정을 제거하면 더 이상 클러스터를 사용할 수 없습니다.

	HDInsight 클러스터는 저장소 계정이 있는 동일한 데이터 센터에 배치됩니다. 
	</td></tr>
	</table>

	클러스터 이름을 복사해 둡니다. 이 이름은 자습서의 뒷부분에서 필요합니다.

	
5. **HDInsight 클러스터 만들기**를 클릭합니다. 프로비전이 완료되면 상태 열에 **실행 중**이 표시됩니다.

	>[AZURE.NOTE] 위 절차가 기본 SSH 사용자 이름 및 Azure 저장소 컨테이너를 사용하여 quick-create 옵션으로 Linux 클러스터를 만듭니다. 인증에서 SSH 키를 사용하거나 추가 저장소 계정을 사용하는 등의 사용자 지정 옵션으로 클러스터를 만들려면 [사용자 지정 옵션을 사용하여 HDInsight Linux 클러스터 프로비전](../hdinsight-hadoop-provision-linux-clusters)을 참조하세요.


## <a name="hivequery"></a>클러스터에서 Hive 작업 제출
HDInsight Linux 클러스터를 프로비전했으므로 다음 단계는 Hive 작업을 실행하여 HDInsight 클러스터와 함께 제공되는 샘플 데이터(sample.log)를 쿼리하는 것입니다. 예제 데이터는 추적, 경고, 정보, 오류 등을 포함한 로그 정보를 포함합니다. 우리는 특정 심각도를 사용하여 모든 오류 로그를 검색하기 위해 이 데이터를 쿼리합니다. HDInsight Linux 클러스터에서 Hive 쿼리를 실행하려면 다음 단계를 수행해야 합니다.

- Linux 클러스터에 연결합니다.
- Hive 작업 실행



### 클러스터에 연결하려면

Linux 컴퓨터 또는 SSH를 사용하는 Windows 컴퓨터에서 Linux의 HDInsight 클러스터에 연결할 수 있습니다.

**Linux 컴퓨터에 연결하려면**

1. 터미널을 열고 다음 명령을 실행합니다.

		ssh <username>@<clustername>-ssh.azurehdinsight.net

	빠른 생성 옵션을 사용하여 클러스터를 프로비전하기 때문에 기본 SSH 사용자 이름은 **hdiuser**입니다. 따라서 명령이어야 합니다.

		ssh hdiuser@myhdinsightcluster-ssh.azurehdinsight.net

2. 메시지가 표시되면 클러스트를 프로비전할 때 제공되는 암호를 입력합니다. 성공적으로 연결되면 프로프트가 다음과 같이 변경됩니다.

		hdiuser@headnode-0:~$


**Windows 컴퓨터에서 연결하려면**

1. Windows 클라이언트용 **PuTTY**를 다운로드합니다. <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html</a>에서 사용할 수 있습니다.

2. **PuTTY**를 엽니다. **범주**에서 **세션**을 클릭합니다. **PuTTY 세션에 대한 기본 옵션** 화면에서, **호스트 이름(또는 IP 주소)** 필드에 HDInsight 서버의 SSH 주소를 입력합니다. SSH 주소는 **-ssh.azurehdinsight.net** 뒤의 클러스터 이름입니다. 예를 들면, **myhdinsightcluster-ssh.azurehdinsight.net**입니다.

	![Connect to an HDInsight cluster on Linux using PuTTY](./media/hdinsight-hadoop-linux-get-started/HDI.linux.connect.putty.png)

3. 나중에 사용할 연결 정보를 저장하려면 **저장 세션** 아래 이 연결을 위한 이름을 입력한 후 **저장**을 클릭합니다. 연결이 저장 세션의 목록에 추가됩니다.

4. **열기**를 클릭하여 클러스터에 연결합니다. 사용자 이름에 대한 메시지가 표시되면 *hdiuser*를 입력합니다. 암호는 클러스터를 프로비전하는 동안 지정한 암호를 입력합니다. 성공적으로 연결되면 프로프트가 다음과 같이 변경됩니다.

		hdiuser@headnode-0:~$

### Hive 작업을 실행하려면

SSH를 사용하여 클러스터에 연결되면 다음 명령을 사용하여 Hive 쿼리를 실행합니다.

1. 프롬프트에서 다음 명령을 사용하여 Hive 명령줄 인터페이스(CLI)를 시작합니다.

		hive

2. CLI를 사용하여 이미 클러스터에서 사용할 수 있는 샘플 데이터를 사용하여 log4jLogs라는 새 테이블을 만들려면 다음 문을 입력합니다.

		DROP TABLE log4jLogs;
		CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) 
		ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' 
		STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
		SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;

	이러한 문은 다음 작업을 수행합니다.

	- **DROP TABLE** - 테이블이 이미 있는 경우에 테이블 및 데이터 파일을 삭제합니다.
	- **CREATE EXTERNAL TABLE** - Hive에서 새 '외부' 테이블을 만듭니다. 외부 테이블은 Hive에 테이블 정의만 저장하고, 데이터는 원래 위치에 남아 있습니다.
	- **ROW FORMAT** - 데이터의 형식 지정 방식을 Hive에 알립니다. 이 경우, 각 로그의 필드는 공백으로 구분됩니다.
	- **STORED AS TEXTFILE LOCATION** - 데이터가 저장된 위치와 텍스트로 저장되었다는 것을 Hive에 알립니다.
	- **SELECT** - t4 열에 [ERROR] 값이 포함된 모든 행의 수를 선택합니다.

	>[WACOM.NOTE] 자동화된 데이터 업로드 프로세스와 같은 외부 원본이나 또 다른 MapReduce 작업을 통해 기본 데이터를 업데이트해야 하지만 Hive 쿼리에서 항상 최신 데이터를 사용하려고 할 경우 외부 테이블을 사용해야 합니다. 외부 테이블을 삭제하면 데이터**뿐만 아니라** 테이블 정의도 삭제됩니다.

	다음 출력을 반환합니다.

		Query ID = hdiuser_20150116000202_cceb9c6b-4356-4931-b9a7-2c373ebba493
		Total jobs = 1
		Launching Job 1 out of 1
		Number of reduce tasks not specified. Estimated from input data size: 1
		In order to change the average load for a reducer (in bytes):
		  set hive.exec.reducers.bytes.per.reducer=<number>
		In order to limit the maximum number of reducers:
		  set hive.exec.reducers.max=<number>
		In order to set a constant number of reducers:
		  set mapreduce.job.reduces=<number>
		Starting Job = job_1421200049012_0006, Tracking URL = <URL>:8088/proxy/application_1421200049012_0006/
		Kill Command = /usr/hdp/2.2.1.0-2165/hadoop/bin/hadoop job  -kill job_1421200049012_0006
		Hadoop job information for Stage-1: number of mappers: 1; number of reducers: 1
		2015-01-16 00:02:40,823 Stage-1 map = 0%,  reduce = 0%
		2015-01-16 00:02:55,488 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 3.32 sec
		2015-01-16 00:03:05,298 Stage-1 map = 100%,  reduce = 100%, Cumulative CPU 5.62 sec
		MapReduce Total cumulative CPU time: 5 seconds 620 msec
		Ended Job = job_1421200049012_0006
		MapReduce Jobs Launched: 
		Stage-Stage-1: Map: 1  Reduce: 1   Cumulative CPU: 5.62 sec   HDFS Read: 0 HDFS Write: 0 SUCCESS
		Total MapReduce CPU Time Spent: 5 seconds 620 msec
		OK
		[ERROR]    3
		Time taken: 60.991 seconds, Fetched: 1 row(s)

	이 값이 포함된 3개 행이 있으므로 출력은 **[ERROR]  3**을 포함합니다.

3. **errorLogs**라는 새 "내부" 테이블을 만들려면 다음 문을 사용합니다.

		CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
		INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';


	이러한 문은 다음 작업을 수행합니다.

	- **CREATE TABLE IF NOT EXISTS** - 테이블을 만듭니다(아직 없는 경우). EXTERNAL 키워드가 사용되지 않으면 Hive 데이터 웨어하우스에 저장되고 Hive에서 완전히 관리되는 '내부' 테이블입니다. **EXTERNAL** 테이블과 달리 내부 테이블을 삭제하면 기본 데이터도 삭제됩니다.
	- **STORED AS ORC** - 데이터를 ORC(Optimized Row Columnar) 형식으로 저장합니다. Hive 데이터를 저장하기 위한 고도로 최적화되고 효율적인 형식입니다.
	- **INSERT OVERWRITE ... SELECT** - [ERROR]가 포함된 **log4jLogs** 테이블에서 행을 선택하고 데이터를 **errorLogs**테이블에 삽입합니다.

4. t4 열에 **[ERROR]**가 포함된 행만 **errorLogs** 테이블에 저장되었는지 확인하려면 다음 문을 사용하여 errorLogs의 모든 행을 반환합니다.

		SELECT * from errorLogs;

	The following output should be displayed on the console.

		2012-02-03	18:35:34	SampleClass0	[ERROR]	 incorrect		id
		2012-02-03	18:55:54	SampleClass1	[ERROR]	 incorrect		id
		2012-02-03	19:25:27	SampleClass4	[ERROR]	 incorrect		id
		Time taken: 0.987 seconds, Fetched: 3 row(s)

	The returned data should all correspond to [ERROR] logs.


## <a name="nextsteps"></a>다음 단계
이 자습서에서 HDInsight를 사용하여 Hadoop Linux 클러스터를 프로비전하는 방법과 SSH를 사용하여 Hive 쿼리를 실행하는 방법을 배웠습니다. 자세한 내용은 다음 문서를 참조하세요.

- [사용자 지정 옵션을 사용하여 Linux에서 HDInsight 프로비전](../hdinsight-hadoop-provision-linux-clusters)
- [Linux에서 HDInsight 작업](../hdinsight-hadoop-linux-information)
- [Ambari를 사용하여 HDInsight 클러스터 관리](../hdinsight-hadoop-manage-ambari)
- [HDInsight에서 MapReduce 사용][hdinsight-use-mapreduce]
- [HDInsight에서 Hive 사용][hdinsight-use-hive]
- [HDInsight에서 Pig 사용][hdinsight-use-pig]
- [HDInsight에서 Azure Blob 저장소 사용](../hdinsight-use-blob-storage)
- [HDInsight에 데이터 업로드][hdinsight-upload-data]


[1]: ../hdinsight-hadoop-visual-studio-tools-get-started/

[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-use-mapreduce]: ../hdinsight-use-mapreduce/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/

[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: ../install-configure-powershell/
[powershell-open]: ../install-configure-powershell/#Install

[img-hdi-dashboard]: ./media/hdinsight-get-started/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-get-started/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.png
[img-hdi-dashboard-query-select-result-output]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.output.png
[img-hdi-dashboard-query-browse-output]: ./media/hdinsight-get-started/HDI.dashboard.query.browse.output.png
[image-hdi-clusterstatus]: ./media/hdinsight-get-started/HDI.ClusterStatus.png
[image-hdi-gettingstarted-powerquery-importdata]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData.png
[image-hdi-gettingstarted-powerquery-importdata2]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData2.png
<!--HONumber=47-->
