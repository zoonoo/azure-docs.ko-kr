<properties urlDisplayName="Get Started" pageTitle="HDInsight에서 Hive를 사용하여 HBase 테이블 설정 및 쿼리 | Azure" metaKeywords="" description="Get started using HBase with Hadoop in HDInsight. Learn how to create HBase tables and query them using Hive." metaCanonical="" services="hdinsight" documentationCenter="" title="Set up HBase clusters and query them using Hive on Hadoop in HDInsight" authors="bradsev" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/9/2014" ms.author="bradsev" />



# HDInsight의 Hadoop에서 Hive를 사용하여 HBase 클러스터 설정 및 쿼리

HDInsight의 Hadoop에서 Hive를 사용하여 HBase 테이블을 만들고 쿼리하는 방법을 알아봅니다. 

##이 문서에서는 다음을 수행합니다.

* [HBase 정의](#hbaseintro)
* [필수 조건](#prerequisites)
* [Azure 관리 포털을 사용하여 HBase 클러스터 프로비전](#create-hbase-cluster)
* [HBase 셸을 사용하여 HBase 테이블 관리](#create-sample-table)
* [HiveQL을 사용하여 HBase 테이블 쿼리](#hive-query)
* [Microsoft HBase REST 클라이언트 라이브러리를 사용하여 HBase 테이블 관리](#hbase-powershell)
* [참고 항목](#seealso)

##<a name="hbaseintro"></a>HBase 정의

HBase는 빅 데이터를 온라인으로 트랜잭션 처리할 수 있게 하는 짧은 대기 시간의 NoSQL 데이터베이스입니다. HBase는 Azure 환경에 통합된 관리 클러스터로 제공합니다. 이 클러스터는 Azure Blob 저장소에 직접 데이터를 저장하도록 구성되며, 그러면 대기 시간이 짧고 성능/비용 선택 시 탄력성이 높습니다. 따라서 고객은 대규모 데이터 집합으로 작업하는 대화형 웹 사이트를 구축하고, 수백만 개의 끝점에서 발생하는 센서 및 원격 분석 데이터를 저장하는 서비스를 구축하고, Hadoop 작업을 사용해 이 데이터를 분석할 수 있습니다. HBase에 대한 자세한 내용은 HBase를 사용할 수 있는 시나리오에 대해서는 [HDInsight HBase 개요](http://azure.microsoft.com/ko-kr/documentation/articles/hdinsight-hbase-overview/)를 참조하세요.

> [WACOM.NOTE] HBase(버전 0.98.0)는 HDInsight의 HDInsight 3.1 클러스터에서만 사용할 수 있습니다(Apache Hadoop 및 YARN 2.4.0 기준). 버전 정보는 [HDInsight에서 제공하는 Hadoop 클러스터 버전의 새로운 기능][hdinsight-versions](영문)을 참조하세요.

##<a name="prerequisites"></a>필수 조건

이 자습서를 시작하기 전에 다음이 있어야 합니다.

- **Azure 구독** 구독을 얻는 방법에 대한 자세한 내용은 [구매 옵션][azure-purchase-options], [구성원 제공 항목][azure-member-offers] 또는 [무료 평가판][azure-free-trial]을 참조하세요.
- **Azure 저장소 계정** 자세한 내용은 [저장소 계정을 만드는 방법][azure-create-storageaccount]을 참조하세요.
- Visual Studio 2013이 설치된 **워크스테이션.** 관련 지침은 [Visual Studio 설치](http://msdn.microsoft.com/ko-kr/library/e2h7fzkw.aspx)를 참조하세요.
- [Microsoft HBase REST Client Library for .NET]을 다운로드합니다(https://github.com/hdinsight/hbase-sdk-for-net). 


##<a name="create-hbase-cluster"></a>Azure 포털에서 HBase 클러스터 프로비전

이 섹션에서는 Azure 포털을 사용하여 HBase 클러스터를 프로비전하는 방법을 설명합니다.


[WACOM.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]

**Azure 포털에서 HDInsight 클러스터를 프로비전하려면** 


1. [Azure 관리 포털][azure-management-portal]에 로그인합니다. 

2. 왼쪽에 있는 **HDInsight**를 클릭하여 계정에 있는 클러스터의 상태를 나열한 다음 왼쪽 아래에서 **+새로 만들기** 아이콘을 클릭합니다. 

	![Creating an HBase cluster in HDInsight in the Azure portal.](http://i.imgur.com/PmGynKZ.jpg)

3. 왼쪽에서 두 번째 열에서 HDInsight 아이콘을 클릭한 다음 그 다음 열에서 HBase 옵션을 선택합니다. CLUSTERNAME 및 CLUSTER SIZE의 값, 저장소 계정의 이름 및 새 HBase 클러스터의 암호를 지정합니다.
 
	![Choosing and HBase cluster type and entering cluster login credentials.](http://i.imgur.com/ecxbB9K.jpg)

4. 왼쪽 아래에 있는 확인 아이콘을 클릭하여 HBase 클러스터를 만듭니다.


##<a name="create-sample-table"></a>HBase 셸에서 HBase 샘플 테이블 만들기
이 섹션에서는 RDP(원격 데스크톱 프로토콜)를 설정 및 사용하여 HBase 셸에 액세스한 후 이 셸을 사용하여 HBase 샘플 테이블을 만들고 행을 추가한 후 테이블의 행을 나열하는 방법을 설명합니다.

여기서는 첫 번째 섹션에서 설명한 절차를 완료하여 이미 HBase 클러스터를 만들었다고 가정합니다.

**HBase 클러스터에 대한 RDP 연결 사용**

1. HDInsight 클러스터에 원격 데스크톱 연결을 사용하려면 만든 HBase 클러스터를 선택하고 **구성** 탭을 클릭합니다. 페이지 아래에서 **원격 사용** 단추를 클릭하여 클러스터에 RDP 연결을 설정합니다.
2. **원격 데스크톱 구성** 마법사에서 자격 증명과 만료 날짜를 제공하고 오른쪽 아래에서 체크 표시가 된 원을 클릭합니다. (작업이 완료되는 데 몇 분 정도 걸릴 수 있습니다.)
3. HDInsight 클러스터에 연결하려면 **구성** 탭의 맨 아래에서 **연결** 단추를 클릭합니다.

 
**HBase 셸 열기**

1. RDP 세션 내에서 바탕 화면에 있는 **Hadoop 명령 프롬프트** 바로 가기를 클릭합니다.

2. 폴더를 HBase 홈 디렉터리로 변경합니다.
		
		cd %HBASE_HOME%\bin

3. HBase 셸을 엽니다.

		hbase shell


**샘플 테이블 만들기, 데이터 추가 및 데이터 검색**

1. 샘플 테이블을 만듭니다.

		create 'sampletable', 'cf1'

2. 샘플 테이블에 행을 추가합니다.

		put 'sampletable', 'row1', 'cf1:col1', 'value1'

3. 샘플 테이블의 행을 나열합니다.
	
		scan 'sampletable'

##<a name="hive-query"></a>Hive를 사용하여 HBase 테이블 쿼리

HBase 클러스터를 프로비전했고 테이블을 만들었으며, 이제 Hive를 사용하여 쿼리할 수 있습니다. 이 섹션에서는 HBase 테이블에 매핑되는 Hive 테이블을 만들고 이를 사용하여 HBase 테이블의 데이터를 쿼리합니다.

**클러스터 대시보드를 열려면**

1. [Azure 관리 포털][azure-management-portal]에 로그인합니다. 
2. 왼쪽 창에서 **HDINSIGHT**를 클릭합니다. 지난 섹션에서 만든 클러스터를 포함하여 생성된 클러스터의 목록이 표시됩니다.
3. Hive 작업을 실행할 클러스터의 이름을 클릭합니다.
4. 페이지 아래에서 **클러스터 관리**를 클릭하여 클러스터 대시보드를 엽니다. 다른 브라우저 탭에서 웹 페이지가 열립니다.   
5. Hadoop 사용자 계정의 사용자 이름 및 암호를 입력합니다.  기본 사용자 이름은 **admin**이고 암호는 프로비전 프로세스에서 입력한 암호입니다.  대시보드는 다음과 유사하게 표시됩니다.

	![HDInsight cluster dashboard.](http://i.imgur.com/tMwXlj9.jpg)


**Hive 쿼리를 실행하려면**

1. HBase 테이블에 매핑되는 Hive 테이블을 만들려면 다음 HiveQL 스크립트를 Hive 콘솔 창에 입력하고 **전송** 단추를 클릭합니다. 다음 문을 실행하기 전에 HBase 셸을 사용하여 HBase에서 참조되는 샘플 테이블을 만들었는지 확인합니다.
 
    	CREATE EXTERNAL TABLE hbasesampletable(rowkey STRING, col1 STRING, col2 STRING)
    	STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
    	WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,cf1:col1,cf1:col2')
    	TBLPROPERTIES ('hbase.table.name' = 'sampletable');

 
2. HBase 테이블의 데이터에 대해 Hive 쿼리를 실행하려면 다음 HiveQL 스크립트를 Hive 콘솔 창에 입력하고 **전송** 단추를 클릭합니다.

     	SELECT count(*) FROM hbasesampletable;
 
4. Hive 쿼리의 결과를 검색하려면 작업 실행이 완료되면 **작업 세션** 창에서 **자세히 보기** 링크를 클릭합니다.


참고: HBase 셸 링크를 클릭하면 탭이 **HBase 셸**로 전환됩니다.



**출력 파일을 찾아보려면**

1. 클러스터 대시보드의 상단에서 **파일**을 클릭합니다.
2. **Templeton-Job-Status**를 클릭합니다.
3. 마지막으로 수정한 시간이 앞에서 기록한 작업 시작 시간보다 약간 늦은 GUID 번호를 클릭합니다. 이 GUID를 기록해 둡니다.  다음 섹션에서 필요합니다.
4. **stdout** 파일에는 다음 섹션에 필요한 데이터가 있습니다. 원하는 경우 **stdout**를 클릭하여 데이터 파일 복사본을 다운로드합니다.

	
##<a name="hbase-powershell"></a>HBase REST Client Library for .NET C# API를 사용하여 HBase 테이블 만들기 및 테이블에서 데이터 검색

Microsoft HBase REST Client Library for .NET 프로젝트는 GitHub에서 다운로드해야 하며 HBase .NET SDK를 사용하도록 구축한 프로젝트입니다. 다음 절차에는 이 작업에 대한 지침이 있습니다.

1. [Microsoft HBase REST Client Library for .NET]을 다운로드합니다(https://github.com/hdinsight/hbase-sdk-for-net) (이 필수 조건이 아직 충족되지 않은 경우).

2. Visual Studio(**파일** -> **열기** -> **프로젝트/솔루션...**)를 통해 Marlin.sln을 다운로드한 위치에서 엽니다. **보기** -> **솔루션 탐색기**를 선택하여 'Marlin' 솔루션과 해당 Microsoft.HBase.Client 프로젝트를 확인합니다. **솔루션 탐색기**에서 **Marlin** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **솔루션 빌드**를 선택하여 프로젝트를 빌드합니다. 

4. 새 Visual C# 콘솔 응용 프로그램을 만듭니다. ...\bin\debug\Microsoft.HBase.Client 디렉터리에서 빌드된 결과 Microsoft.HBase.Client.dll 및 protobuf.dll을 검색하고 C# 프로젝트에 추가합니다. **참조**를 마우스 오른쪽 단추로 클릭하고 **참조 추가...**를 선택하고 나서 2개 어셈블리로 이동하여 이를 업로드합니다. [protobuf-net](http://code.google.com/p/protobuf-net/) 은 데이터 통신에 사용되는 Google Protocol Buffers 이진 직렬화기의 .NET 구현입니다.

5. 파일 맨 위에 다음 using 문을 추가합니다.
		
		using Microsoft.HBase.Client;
		using org.apache.hadoop.hbase.rest.protobuf.generated;

6. 클러스터 자격 증명을 사용하여 새 HBase 클라이언트 인스턴스를 만들고 클러스터 버전을 검색합니다.

		// Create a new instance of an HBase client.
		var creds = new ClusterCredentials(new Uri("https://myclustername.azurehdinsight.net"), "myusername", "mypassword");
		var client = new HBaseClient(creds);

		// Retrieve the cluster version
		var version = client.GetVersion();
		Console.WriteLine(version);

7. 새 HBase 테이블을 만들려면 다음 코드를 사용합니다.

		// Create a new HBase table.
		var testTableSchema = new TableSchema();
		testTableSchema.name = "mytablename";
		testTableSchema.columns.Add(new ColumnSchema() { name = "d" });
		testTableSchema.columns.Add(new ColumnSchema() { name = "f" });
		client.CreateTable(testTableSchema);

8. 다음 코드를 사용하여 테이블에 데이터를 삽입합니다.

		// Insert data into a table.
		var tableName = "mytablename";
		var testKey = "content";
		var testValue = "the force is strong in this column";
		var set = new CellSet();
		var row = new CellSet.Row { key = Encoding.UTF8.GetBytes(testKey) };
		set.rows.Add(row);

		var value = new Cell { column = Encoding.UTF8.GetBytes("d:starwars"), data = Encoding.UTF8.GetBytes(testValue) };
		row.values.Add(value);
		client.StoreCells(tableName, set);

9. 키를 사용하여 셀을 검색하려면 다음 코드를 사용합니다. 

		// Retrieve a cell with its key.
		var testKey = "content";
		var tableName = "mytablename";

		var cells = client.GetCells(tableName, testKey);
		// get the first value from the row.
		.WriteLine(Encoding.UTF8.GetString(cells.rows[0].values[0].data));
		// with the previous insert, it should yield: "the force is strong in this column"

10. 다음 코드를 사용하여 테이블에서 행을 검색합니다.

		//Scan over rows in a table.
		var creds = new ClusterCredentials(new Uri("https://myclustername.azurehdinsight.net"), "myusername", "mypassword");
		var client = new HBaseClient(creds);

		var tableName = "mytablename";

		// assume the table has integer keys and we want data between keys 25 and 35
		var scanSettings = new Scanner()
		{
    		batch = 10,
    		startRow = BitConverter.GetBytes(25),
    		endRow = BitConverter.GetBytes(35)
		};

		var scannerInfo = client.CreateScanner(tableName, scanSettings);
		CellSet next = null;
		while ((next = client.ScannerGetNext(scannerInfo)) != null)
		{
    		foreach (var row in next.rows)
    		{
        		// ... read the rows
    		}
		}



##<a name="summary"></a>요약
이 자습서에서는 HBase 클러스터를 프로비전하는 방법, 테이블을 만들고 HBase 셸에서 가져온 데이터를 이 테이블에서 보는 방법을 알아보았습니다. 또한 Hive를 사용하여 HBase 테이블의 데이터를 쿼리하는 방법 및 HBase C# API를 사용하여 HBase 테이블을 만들고 이 테이블에서 데이터를 검색하는 방법도 알아보았습니다.

##<a name="next"></a> 다음 작업

[HDInsight HBase 개요][hdinsight-hbase-overview]:
HBase는 구조화되지 않은/반구조화된 대량 데이터에 대해 임의 액세스 및 강력한 일관성을 제공하는 Hadoop 기반의 Apache 오픈 소스 NoSQL 데이터베이스입니다.

[Azure 가상 네트워크에 HBase 클러스터 프로비전][hdinsight-hbase-provision-vnet]:
가상 네트워크 통합을 사용하면 응용 프로그램이 HBase와 직접 통신할 수 있도록 응용 프로그램과 동일한 가상 네트워크에 HBase 클러스터를 배포할 수 있습니다.

[HDInsight에서 HBase를 사용하여 Twitter 데이터 분석][hbase-twitter-sentiment]:
HDInsight의 Hadoop에서 HBase를 사용하여 빅 데이터의 실시간 [감정 분석](http://en.wikipedia.org/wiki/Sentiment_analysis) 을 수행하는 방법을 알아봅니다.

[hdinsight-hbase-overview]: ../hdinsight-hbase-overview/
[hdinsight-hbase-provision-vnet]: ../hdinsight-hbase-provision-vnet
[hdinsight-versions]: ../hdinsight-component-versioning/

[hdinsight-get-started-30]: ../hdinsight-get-started-30/

[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/

[hbase-twitter-sentiment]: ../hdinsight-hbase-analyze-twitter-sentiment/

[hdinsight-use-hive]: ../hdinsight-use-hive/

[hdinsight-storage]: ../hdinsight-use-blob-storage/



[azure-purchase-options]: http://azure.microsoft.com/ko-kr/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/ko-kr/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/ko-kr/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/ko-kr/documentation/articles/storage-create-storage-account/ 

[apache-hadoop]: http://hadoop.apache.org/

[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: ../install-configure-powershell/
[powershell-open]: ../install-configure-powershell/#Install


[img-hdi-dashboard]: ./media/hdinsight-get-started/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-get-started/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.png







<!--HONumber=35_1-->
