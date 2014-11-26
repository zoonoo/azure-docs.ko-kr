<properties linkid="manage-services-hdinsight-sample-sqoop-import-export" urlDisplayName="HDInsight Samples" pageTitle="Sqoop Import-Export sample | Windows Azure" metaKeywords="hdinsight, hdinsight administration, hdinsight administration azure" description="Learn how to run Sqoop import and export on HDInsight." umbracoNaviHide="0" disqusComments="1" writer="bradsev" editor="cgronlun" manager="paulettm" title="Sqoop Import-Export sample" />

Sqoop 가져오기-내보내기 샘플
============================

이 샘플 항목에서는 Apache Sqoop을 사용하여 Azure의 SQL 데이터베이스에서 Azure HDFS 클러스터의 Hadoop으로 데이터를 가져오는 방법을 보여 줍니다.

비구조적 및 반구조적 데이터(예: 로그 및 파일)를 처리하기 위해 당연히 Hadoop을 선택하지만 관계형 데이터베이스에 저장된 구조적 데이터를 처리해야 할 경우도 있습니다.

Sqoop은 Hadoop 클러스터와 관계형 데이터베이스 간 데이터 전송을 위해 설계된 도구입니다. 이 도구를 사용하면 SQL, MySQL, Oracle 등의 RDBMS(관계형 데이터베이스 관리 시스템)에서 HDFS(Hadoop Distributed File System)로 데이터를 가져오고, MapReduce 또는 Hive로 Hadoop의 데이터를 변환한 후 데이터를 RDBMS로 다시 내보낼 수 있습니다. 이 자습서에서는 관계형 데이터베이스에 SQL 데이터베이스를 사용합니다.

