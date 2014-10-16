<properties linkid="manage-services-hdinsight-hbase-get-started-hdinsight-hadoop" urlDisplayName="Get Started" pageTitle="Get started using HBase with Hadoop in HDInsight | Azure" metaKeywords="" description="Get started using HBase with Hadoop in HDInsight. learn how to created HBase tables and query them with Hive." metaCanonical="" services="hdinsight" documentationCenter="" title="Get started using HBase with Hadoop in HDInsight" authors="bradsev" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/21/2014" ms.author="bradsev"></tags>

# HDInsight에서 Hadoop과 함께 HBase를 사용하여 시작

HBase는 빅데이터를 온라인으로 트랜잭션 처리할 수 있게 하는 짧은 대기 시간의 NoSQL 데이터베이스입니다. HBase는 Azure 환경에 통합된 관리 클러스터로 제공합니다. 이 클러스터는 Azure Blob 저장소에 직접 데이터를 저장하도록 구성되며, 그러면 대기 시간이 짧고 성능/비용 선택 시 탄력성이 높습니다. 따라서 고객은 대규모 데이터 집합으로 작업하는 대화형 웹 사이트를 구축하고, 수백만 개의 끝점에서 발생하는 센서 및 원격 분석 데이터를 저장하는 서비스를 구축하고, Hadoop 작업을 사용해 이 데이터를 분석할 수 있습니다.

이 자습서에서는 HDInsight를 사용하여 HBase 테이블을 만들고 쿼리하는 방법을 알아봅니다. 다음 절차에 대해 설명합니다.

-   Azure 포털을 사용하여 HBase 클러스터를 프로비전하는 방법
-   RDP를 설정 및 사용하여 HBase 셸에 액세스하고 HBase 셸을 사용하여 HBase 샘플 테이블을 만들고 행을 추가하며 테이블의 행을 나열하는 방법
-   기존 HBase 테이블에 매핑되는 Hive 테이블을 만들고 HiveQL을 사용하여 HBase 테이블의 데이터를 쿼리하는 방법
-   .NET SDK를 사용하여 새 HBase 테이블을 만들고 계정의 HBase 테이블을 나열하는 방법 및 테이블에서 행을 추가하고 검색하는 방법

> [WACOM.NOTE] HBase는 현재 HDInsight에서 HDInsight 3.0 클러스터에 사용하도록 미리 보기로만 제공됩니다(Hadoop 2.2.0 기반). 버전 정보는 [HDInsight에서 제공하는 Hadoop 클러스터 버전의 새로운 기능][](영문)을 참조하세요.

미기 보기 기간 동안에는 HBase 클러스터에서 사용되는 원본 버전 데이터의 백업 복사본을 이 클러스터 외부에 저장하는 것이 좋습니다. 데이터베이스 파일의 형식이 향후 버전에서 바뀔 수 있으며 미리 보기 버전에서 사용되는 현재 데이터 파일 형식이 지원되지 않거나 후속 버전으로 업그레이드할 수 없을 수도 있기 때문입니다.

**필수 조건:**

이 자습서를 시작하기 전에 다음이 있어야 합니다.

-   Azure 구독. 구독을 얻는 방법에 대한 자세한 내용은 [구매 옵션][], [구성원 제공 항목][] 또는 [무료 평가판][]을 참조하세요.
-   Azure 저장소 계정. 자세한 내용은 [저장소 계정을 만드는 방법][]을 참조하세요.
-   Visual Studio의 복사본.

**예상 완료 시간:** 30분

## 자습서 내용

