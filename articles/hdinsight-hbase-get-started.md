<properties
	pageTitle="HDInsight에서 Hive를 사용하여 HBase 테이블 설정 및 쿼리 | Azure"
	description="HDInsight의 Hadoop과 함께 HBase를 사용합니다. HBase 테이블을 만들고 Hive를 사용하여 쿼리하는 방법에 대해 알아봅니다."
	services="hdinsight"
	documentationCenter=""
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/09/2015"
	ms.author="jgao"/>



# HDInsight에서 Apache HBase 시작

HDInsight에서 Hive를 사용하여 HBase 테이블을 만들고 HBase 테이블을 쿼리하는 방법에 대해 알아봅니다.

HBase는 빅데이터를 온라인으로 트랜잭션 처리할 수 있게 하는 짧은 대기 시간의 NoSQL 데이터베이스입니다. HBase는 Azure 환경에 통합된 관리 클러스터로 제공합니다. 이 클러스터는 Azure Blob 저장소에 직접 데이터를 저장하도록 구성되며, 그러면 대기 시간이 짧고 성능 및 비용 선택 시 탄력성이 높습니다. 따라서 고객은 대규모 데이터 집합으로 작업하는 대화형 웹 사이트를 구축하고, 수백만 개의 끝점에서 발생하는 센서 및 원격 분석 데이터를 저장하는 서비스를 구축하고, Hadoop 작업을 사용해 이 데이터를 분석할 수 있습니다. HBase에 대한 자세한 내용과 HBase를 사용할 수 있는 시나리오에 가 필요하면 [HDInsight HBase 개요][hdinsight-hbase-overview]를 참조하세요.

> [AZURE.NOTE]HBase(버전 0.98.0)는 HDInsight의 HDInsight 3.1 클러스터에서만 사용할 수 있습니다(Apache Hadoop 및 YARN 2.4.0 기준). 버전 정보는 [HDInsight에서 제공하는 Hadoop 클러스터 버전의 새로운 기능][hdinsight-versions](영문)을 참조하세요.

## 필수 조건

이 자습서를 시작하기 전에 다음이 있어야 합니다.