Sqoop은 Cloudera, Inc.의 오픈 소스 소프트웨어 제품으로 2011년에 gitHub에서 [Apache Sqoop](http://sqoop.apache.org/)(영문) 사이트로 이동되었습니다.

Azure HDInsight에서는 Sqoop을 HDFS 클러스터 헤드 노드의 Hadoop 명령 셸에서 배포됩니다. Azure 포털의 Hadoop에서 사용 가능한 원격 데스크톱 기능을 사용하면 이 배포용 클러스터의 헤드 노드에 액세스할 수 있습니다.

**다음 내용을 배웁니다.**

-   Azure PowerShell을 사용하여 Azure HDInsight에서 파일에 포함된 데이터를 분석하는 MapReduce 프로그램을 실행하는 방법

**필수 조건**: 

Azure 계정이 있어야 하며, 구독에서 HDInsight Service를 사용할 수 있어야 합니다. Azure PowerShell 및 Azure HDInsight용 Powershell 도구를 설치하고 계정에서 사용할 수 있도록 구성해야 합니다. 이를 수행하는 방법에 대한 자세한 내용은 [Azure HDInsight 시작](/ko-kr/manage/services/hdinsight/get-started-hdinsight/)을 참조하십시오.

또한 SQL 데이터베이스에서 방화벽을 구성할 경우 현재 위치의 외부 연결 IP 주소도 있어야 합니다. 해당 주소를 확인하려면 [WhatIsMyIP](http://www.whatismyip.com/)(영문) 사이트에 방문하여 주소를 기록합니다. 또한 이 절차의 뒷부분에서 Hadoop 클러스터의 헤드에도 외부 연결 IP 주소가 필요합니다. 같은 방식으로 이 IP 주소를 확인할 수 있습니다.

**개요** 

이 항목에서는 샘플의 실행 방법을 보여 주고, MapReduce 프로그램의 Java 코드를 제공하며, 배운 내용을 요약하고, 다음 몇 단계에 대해 설명합니다. 이 항목에는 다음 섹션이 있습니다.

1.  [SQL 데이터베이스 설정](#set-up-sql)
2.  [Sqoop을 사용하여 HDInsigh에서 클러스터로 데이터 가져오기](#java-code)
3.  [요약](#summary)
4.  [다음 단계](#next-steps)

SQL 데이터베이스 설정
---------------------

TBD: screen shots for this section - this is the RDP approach still.

**SQL 데이터베이스를 만들려면**

1.  Azure 관리 포털에 로그인합니다.
2.  왼쪽 아래에서 새로 만들기를 클릭하고 데이터 서비스를 클릭한 후 SQL 데이터베이스를 클릭합니다. Azure 계정에 로그인합니다. 데이터베이스 서버를 만들려면 페이지의 왼쪽 아래 모서리에서 **데이터베이스** 아이콘을 클릭합니다.

3.  **시작** 페이지에서 **새 SQL 데이터베이스 서버 만들기** 옵션을 클릭합니다.

4.  **서버 만들기** 창에서 계정과 연결된 구독의 유형(예: **종량제**)을 선택하고 **다음**을 누릅니다.

5.  **서버 만들기** 창에서 해당 **지역**을 선택하고 **다음**을 클릭합니다.

6.  SQL 데이터베이스 서버의 서버 수준 보안 주체에 대한 로그인 및 암호를 지정한 후 **다음**을 누릅니다.

7.  **추가**를 눌러 현재 위치에서 SQL 데이터베이스에 액세스하여 AdventureWorks 데이터베이스를 업로드할 수 있도록 방화벽 규칙을 지정합니다. 방화벽은 각 요청이 시작된 IP 주소에 따라 액세스를 허용합니다. 추가할 값에 이 자습서의 구성 준비 단계에서 확인한 IP 주소를 사용합니다. 규칙 이름을 그림과 같이 지정해야 하지만 아래 예시를 위해 사용한 주소가 아니라 고유한 IP 주소를 사용해야 합니다. 또한 Hadoop 클러스터 헤드 노드의 외부 연결 IP 주소도 추가해야 합니다. 해당 주소를 이미 알고 있는 경우 지금 추가합니다. 그런 다음 **마침** 버튼을 누릅니다.

**AdventureWorks2012를 가져오려면**

1.  SQL 데이터베이스 사이트의 Adventure Works에 대한 권장 다운로드 링크에서 AdventureWorks2012 데이터베이스를 로컬 컴퓨터로 다운로드합니다.

2.  파일의 압축을 풀고 관리자 명령 프롬프트를 열어 AdventureWorks2012ForSQLAzure 폴더 내의 AdventureWorks 디렉터리로 이동합니다.

3.  다음 명령을 입력하여 CreateAdventureWorksForSQLAzure.cmd를 실행합니다.

 	CreateAdventureWorksForSQLAzure.cmd servername username password

    예를 들어 할당된 SQL 데이터베이스 서버의 이름이 b1gl33p이고 관리자의 사용자 이름이 "Fred"이며 암호가 "Secret"인 경우 다음과 같이 입력합니다.

    CreateAdventureWorksForSQLAzure.cmd b1gl33p.database.windows.net Fred@b1gl33p Secret

    이 스크립트는 데이터베이스를 만들고 스키마를 설치하고 데이터베이스를 샘플 데이터로 채웁니다.

1.  **AzurePlatform** 포털 페이지로 돌아가 왼쪽에서 사용 중인 구독(아래 예제에서는 **종량제**)을 클릭하고 데이터베이스(여기서는 wq6xlbyoq0)를 선택합니다. AventureWorks2012가 **데이터베이스 이름** 열에 나열됩니다. AventureWorks2012를 선택하고 페이지 위쪽에서 **관리** 아이콘을 누릅니다.

2.  메시지가 표시되면 SQL 데이터베이스의 자격 증명을 입력하고 **로그온**을 누릅니다.

3.  그러면 SQL 데이터베이스의 Adventure Works 데이터베이스에 대한 웹 인터페이스가 열립니다. 위쪽에 있는 **새 쿼리** 아이콘을 눌러 쿼리 편집기를 엽니다.

4.  현재 Sqoop에서 테이블 이름에 대괄호를 추가하므로, 동의어를 추가하여 SQL Server 테이블의 두 부분으로 구성된 이름을 지원해야 합니다. 그렇게 하려면 다음 쿼리를 실행합니다.

 	`CREATE SYNONYM [Sales.SalesOrderDetail] FOR Sales.SalesOrderDetail`

1.  다음 쿼리를 실행하여 결과를 검토합니다.

 	`select top 200 * from [Sales.SalesOrderDetail] `

Sqoop을 사용하여 HDInsigh에서 클러스터로 데이터 가져오기
--------------------------------------------------------

1.  계정 페이지의 클러스터 섹션에서 포트 열기 아이콘까지 아래로 스크롤하고 해당 아이콘을 클릭하여 클러스터 헤드 노드의 ODBC 서버 포트를 엽니다.

2.  계정 페이지로 돌아가 클러스터 섹션까지 아래로 스크롤하고 이번에는 **원격 데스크톱** 아이콘을 클릭하여 클러스터의 헤드 노드를 엽니다. TBD: update to use with PS.

3.  메시지가 표시되면 **열기**를 선택하여 .rdp 파일을 엽니다.

4.  원격 데스크톱 연결 창에서 연결을 선택합니다.

5.  Hadoop 클러스터(Azure 계정의 Hadoop이 아님)의 자격 증명을 **Windows 보안** 창에 입력하고 **확인**을 선택합니다.

6.  Internet Explorer를 열어 WhatIsMyIP 사이트로 이동하여 클러스터 헤드 노드의 외부 연결 IP 주소를 확인합니다. SQL 데이터베이스 관리 페이지로 돌아가 Hadoop 클러스터에서 SQL 데이터베이스에 액세스할 수 있는 방화벽 규칙을 추가합니다. 방화벽은 각 요청이 시작된 IP 주소에 따라 액세스를 허용합니다.

7.  데스크톱의 왼쪽 위에서 Hadoop 명령 셸 아이콘을 두 번 클릭하여 엽니다. "c:\\Apps\\dist\\sqoop\\bin" 디렉터리로 이동하여 다음 명령을 실행합니다.

    `sqoop import --connect "jdbc:sqlserver://[serverName].database.windows.net;username=[userName]@[serverName];password=[password];database=AdventureWorks2012" --table Sales.SalesOrderDetail --target-dir /data/lineitemData -m 1`

    예를 들어 다음 값을 가져옵니다. 
 * server name: wq6xlbyoq0 
 * username: HadoopOnAzureSqoopAdmin 
 * password: Pa\$\$w0rd

    sqoop 명령은 다음과 같습니다.

    `sqoop import --connect "jdbc:sqlserver://wq6xlbyoq0.database.windows.net;username=HadoopOnAzureSqoopAdmin@wq6xlbyoq0;password=Pa$$w0rd;;database=AdventureWorks2012" --table Sales.SalesOrderDetail --target-dir /data/lineitemData -m 1`

8.  Azure 포털에서 Hadoop의 **계정** 페이지로 돌아가 이번에는 대화형 콘솔을 엽니다. JavaScript 콘솔에서 \#lsr 명령을 실행하여 HDFS 클러스터의 파일 및 디렉터리를 나열합니다. TBD Update for GA.

9.  \#tail 명령을 실행하여 part-m-0000 파일에서 선택한 결과를 확인합니다. `tail /user/RAdmin/data/SalesOrderDetail/part-m-00000`

요약
----

이 자습서에서는 Azure HDInsight에서 관리한 Hadoop 클러스터와 Apache Sqoop을 사용하는 관계형 데이터베이스 간에 데이터를 전송하는 방법에 대해 알아보았습니다.

다음 단계
---------

Azure PowerShell을 사용하여 Azure HDInsight에서 Pig, Hive 및 MapReduce 작업 사용에 대한 지침을 제공하고 다른 샘플을 실행하는 자습서는 다음 항목을 참조하십시오.

-   [샘플: 파이 추정](/ko-kr/manage/services/hdinsight/howto-run-samples/sample-pi-estimator/)
-   [샘플: WordCount](/ko-kr/manage/services/hdinsight/howto-run-samples/sample-wordcount/)
-   [샘플: C\# 스트리밍](/ko-kr/manage/services/hdinsight/howto-run-samples/sample-csharp-streaming/)
-   [샘플: 10GB GraySort](/ko-kr/manage/services/hdinsight/howto-run-samples/sample-10gb-graysort/)
-   [자습서: Pig 사용](/ko-kr/manage/services/hdinsight/using-pig-with-hdinsight/)
-   [자습서: Hive 사용](/ko-kr/manage/services/hdinsight/using-hive-with-hdinsight/)
-   [자습서: MapReduce 사용](/ko-kr/manage/services/hdinsight/using-mapreduce-with-hdinsight/)
-   [Azure HDInsight SDK 문서](http://msdnstage.redmond.corp.microsoft.com/ko-kr/library/dn479185.aspx)