-   [Azure 포털에서 HBase 클러스터 프로비전][]
-   [HBase 셸에서 HBase 샘플 테이블 만들기][]
-   [Hive를 사용하여 HBase 테이블 쿼리][]
-   [HBase C# API를 사용하여 HBase 테이블 만들기 및 테이블에서 데이터 검색][]
-   [요약][]

## <a name="create-hbase-cluster"></a>Azure 포털에서 HBase 클러스터 프로비전

이 섹션에서는 Azure 포털을 사용하여 HBase 클러스터를 프로비전하는 방법을 설명합니다.

**Azure 포털에서 HDInsight 클러스터를 프로비전하려면**

1.  [Azure 관리 포털][]에 로그인합니다.

2.  왼쪽에 있는 **HDInsight**를 클릭하여 계정에 있는 클러스터의 상태를 나열한 다음 왼쪽 아래에서 **+새로 만들기** 아이콘을 클릭합니다.

    ![][]

3.  왼쪽에서 두 번째 열에서 HDInsight 아이콘을 클릭한 다음 그 다음 열에서 HBase 옵션을 선택합니다. CLUSTERNAME 및 CLUSTER SIZE의 값, 저장소 계정의 이름 및 새 HBase 클러스터의 암호를 지정합니다.

    ![][1]

4.  왼쪽 아래에 있는 확인 아이콘을 클릭하여 HBase 클러스터를 만듭니다.

## <a name="create-sample-table"></a>HBase 셸에서 HBase 샘플 테이블 만들기

이 섹션에서는 RDP(원격 데스크톱 프로토콜)를 설정 및 사용하여 HBase 셸에 액세스한 후 이 셸을 사용하여 HBase 샘플 테이블을 만들고 행을 추가한 후 테이블의 행을 나열하는 방법을 설명합니다.

여기서는 첫 번째 섹션에서 설명한 절차를 완료하여 이미 HBase 클러스터를 만들었다고 가정합니다.

**HBase 클러스터에 대한 RDP 연결 사용**

1.  HDInsight 클러스터에 원격 데스크톱 연결을 사용하려면 만든 HBase 클러스터를 선택하고 **구성** 탭을 클릭합니다. 페이지 아래에서 **원격 사용** 단추를 클릭하여 클러스터에 RDP 연결을 설정합니다.
2.  **원격 데스크톱 구성** 마법사에서 자격 증명과 만료 날짜를 제공하고 오른쪽 아래에서 체크 표시가 된 원을 클릭합니다. (작업이 완료되는 데 몇 분 정도 걸릴 수 있습니다.)
3.  HDInsight 클러스터에 연결하려면 **구성** 탭의 맨 아래에서 **연결** 단추를 클릭합니다.

**HBase 셸 열기**

1.  RDP 세션 내에서 바탕 화면에 있는 **Hadoop 명령 프롬프트** 바로 가기를 클릭합니다.

2.  폴더를 HBase 홈 디렉터리로 변경합니다.

        cd %HBASE_HOME%\bin

3.  HBase 셸을 엽니다.

        hbase shell

**샘플 테이블 만들기, 데이터 추가 및 데이터 검색**

1.  샘플 테이블을 만듭니다.

        create 'sampletable', 'cf1'

2.  샘플 테이블에 행을 추가합니다.

        put 'sampletable', 'row1', 'cf1:col1', 'value1'

3.  샘플 테이블의 행을 나열합니다.

        scan 'sampletable'

## <a name="hive-query"></a>Hive를 사용하여 HBase 테이블 쿼리

HBase 클러스터를 프로비전했고 테이블을 만들었으며, 이제 Hive를 사용하여 쿼리할 수 있습니다. 이 섹션에서는 HBase 테이블에 매핑되는 Hive 테이블을 만들고 이를 사용하여 HBase 테이블의 데이터를 쿼리합니다.

**클러스터 대시보드를 열려면**

1.  [Azure 관리 포털][]에 로그인합니다.
2.  왼쪽 창에서 **HDINSIGHT**를 클릭합니다. 지난 섹션에서 만든 클러스터를 포함하여 생성된 클러스터의 목록이 표시됩니다.
3.  Hive 작업을 실행할 클러스터의 이름을 클릭합니다.
4.  페이지 아래에서 **클러스터 관리**를 클릭하여 클러스터 대시보드를 엽니다. 다른 브라우저 탭에서 웹 페이지가 열립니다.
5.  Hadoop 사용자 계정의 사용자 이름 및 암호를 입력합니다. 기본 사용자 이름은 **admin**이고 암호는 프로비전 프로세스에서 입력한 암호입니다. 대시보드는 다음과 유사하게 표시됩니다.

    ![][2]

**Hive 쿼리를 실행하려면**

1.  HBase 테이블에 매핑되는 Hive 테이블을 만들려면 다음 HiveQL 스크립트를 Hive 콘솔 창에 입력하고 **전송** 단추를 클릭합니다. 다음 문을 실행하기 전에 HBase 셸을 사용하여 HBase에서 참조되는 샘플 테이블을 만들었는지 확인합니다.

        SET hbase.zookeeper.quorum=zookeepernode0,zookeepernode1,zookeepernode2;  
        CREATE EXTERNAL TABLE hbasesampletable(rowkey STRING, col1 STRING, col2 STRING)
        STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
        WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,cf1:col1,cf1:col2')
        TBLPROPERTIES ('hbase.table.name' = 'sampletable');

2.  HBase 테이블의 데이터에 대해 Hive 쿼리를 실행하려면 다음 HiveQL 스크립트를 Hive 콘솔 창에 입력하고 **전송** 단추를 클릭합니다.

        SET hbase.zookeeper.quorum=zookeepernode0,zookeepernode1,zookeepernode2;
        SET hive.aux.jars.path=file:///C:/Apps/dist/hive-0.12.0.2.0.9.0-1677/lib/hive-hbase-handler-0.12.0.2.0.9.0-1677.jar,file:///C:/Apps/dist/hive-0.12.0.2.0.9.0-1677/lib/hbase-server-0.96.0.2.0.9.0-1677-hadoop2.jar,file:///C:/Apps/dist/hive-0.12.0.2.0.9.0-1677/lib/hbase-protocol-0.96.0.2.0.9.0-1677-hadoop2.jar,file:///C:/Apps/dist/hive-0.12.0.2.0.9.0-1677/lib/htrace-core-2.01.jar,file:///C:/Apps/dist/hive-0.12.0.2.0.9.0-1677/lib/hbase-client-0.96.0.2.0.9.0-1677-hadoop2.jar,file:///C:/Apps/dist/hive-0.12.0.2.0.9.0-1677/lib/guava-12.0.1.jar;
        SELECT count(*) FROM hbasesampletable;

3.  Hive 쿼리의 결과를 검색하려면 작업 실행이 완료되면 **작업 세션** 창에서 **자세히 보기** 링크를 클릭합니다.

참고: HBase 셸 링크를 클릭하면 탭이 **HBase 셸**로 전환됩니다.

**출력 파일을 찾아보려면**

1.  클러스터 대시보드의 상단에서 **파일**을 클릭합니다.
2.  **Templeton-Job-Status**를 클릭합니다.
3.  마지막으로 수정한 시간이 앞에서 기록한 작업 시작 시간보다 약간 늦은 GUID 번호를 클릭합니다. 이 GUID를 기록해 둡니다. 다음 섹션에서 필요합니다.
4.  **stdout** 파일에는 다음 섹션에 필요한 데이터가 있습니다. 원하는 경우 **stdout**를 클릭하여 데이터 파일 복사본을 다운로드합니다.

## <a name="hbase-powershell"></a>HBase C# API를 사용하여 HBase 테이블 만들기 및 테이블에서 데이터 검색

Marlin은 REST API 위의 얇은 레이어로, C#에서 ProtoBuf를 사용하여 HBase를 조작하는 작업을 처리합니다. Marlin 프로젝트는 github에서 다운로드해야 하며 HBase .NET SDK를 사용하도록 구축한 프로젝트입니다.

1.  [Marlin 프로젝트 페이지][](영문)의 Marlin 프로젝트 다운로드에서 설명하는 빌드 단계를 따르세요. 압축을 풀어 로컬 디렉터리에 넣습니다.

2.  Visual Studio에서 프로젝트를 엽니다. **도구** 메뉴 -\> **라이브러리 패키지 관리자**로 이동하여 NuGet 패키지 관리자 관리 마법사를 열고 **솔루션에 대한 NuGet 패키지 관리...**를 선택합니다.

    ![][3]

3.  오른쪽 위에 있는 온라인 상자에서 protobuf-net을 검색하고 v2.0.0.68을 설치합니다. **솔루션 탐색기**에서 **Marlin** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **빌드**를 선택하여 Marlin 프로젝트를 빌드합니다.

4.  빌드된 결과 marlin.dll을 검색하여 C# 프로젝트에 추가합니다.

5.  클러스터 자격 증명을 사용하여 새 Marlin 인스턴스를 만들고 클러스터 버전을 검색합니다.

        var credentials = ClusterCredentials.Create("https://yourclustername.azurehdinsight.net/", "user", "password");
            var marlin = new Marlin(credentials);
        // retrieve the version as a test
        var version = marlin.GetVersion();
        Console.WriteLine(version);

6.  클러스터에서 테이블을 나열하려면 다음 코드를 사용할 수 있습니다.

        var tables = marlin.ListTables();
        foreach(var tableName in tables.name) 
                Console.WriteLine(tableName);

7.  새 HBase 테이블을 만들려면 다음 코드를 사용합니다.

        var schema = new TableSchema();
        schema.name = "sampletable";
        schema.columns.Add(new ColumnSchema() { name = "cf1" });
        schema.columns.Add(new ColumnSchema() { name = "cf2" });
        marlin.CreateTable(schema);

8.  키를 기준으로 행을 검색하려면 테이블 이름 및 행 키를 지정하여 행 값을 검색합니다.

        var cells = marlin.GetCells("sampletable", "row1");
        var row = cells.rows[0];
            foreach(var val in row.values) 
            {
               Console.WriteLine(Encoding.UTF8.GetString(val.data));
            }

9.  새 데이터 행을 저장하려면 다음 코드를 사용할 수 있습니다.

        CellSet set = new CellSet();
        CellSet.Row row = new CellSet.Row() { key = BitConverter.GetBytes(1337) };
            var value = new Cell()
                    {
                        column = Encoding.UTF8.GetBytes("cf1:d"),
                        data = Encoding.UTF8.GetBytes("Hello World!")
                    };
            row.values.Add(value);
            set.rows.Add(row);
        marlin.StoreCells("sampletable", set);

## <a name="summary"></a>요약

이 자습서에서는 HBase 클러스터를 프로비전하는 방법, 테이블을 만들고 HBase 셸에서 가져온 데이터를 이 테이블에서 보는 방법을 알아보았습니다. 또한 Hive를 사용하여 HBase 테이블의 데이터를 쿼리하는 방법 및 HBase C# API를 사용하여 HBase 테이블을 만들고 이 테이블에서 데이터를 검색하는 방법도 알아보았습니다.

  [HDInsight에서 제공하는 Hadoop 클러스터 버전의 새로운 기능]: ../hdinsight-component-versioning/
  [구매 옵션]: http://azure.microsoft.com/en-us/pricing/purchase-options/
  [구성원 제공 항목]: http://azure.microsoft.com/en-us/pricing/member-offers/
  [무료 평가판]: http://azure.microsoft.com/en-us/pricing/free-trial/
  [저장소 계정을 만드는 방법]: http://azure.microsoft.com/ko-kr/documentation/articles/storage-create-storage-account/
  [Azure 포털에서 HBase 클러스터 프로비전]: #create-hbase-cluster
  [HBase 셸에서 HBase 샘플 테이블 만들기]: #create-sample-table
  [Hive를 사용하여 HBase 테이블 쿼리]: #hive-query
  [HBase C# API를 사용하여 HBase 테이블 만들기 및 테이블에서 데이터 검색]: #hbase-powershell
  [요약]: #summary
  [Azure 관리 포털]: https://manage.windowsazure.com/
  []: http://i.imgur.com/PmGynKZ.jpg
  [1]: http://i.imgur.com/ecxbB9K.jpg
  [2]: http://i.imgur.com/tMwXlj9.jpg
  [Marlin 프로젝트 페이지]: https://github.com/thomasjungblut/marlin
  [3]: http://i.imgur.com/hUNoJDJ.jpg