- **Azure 구독**: 구독을 얻는 방법에 대한 자세한 내용은 [구매 옵션][azure-purchase-options], [구성원 제공 항목][azure-member-offers] 또는 [무료 평가판][azure-free-trial]을 참조하세요.
- **Azure 저장소 계정**: 자세한 내용은 [저장소 계정을 만드는 방법][azure-create-storageaccount]을 참조하세요.
- Visual Studio 2013이 설치된 **워크스테이션**: 지침은 [Visual Studio 설치](http://msdn.microsoft.com/library/e2h7fzkw.aspx)를 참조하세요.

## HBase 클러스터 프로비저닝

[AZURE.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]

**Azure 포털을 사용하여 HBase 클러스터를 프로비전하려면**


1. [Azure 포털][azure-management-portal]에 로그인합니다.
2. 왼쪽 아래에서 **새로 만들기**를 클릭한 후 **데이터 서비스** > **HDINSIGHT** > **HBASE**를 클릭합니다.

	사용자 지정 만들기 옵션을 사용할 수도 있습니다.
3. **클러스터 이름**, **클러스터 크기**, 클러스터 사용자 암호 및 **저장소 계정**을 입력합니다.

	![HBase 클러스터 유형을 선택하고 클러스터 로그인 자격 증명 입력][img-hdinsight-hbase-cluster-quick-create]

	기본 HTTP 사용자 이름은 admin입니다. 사용자 지정 만들기 옵션을 사용하여 이름을 사용자 지정할 수 있습니다.

	> [AZURE.WARNING]HBase 서비스의 고가용성을 위해 **3개** 이상의 노드가 포함된 클러스터를 프로비전해야 합니다. 이렇게 하면 하나의 노드가 작동이 중지된 경우 다른 노드에서 HBase 데이터 영역을 사용할 수 있습니다.

4. 오른쪽 아래에 있는 확인 표시 아이콘을 클릭하여 HBase 클러스터를 만듭니다.

>[AZURE.NOTE]HBase 클러스터를 삭제한 후에는 동일한 기본 Blob을 사용하여 다른 HBase 클러스터를 만들 수 있습니다. 새 클러스터에서는 원래 클러스터에서 만든 HBase 테이블을 선택합니다.

## HBase 셸에서 HBase 샘플 테이블 만들기
이 섹션에서는 HBase 셸을 사용하여 HBase 테이블을 만들고, 행을 추가하고, 행을 나열하는 방법을 설명합니다. HBase 셸에 액세스하려면 먼저 RDP(원격 데스크톱 프로토콜)를 사용하도록 설정한 다음 HBase 클러스터에 대한 RDP 연결을 설정해야 합니다. 자세한 내용은 [Azure 포털을 사용하여 HDInsight의 Hadoop 클러스터 관리][hdinsight-manage-portal]를 참조하세요.


**HBase 셸을 사용하려면**

1. RDP 세션 내에서 바탕 화면에 있는 **Hadoop 명령줄** 바로 가기를 클릭합니다.
2. 폴더를 HBase 홈 디렉터리로 변경합니다.

		cd %HBASE_HOME%\bin
3. HBase 셸을 엽니다.

		hbase shell

4. 하나의 열 패밀리가 있는 HBase를 만들고 행을 삽입합니다.

		create 'sampletable', 'cf1'
		put 'sampletable', 'row1', 'cf1:col1', 'value1'
		scan 'sampletable'

	Hbase 테이블 스키마에 대한 자세한 내용은 [HBase 스키마 디자인 소개][hbase-schema]를 참조하세요. HBase 명령에 대한 자세한 내용은 [Apache HBase 참조 가이드][hbase-quick-start]를 참조하세요.
5. HBase 테이블을 나열합니다.

		list

**HBase WebUI에서 클러스터 상태 확인**

HBase에는 클러스터를 모니터링하는 데 사용할 수 있는 WebUI도 제공됩니다. 예를 들어 지역에 대한 정보 또는 통계를 요청할 수 있습니다. HBase 클러스터의 zookeepernode 주소 아래에서 WebUI를 찾을 수 있습니다.


	http://zookeepernode:60010/master-status

고가용성 클러스터에서는 WebUI를 호스트하는 현재 활성 상태인 HBase 마스터 노드에 대한 링크를 찾을 수 있습니다.

**샘플 테이블 대량 로드**

1. HBase 셸에서 두 개의 열 패밀리가 있는 HBase 테이블을 만듭니다.

		create 'Contacts', 'Personal', 'Office'


3. 다음 데이터가 포함된 연락처 목록을 만들어 Azure Blob 저장소의 /tmp/contacts.txt 폴더에 업로드합니다. 지침은 [HDInsight에서 Hadoop 작업에 대한 데이터 업로드][hdinsight-upload-data]를 참조하세요.

		8396	Calvin Raji	230-555-0191	5415 San Gabriel Dr.
		16600	Karen Wu	646-555-0113	9265 La Paz
		4324	Karl Xie	508-555-0163	4912 La Vuelta
		16891	Jonathan Jackson	674-555-0110	40 Ellis St.
		3273	Miguel Miller	397-555-0155	6696 Anchor Drive
		3588	Osarumwense Agbonile	592-555-0152	1873 Lion Circle
		10272	Julia Lee	870-555-0110	3148 Rose Street
		4868	Jose Hayes	599-555-0171	793 Crawford Street
		4761	Caleb Alexander	670-555-0141	4775 Kentucky Dr.
		16443	Terry Chander	998-555-0171	771 Northridge Drive

2. Hadoop 명령줄에서 폴더를 HBase 홈 디렉터리로 변경합니다.

		cd %HBASE_HOME%\bin

3. ImportTsv를 실행합니다. ImportTsv는 TSV 형식의 데이터를 HBase로 로드하는 도구입니다. 두 가지 고유한 용도가 있습니다. 즉, HDFS(Hadoop Distributed File System)에서 HBase로 TSV 형식의 데이터를 로드하고, 로드할 파일을 준비합니다. 자세한 내용은 [Apache HBase 참조 가이드][hbase-reference]를 참조하세요.

		hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name, Personal:HomePhone, Office:Address" -Dimporttsv.bulk.output=/tmpOutput Contacts /tmp/contacts.txt

4. 이전 명령의 출력을 HBase에 로드합니다.

		hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /tmpOutput Contacts

## Hive를 사용하여 HBase 테이블 쿼리

HBase 클러스터를 프로비전하고 HBase 테이블을 만들었으므로 이제 Hive를 사용하여 테이블의 데이터를 쿼리할 수 있습니다. 이 섹션에서는 HBase 테이블에 매핑되는 Hive 테이블을 만들고 이를 사용하여 HBase 테이블의 데이터를 쿼리합니다.

**클러스터 대시보드를 열려면**

1. [Azure 포털][azure-management-portal]에 로그인합니다.
2. 왼쪽 창에서 **HDINSIGHT**를 클릭합니다. 마지막 섹션에서 만든 클러스터를 포함하여 클러스터 목록이 표시됩니다.
3. Hive 작업을 실행할 클러스터의 이름을 클릭합니다.
4. 페이지 아래쪽에서 **쿼리 콘솔**을 클릭하여 클러스터 대시보드를 엽니다. 웹 페이지가 다른 브라우저 탭에서 열립니다.
5. Hadoop 사용자 계정의 사용자 이름 및 암호를 입력합니다. 기본 사용자 이름은 **admin**이고 암호는 프로비전 프로세스에서 입력한 암호입니다. 새 브라우저 탭이 열립니다.
6. 페이지 위쪽에서 **Hive 편집기**를 클릭합니다. Hive 편집기는 다음과 같습니다.

	![HDInsight 클러스터 대시보드][img-hdinsight-hbase-hive-editor]





























**Hive 쿼리를 실행하려면**

1. 다음 HiveQL 스크립트를 Hive 편집기에 입력하고 **제출**을 클릭하여 HBase 테이블에 매핑되는 Hive 테이블을 만듭니다. 이 문을 실행하기 전에 HBase 셸을 사용하여 이 자습서의 앞에서 참조한 샘플 테이블을 만들었는지 확인합니다.

		CREATE EXTERNAL TABLE hbasecontactstable(rowkey STRING, name STRING, homephone STRING, office STRING)
		STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
		WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:HomePhone,Office:Address')
		TBLPROPERTIES ('hbase.table.name' = 'Contacts');

	**상태**가 **완료**로 업데이트될 때까지 기다립니다.

2. Hive 편집기에 다음 HiveQL 스크립트를 입력한 다음 **제출**을 클릭합니다. 하이브 쿼리는 HBase 테이블에 있는 데이터를 쿼리합니다.

     	SELECT count(*) FROM hbasecontactstable;

4. Hive 쿼리 결과를 검색하려면 작업 실행이 완료된 후 **작업 세션** 창에서 **세부 정보 보기** 링크를 클릭합니다. HBase 테이블에 하나의 레코드만 입력했기 때문에 하나의 작업 출력 파일만 있습니다.




**출력 파일을 찾아보려면**

1. 쿼리 콘솔에서 **파일 브라우저**를 클릭합니다.
2. HBase 클러스터에 대한 기본 파일 시스템으로 사용되는 Azure 저장소 계정을 클릭합니다.
3. HBase 클러스터 이름을 클릭합니다. 기본 Azure 저장소 계정 컨테이너에서 클러스터 이름을 사용합니다.
4. **사용자**를 클릭한 다음 **Admin**을 클릭합니다. 이는 Hadoop 사용자 이름입니다.
6. SELECT Hive 쿼리가 실행된 시간과 일치하는 **마지막 수정** 시간의 작업 이름을 클릭합니다.
4. **stdout**를 클릭합니다. 파일을 저장하고 이 파일을 메모장으로 엽니다. 하나의 출력 파일이 있습니다.

	![HDInsight HBase Hive 편집기 파일 브라우저][img-hdinsight-hbase-file-browser]

## HBase REST API Client Library for .NET C#을 사용하여 HBase 테이블 만들기 및 테이블에서 데이터 검색

GitHub에서 HBase REST API Client Library for .NET을 다운로드하고 HBase .NET SDK를 사용할 수 있도록 프로젝트를 빌드해야 합니다. 다음 절차에는 이 작업에 대한 지침이 있습니다.

1. 새 C# Visual Studio Windows 데스크톱 콘솔 응용 프로그램을 만듭니다.
2. **도구** 메뉴 > **NuGet 패키지 관리자** > **패키지 관리자 콘솔**을 클릭하여 NuGet 패키지 관리자 콘솔을 엽니다.
3. 콘솔에서 다음 NuGet 명령을 실행합니다.

		Install-Package Microsoft.HBase.Client

5. 파일 맨 위에 다음 **using** 문을 추가합니다.

		using Microsoft.HBase.Client;
		using org.apache.hadoop.hbase.rest.protobuf.generated;

6. **Main** 함수를 다음으로 바꿉니다.

        static void Main(string[] args)
        {
            string clusterURL = "https://<yourHBaseClusterName>.azurehdinsight.net";
            string hadoopUsername= "<yourHadoopUsername>";
            string hadoopUserPassword = "<yourHadoopUserPassword>";

            string hbaseTableName = "sampleHbaseTable";

            // Create a new instance of an HBase client.
            ClusterCredentials creds = new ClusterCredentials(new Uri(clusterURL), hadoopUsername, hadoopUserPassword);
            HBaseClient hbaseClient = new HBaseClient(creds);

            // Retrieve the cluster version
            var version = hbaseClient.GetVersion();
            Console.WriteLine("The HBase cluster version is " + version);

            // Create a new HBase table.
            TableSchema testTableSchema = new TableSchema();
            testTableSchema.name = hbaseTableName;
            testTableSchema.columns.Add(new ColumnSchema() { name = "d" });
            testTableSchema.columns.Add(new ColumnSchema() { name = "f" });
            hbaseClient.CreateTable(testTableSchema);

            // Insert data into the HBase table.
            string testKey = "content";
            string testValue = "the force is strong in this column";
            CellSet cellSet = new CellSet();
            CellSet.Row cellSetRow = new CellSet.Row { key = Encoding.UTF8.GetBytes(testKey) };
            cellSet.rows.Add(cellSetRow);

            Cell value = new Cell { column = Encoding.UTF8.GetBytes("d:starwars"), data = Encoding.UTF8.GetBytes(testValue) };
            cellSetRow.values.Add(value);
            hbaseClient.StoreCells(hbaseTableName, cellSet);

            // Retrieve a cell by its key.
            cellSet = hbaseClient.GetCells(hbaseTableName, testKey);
            Console.WriteLine("The data with the key '" + testKey + "' is: " + Encoding.UTF8.GetString(cellSet.rows[0].values[0].data));
            // with the previous insert, it should yield: "the force is strong in this column"

		    //Scan over rows in a table. Assume the table has integer keys and you want data between keys 25 and 35.
		    Scanner scanSettings = new Scanner()
		    {
    		    batch = 10,
    		    startRow = BitConverter.GetBytes(25),
    		    endRow = BitConverter.GetBytes(35)
		    };

		    ScannerInformation scannerInfo = hbaseClient.CreateScanner(hbaseTableName, scanSettings);
		    CellSet next = null;
            Console.WriteLine("Scan results");

            while ((next = hbaseClient.ScannerGetNext(scannerInfo)) != null)
		    {
    		    foreach (CellSet.Row row in next.rows)
    		    {
                    Console.WriteLine(row.key + " : " + Encoding.UTF8.GetString(row.values[0].data));
    		    }
		    }

            Console.WriteLine("Press ENTER to continue ...");
            Console.ReadLine();
        }

7. **Main** 함수에서 처음 세 개의 변수를 설정합니다.
8. **F5** 키를 눌러 응용 프로그램을 실행합니다.



## 다음 작업
이 자습서에서는 HBase 클러스터를 프로비전하는 방법 및 테이블을 만들고 HBase 셸에서 가져온 데이터를 이 테이블에서 보는 방법을 알아보았습니다. 또한 Hive를 사용하여 HBase 테이블의 데이터를 쿼리하는 방법 및 HBase C# REST API를 사용하여 HBase 테이블을 만들고 이 테이블에서 데이터를 검색하는 방법도 알아보았습니다.

자세한 내용은 다음을 참조하세요.

- [HDInsight HBase 개요][hdinsight-hbase-overview]\: HBase는 비구조적/반구조적 대량 데이터에 대해 임의 액세스 및 강력한 일관성을 제공하는 Hadoop 기반의 Apache 오픈 소스 NoSQL 데이터베이스입니다.
- [Azure 가상 네트워크에서 HBase 클러스터 프로비전][hdinsight-hbase-provision-vnet]\: 가상 네트워크 통합을 사용하면 응용 프로그램이 HBase와 직접 통신할 수 있도록 응용 프로그램과 동일한 가상 네트워크에 HBase 클러스터를 배포할 수 있습니다.
- [HDInsight에서 HBase 복제 구성](hdinsight-hbase-geo-replication.md): 두 Azure 데이터 센터에서 HBase 복제를 구성하는 방법에 대해 알아봅니다. 
- [HDInsight에서 HBase를 사용하여 Twitter 데이터 분석][hbase-twitter-sentiment]\: HDInsight의 Hadoop 클러스터에서 HBase를 사용하여 빅데이터에 대한 실시간 [데이터 분석](http://en.wikipedia.org/wiki/Sentiment_analysis)을 수행하는 방법에 대해 알아봅니다.

[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hbase-reference]: http://hbase.apache.org/book.html#importtsv
[hbase-schema]: http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf
[hbase-quick-start]: http://hbase.apache.org/book.html#quickstart





[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-versions]: hdinsight-component-versioning.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/

[img-hdinsight-hbase-cluster-quick-create]: ./media/hdinsight-hbase-get-started/hdinsight-hbase-quick-create.png
[img-hdinsight-hbase-hive-editor]: ./media/hdinsight-hbase-get-started/hdinsight-hbase-hive-editor.png
[img-hdinsight-hbase-file-browser]: ./media/hdinsight-hbase-get-started/hdinsight-hbase-file-browser.png

<!--HONumber=54-->