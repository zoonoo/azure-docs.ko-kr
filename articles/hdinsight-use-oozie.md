<properties linkid="hdinsight-use-oozie-with-hdinsight" urlDisplayName="Use Oozie with HDInsight" pageTitle="Use Oozie with HDInsight | Azure" metaKeywords="" description="Use Oozie with HDInsight, a big data solution. Learn how to define an Oozie workflow, and submit an Oozie job." metaCanonical="" services="hdinsight" documentationCenter="" title="Use Oozie with HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

HDInsight에서 Oozie 사용
========================

워크플로를 정의하는 방법 및 HDInsight에서 워크플로를 실행하는 방법을 알아보십시오. Oozie 코디네이터에 대해 알아보려면 [HDInsight에서 시간 기준의 Oozie 코디네이터 사용](../hdinsight-use-oozie-coordinator-time/)을 참조하십시오.

**예상 완료 시간:** 40분

이 문서에서는 다음을 수행합니다.
--------------------------------

1.  [Oozie 정의](#whatisoozie)
2.  [필수 조건](#prerequisites)
3.  [Oozie 워크플로 파일 정의](#defineworkflow)
4.  [Oozie 프로젝트 배포 및 자습서 준비](#deploy)
5.  [워크플로 실행](#run)
6.  [다음 단계](#nextsteps)

Oozie 정의
----------

Apache Oozie는 Hadoop 작업을 관리하는 워크플로/코디네이션 시스템입니다. Hadoop 스택과 통합되며 Apache MapReduce, Apache Pig, Apache Hive, Apache Sqoop에 Hadoop 작업을 지원합니다. 또한 Java 프로그램이나 셸 스크립트와 같이 시스템에 고유한 작업을 예약하는 데 사용할 수 있습니다.

구현할 워크플로에는 다음 두 작업이 포함되어 있습니다.

![워크플로 다이어그램](./media/hdinsight-use-oozie/HDI.UseOozie.Workflow.Diagram.png)

1.  log4j 로그 파일에서 각 로그 수준 유형의 수를 계산하는 HiveQL 스크립트를 실행하는 Hive 작업. 각 log4j 로그는 유형과 심각도를 표시하는 [LOG LEVEL] 필드가 포함된 필드의 줄로 구성되어 있습니다. 예를 들면 다음과 같습니다.

         2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
         2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
         2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
         ...

    Hive 스크립트는 다음과 유사하게 출력됩니다.

         [DEBUG] 434
         [ERROR] 3
         [FATAL] 1
         [INFO]  96
         [TRACE] 816
         [WARN]  4

    Hive에 대한 자세한 내용은 [HDInsight에서 Hive 사용](/ko-kr/documentation/articles/hdinsight-use-hive/)을 참조하십시오.

2.  HiveQL 작업 출력을 Azure SQL 데이터베이스의 테이블에 내보내는 Sqoop 작업. Sqoop에 대한 자세한 내용은 [HDInsight에서 Sqoop 사용](../hdinsight-use-sqoop/)을 참조하십시오.

> [WACOM.NOTE] HDInsight 클러스터에서 지원되는 Oozie 버전을 보려면 [HDInsight에서 제공하는 클러스터 버전의 새로운 기능](/ko-kr/documentation/articles/hdinsight-component-versioning/)을 참조하십시오.

> [WACOM.NOTE] 이 자습서 내용은 HDInsight 클러스터 버전 2.1 및 3.0에서 작동합니다. 이 문서는 HDInsight 에뮬레이터에서는 테스트되지 않았습니다.

필수 조건
---------

이 자습서를 시작하기 전에 다음이 있어야 합니다.

-   Azure PowerShell이 설치 및 구성된 **워크스테이션**. 자세한 내용은 [Azure PowerShell 설치 및 구성](/en-us/manage/install-and-configure-windows-powershell/)을 참조하십시오. PowerShell 스크립트를 실행하려면 Azure PowerShell을 관리자 권한으로 실행하고 실행 정책을 *RemoteSigned*로 설정해야 합니다. [Windows PowerShell 스크립트 실행](http://technet.microsoft.com/en-us/library/ee176949.aspx)을 참조하십시오.
-   **HDInsight 클러스터**. HDInsight 클러스터 만들기에 대한 자세한 내용은 [HDInsight 클러스터 프로비전](/ko-kr/documentation/articles/hdinsight-provision-clusters/) 또는 [HDInsight 시작](/ko-kr/documentation/articles/hdinsight-get-started/)을 참조하십시오. 자습서를 진행하려면 다음 데이터가 있어야 합니다.

    <table data-morhtml="true" border="1">
  <tr data-morhtml="true"><th data-morhtml="true">클러스터 속성</th><th data-morhtml="true">PowerShell 변수 이름</th><th data-morhtml="true">값</th><th data-morhtml="true">설명</th></tr>
  <tr data-morhtml="true"><td data-morhtml="true">HDInsight 클러스터 이름</td><td data-morhtml="true">$clusterName</td><td data-morhtml="true"></td><td data-morhtml="true">이 자습서를 실행할 HDInsight 클러스터입니다.</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">HDInsight 클러스터 사용자 이름</td><td data-morhtml="true">$clusterUsername</td><td data-morhtml="true"></td><td data-morhtml="true">HDInsight 클러스터 사용자의 사용자 이름입니다. </td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">HDInsight 클러스터 사용자 암호 </td><td data-morhtml="true">$clusterPassword</td><td data-morhtml="true"></td><td data-morhtml="true">HDInsight 클러스터 사용자의 암호입니다.</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">Azure 저장소 계정 이름</td><td data-morhtml="true">$storageAccountName</td><td data-morhtml="true"></td><td data-morhtml="true">HDInsight 클러스터에 사용할 수 있는 Azure 저장소 계정입니다. 이 자습서에서는 클러스터 프로비전 프로세스 중에 지정된 기본 저장소 계정을 사용합니다.</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">Azure Blob 컨테이너 이름</td><td data-morhtml="true">$containerName</td><td data-morhtml="true"></td><td data-morhtml="true">이 예제에서는 기본 HDInsight 클러스터 파일 시스템에 사용된 Azure Blob 저장소 컨테이너를 사용합니다. 기본적으로 컨테이너 이름은 HDInsight 클러스터 이름과 동일합니다.</td></tr>
  </table>

-   **Azure SQL 데이터베이스**. 워크스테이션으로부터의 액세스를 허용하도록 SQL 데이터베이스 서버의 방화벽 규칙을 구성해야 합니다. SQL 데이터베이스 만들기 및 방화벽 구성에 대한 자세한 내용은 [Azure SQL 데이터베이스를 사용하여 시작](../sql-database-get-started/)을 참조하십시오. 이 문서에서는 이 자습서에 필요한 SQL 데이터베이스 테이블을 만드는 데 PowerShell 스크립트를 제공합니다.

    <table data-morhtml="true" border="1">
  <tr data-morhtml="true"><th data-morhtml="true">SQL 데이터베이스 속성</th><th data-morhtml="true">PowerShell 변수 이름</th><th data-morhtml="true">값</th><th data-morhtml="true">설명</th></tr>
  <tr data-morhtml="true"><td data-morhtml="true">SQL 데이터베이스 서버 이름</td><td data-morhtml="true">$sqlDatabaseServer</td><td data-morhtml="true"></td><td data-morhtml="true">Sqoop에서 데이터를 내보낼 SQL 데이터베이스 서버입니다. </td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">SQL 데이터베이스 로그인 이름</td><td data-morhtml="true">$sqlDatabaseLogin</td><td data-morhtml="true"></td><td data-morhtml="true">SQL 데이터베이스 로그인 이름입니다.</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">SQL 데이터베이스 로그인 암호</td><td data-morhtml="true">$sqlDatabaseLoginPassword</td><td data-morhtml="true"></td><td data-morhtml="true">SQL 데이터베이스 로그인 암호입니다.</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">SQL 데이터베이스 이름</td><td data-morhtml="true">$sqlDatabaseName</td><td data-morhtml="true"></td><td data-morhtml="true">Sqoop에서 데이터를 내보낼 Azure SQL 데이터베이스입니다. </td></tr>
  </table>

    > [WACOM.NOTE]기본적으로 Azure SQL 데이터베이스는 Azure HDInsight 같은 Azure 서비스로부터의 연결을 허용합니다. 이 방화벽 설정을 사용하지 않도록 설정한 경우 Azure 관리 포털에서 사용하도록 설정해야 합니다. SQL 데이터베이스 만들기 및 방화벽 규칙 구성에 대한 지침은 [SQL 데이터베이스 만들기 및 구성](../sql-database-create-configure/)을 참조하십시오.

> [WACOM.NOTE] 테이블에 값을 채웁니다. 이 자습서를 진행하는 데 도움이 됩니다.

Oozie 워크플로 및 관련 HiveQL 스크립트 정의
-------------------------------------------

Oozie 워크플로 정의는 hPDL(XML 프로세스 정의 언어)로 작성되었습니다. 기본 워크플로 파일 이름은 *workflow.xml*입니다. 워크플로 파일을 로컬로 저장하고 이 자습서의 뒷부분에 나오는 Azure PowerShell을 사용하여 HDInsight 클러스터에 배포합니다.

워크플로의 Hive 작업은 HiveQL 스크립트 파일을 호출합니다. 이 스크립트 파일에는 세 개의 HiveQL 문이 포함되어 있습니다.

1.  **DROP TABLE 문**은 log4j Hive 테이블이 있는 경우 삭제합니다.
2.  **CREATE TABLE 문**은 log4j 로그 파일의 위치를 가리키는
3.  log4j Hive 외부 테이블을 만듭니다. 필드 구분 기호는 ","입니다. 기본 줄 구분 기호는 "\\n"입니다. Hive 외부 테이블은 Oozie 워크플로를 여러 번 실행하려는 경우 데이터 파일이 원래 위치에서 제거되지 않도록 하는 데 사용됩니다.
4.  **INSERT OVERWRITE 문**은 log4j Hive 테이블에서 각 로그 수준 유형의 수를 계산하고 그 출력 결과를 Azure 저장소 - Blob(WASB) 위치에 저장합니다.

알려진 Hive 경로 문제가 있습니다. Oozie 작업을 제출할 때 이 문제가 발생합니다. 문제 해결을 위한 지침은 [TechNet Wiki](http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx)(영문)에서 찾을 수 있습니다.

**HiveQL 스크립트 파일을 워크플로에서 호출하도록 정의하려면**

1.  다음 내용이 포함된 텍스트 파일을 만듭니다.

         DROP TABLE ${hiveTableName};
         CREATE EXTERNAL TABLE ${hiveTableName}(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
         INSERT OVERWRITE DIRECTORY '${hiveOutputFolder}' SELECT t4 AS sev, COUNT(*) AS cnt FROM ${hiveTableName} WHERE t4 LIKE '[%' GROUP BY t4;

    스크립트에 사용된 세 개의 변수는 다음과 같습니다.

    -   ${hiveTableName}
    -   ${hiveDataFolder}
    -   ${hiveOutputFolder}

    워크플로 정의 파일(이 자습서에서는 workflow.xml)은 런타임 시 이러한 값을 이 HiveQL 스크립트에 전달합니다.

2.  이 파일을 **ANSI(ASCII)** 인코딩(텍스트 편집기에서 옵션을 제공하지 않으면 메모장 사용)을 사용하여 **C:\\Tutorials\\UseOozie\\useooziewf.hql**로 저장합니다. 이 스크립트 파일은 자습서의 뒷부분에 나오는 HDInsight 클러스터에 배포됩니다.

**워크플로를 정의하려면**

1.  다음 내용이 포함된 텍스트 파일을 만듭니다.

		<workflow data-morhtml="true"-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
			<start data-morhtml="true" to = "RunHiveScript"/> 
		
			<action data-morhtml="true" name="RunHiveScript">
				<hive data-morhtml="true" xmlns="uri:oozie:hive-action:0.2">
					<job data-morhtml="true"-tracker>${jobTracker}</job-tracker>
					<name data-morhtml="true"-node>${nameNode}</name-node>
					<configuration data-morhtml="true">
						<property data-morhtml="true">
							<name data-morhtml="true">mapred.job.queue.name</name>
							<value data-morhtml="true">${queueName}</value>
						</property>
					</configuration>
					<script data-morhtml="true">${hiveScript}</script>
					<param data-morhtml="true">hiveTableName=${hiveTableName}</param>
					<param data-morhtml="true">hiveDataFolder=${hiveDataFolder}</param>
					<param data-morhtml="true">hiveOutputFolder=${hiveOutputFolder}</param>
				</hive>
				<ok data-morhtml="true" to="RunSqoopExport"/>
				<error data-morhtml="true" to="fail"/>
			</action>
	
			<action data-morhtml="true" name="RunSqoopExport">
				<sqoop data-morhtml="true" xmlns="uri:oozie:sqoop-action:0.2">
					<job data-morhtml="true"-tracker>${jobTracker}</job-tracker>
					<name data-morhtml="true"-node>${nameNode}</name-node>
					<configuration data-morhtml="true">
						<property data-morhtml="true">
							<name data-morhtml="true">mapred.compress.map.output</name>
							<value data-morhtml="true">true</value>
						</property>
					</configuration>
				<arg data-morhtml="true">export</arg>
				<arg data-morhtml="true">--connect</arg> 
				<arg data-morhtml="true">${sqlDatabaseConnectionString}</arg> 
				<arg data-morhtml="true">--table</arg>
				<arg data-morhtml="true">${sqlDatabaseTableName}</arg> 
				<arg data-morhtml="true">--export-dir</arg> 
				<arg data-morhtml="true">${hiveOutputFolder}</arg> 
				<arg data-morhtml="true">-m</arg> 
				<arg data-morhtml="true">1</arg>
				<arg data-morhtml="true">--input-fields-terminated-by</arg>
				<arg data-morhtml="true">"\001"</arg>
				</sqoop>
				<ok data-morhtml="true" to="end"/>
				<error data-morhtml="true" to="fail"/>
			</action>
	
			<kill data-morhtml="true" name="fail">
				<message data-morhtml="true">Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
			</kill>
	
			<end data-morhtml="true" name="end"/>
		</workflow-app>

    여기 워크플로에 정의된 두 작업이 있습니다. 시작 작업은 *RunHiveScript*입니다. 작업이 *ok*를 실행하면 다음 작업은 *RunSqoopExport*입니다.

    RunHiveScript에는 몇 개의 변수가 있습니다. Azure PowerShell을 사용하여 워크스테이션에서 Oozie 작업을 제출할 때 이러한 값을 전달합니다.

	<table data-morhtml="true" border="1">
	<tr data-morhtml="true"><th data-morhtml="true">워크플로 변수</th><th data-morhtml="true">설명</th></tr>
	<tr data-morhtml="true"><td data-morhtml="true">${jobTracker}</td><td data-morhtml="true">Hadoop 작업 추적기의 URL을 지정합니다. HDInsight 클러스터 버전 2.0 및 3.0에서 <strong data-morhtml="true">jobtrackerhost:9010</strong>을 사용합니다.</td></tr>
	<tr data-morhtml="true"><td data-morhtml="true">${nameNode}</td><td data-morhtml="true">Hadoop namenode의 URL을 지정합니다. 기본 파일 시스템 WASB 주소를 사용합니다. 예: <i data-morhtml="true">wasb://&lt;containerName&gt;@&lt;storageAccountName&gt;.blob.core.windows.net</i></td></tr>
	<tr data-morhtml="true"><td data-morhtml="true">${queueName}</td><td data-morhtml="true">작업을 제출할 queuename을 지정합니다. <strong data-morhtml="true">기본값</strong>을 사용합니다.</td></tr>
	</table>

	<table data-morhtml="true" border="1">
	<tr data-morhtml="true"><th data-morhtml="true">Hive 작업 변수</th><th data-morhtml="true">설명</th></tr>
	<tr data-morhtml="true"><td data-morhtml="true">${hiveDataFolder}</td><td data-morhtml="true">Hive Create Table 명령의 소스 디렉터리입니다.</td></tr>
	<tr data-morhtml="true"><td data-morhtml="true">${hiveOutputFolder}</td><td data-morhtml="true">INSERT OVERWRITE 문의 출력 폴더입니다.</td></tr>
	<tr data-morhtml="true"><td data-morhtml="true">${hiveTableName}</td><td data-morhtml="true">log4j 데이터 파일을 참조하는 Hive 테이블의 이름입니다.</td></tr>
	</table>

	<table data-morhtml="true" border="1">
	<tr data-morhtml="true"><th data-morhtml="true">Sqoop 작업 변수</th><th data-morhtml="true">설명</th></tr>
	<tr data-morhtml="true"><td data-morhtml="true">${sqlDatabaseConnectionString}</td><td data-morhtml="true">SQL 데이터베이스 연결 문자열입니다.</td></tr>
	<tr data-morhtml="true"><td data-morhtml="true">${sqlDatabaseTableName}</td><td data-morhtml="true">데이터를 내보낼 SQL 데이터베이스 테이블입니다.</td></tr>
	<tr data-morhtml="true"><td data-morhtml="true">${hiveOutputFolder}</td><td data-morhtml="true">Hive INSERT OVERWRITE 문의 출력 폴더입니다. 이 폴더는 Sqoop 내보내기의 내보내기 디렉터리와 동일한 폴더입니다.</td></tr>
	</table>

    Oozie 워크플로 및 워크플로 작업 사용에 대한 자세한 내용은 [Apache Oozie 4.0 설명서](http://oozie.apache.org/docs/4.0.0/)(HDInsight 클러스터 버전 3.0의 경우) 또는 [Apache Oozie 3.3.2 설명서](http://oozie.apache.org/docs/3.3.2/)(HDInsight 클러스터 버전 2.1의 경우)를 참조하십시오.

2.  이 파일을 ANSI (ASCII) 인코딩(텍스트 편집기에서 옵션을 제공하지 않으면 메모장 사용)을 사용하여 **C:\\Tutorials\\UseOozie\\workflow.xml**로 저장합니다.

Oozie 프로젝트 배포 및 자습서 준비
----------------------------------

Azure PowerShell 스크립트를 실행하여 다음을 수행합니다.

-   HiveQL 스크립트(useoozie.hql) Azure Blob 저장소를 wasb:///tutorials/useoozie/useoozie.hql에 복사합니다.
-   workflow.xml을 wasb:///tutorials/useoozie/workflow.xml에 복사합니다.
-   데이터 파일(/example/data/sample.log)을 wasb:///tutorials/useoozie/data/sample.log에 복사합니다.
-   Sqoop 내보내기 데이터를 저장할 SQL 데이터베이스 테이블을 만듭니다. 테이블 이름은 *log4jLogCount*입니다.

**HDInsight 저장소 이해**

HDInsight는 데이터 저장소로 Azure Blob 저장소를 사용합니다. *WASB* 또는 *Azure 저장소 - Blob*이라고 합니다. WASB는 Azure Blob 저장소에서 HDFS의 Microsoft 구현입니다. 자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용](/ko-kr/documentation/articles/hdinsight-use-blob-storage/)을 참조하십시오.

HDInsight 클러스터를 프로비전할 때 Azure 저장소 계정 및 그 계정에서의 특정 Blob 저장소 컨테이너는 HDFS에서처럼 기본 파일 시스템으로 지정됩니다. 프로비전 프로세스 중에 이 저장소 계정 외에도 동일한 Azure 구독 또는 다른 Azure 구독에서 저장소 계정을 추가할 수 있습니다. 저장소 계정 추가에 대한 지침은 [HDInsight 클러스터 프로비전](/ko-kr/documentation/articles/hdinsight-provision-clusters/)을 참조하십시오. 이 자습서에 사용된 PowerShell 스크립트를 간소화하려면 모든 파일이 */tutorials/useoozie*에 있는 기본 파일 시스템 컨테이너에 저장되어야 합니다. 기본적으로 이 컨테이너 이름은 HDInsight 클러스터 이름과 동일합니다. WASB 구문은 다음과 같습니다.

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] HDInsight 클러스터 버전 3.0에서는 *wasb://* 구문만 지원됩니다. 이전 *asv://* 구문은 HDInsight 2.1 및 1.6 클러스터에서는 지원되지만 HDInsight 3.0 클러스터에서는 지원되지 않으며 그 이상 버전에서도 지원되지 않습니다.

> [WACOM.NOTE] WASB 경로는 가상 경로입니다. 자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용](/ko-kr/documentation/articles/hdinsight-use-blob-storage/)을 참조하십시오.

기본 파일 시스템 컨테이너에 저장된 파일은 다음 URI 중 아무거나 사용하여 HDInsight에서 액세스할 수 있습니다(예제로 workflow.xml 사용).

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/workflow.xml
    wasb:///tutorials/useoozie/workflow.xml
    /tutorials/useoozie/workflow.xml

파일을 저장소 계정에서 바로 액세스하는 경우 파일의 Blob 이름은 다음과 같습니다.

    tutorials/useoozie/workflow.xml

**Hive 내부 테이블 및 외부 테이블 이해**

Hive 내부 테이블 및 외부 테이블에 대해 알아야 할 사항이 몇 가지 있습니다.

-   CREATE TABLE 명령은 관리되는 테이블이라고도 하는 내부 테이블을 만듭니다. 데이터 파일은 기본 컨테이너에 있어야 합니다.
-   CREATE TABLE 명령은 데이터 파일을 기본 컨테이너의 /hive/warehouse/ 폴더로 이동합니다.
-   CREATE EXTERNAL TABLE 명령은 외부 테이블을 만듭니다. 데이터 파일은 기본 컨테이너가 아닌 곳에 있을 수 있습니다.
-   CREATE EXTERNAL TABLE 명령은 데이터 파일을 이동하지 않습니다.
-   CREATE EXTERNAL TABLE 명령은 LOCATION 절에 지정된 폴더 아래에 하위 폴더를 허용하지 않습니다. 이는 자습서에서 sample.log 파일의 복사본을 만들기 때문입니다.

자세한 내용은 [HDInsight: Hive 내부 및 외부 테이블 설명](http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx)을 참조하십시오.

**자습서를 준비하려면**

1.  Windows PowerShell ISE(Windows 8 시작 화면에서 **PowerShell\_ISE**를 입력한 후 **Windows PowerShell ISE**를 클릭합니다. [Windows 8 및 Windows에서 Windows PowerShell 시작](http://technet.microsoft.com/en-us/library/hh847889.aspx)(영문)을 참조하십시오.)를 엽니다.
2.  아래 창에서 다음 명령을 실행하여 Azure 구독에 연결합니다.

         Add-AzureAccount

    Azure 계정 자격 증명을 입력하라는 메시지가 표시됩니다. 이 구독 연결 설치 방법이 시간 초과되었으며 12시간 후에 cmdlet을 다시 실행해야 합니다.

    > [WACOM.NOTE] 여러 Azure 구독이 있으며 기본 구독이 사용할 구독이 아닌 경우 **Select-AzureSubscription** cmdlet을 사용하여 현재 구독을 선택합니다.

3.  다음 스크립트를 스크립트 창에 복사한 다음 처음 6개의 변수를 설정합니다.

         # WASB variables
         $storageAccountName = "<StorageAccountName>"
         $containerName = "<BlobStorageContainerName>"
            
         # SQL database variables
         $sqlDatabaseServer = "<SQLDatabaseServerName>"  
         $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
         $sqlDatabaseLoginPassword = "SQLDatabaseLoginPassword>"
         $sqlDatabaseName = "<SQLDatabaseName>"  
         $sqlDatabaseTableName = "log4jLogsCount"
            
         # Oozie files for the tutorial 
         $workflowDefinition = "C:\Tutorials\UseOozie\workflow.xml"
         $hiveQLScript = "C:\Tutorials\UseOozie\useooziewf.hql"
            
         # WASB folder for storing the Oozie tutorial files.
         $destFolder = "tutorials/useoozie"  # Do NOT use the long path here

    변수에 대한 자세한 설명은 이 자습서에서 [필수 조건](#prerequisites) 섹션을 참조하십시오.

4.  스크립트 창의 스크립트에 다음을 추가합니다.

         # Create a storage context object
         $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
         $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey
            
         function uploadOozieFiles()
         {      
             Write-Host "Copy workflow definition and HiveQL script file ..." -ForegroundColor Green
             Set-AzureStorageBlobContent -File $workflowDefinition -Container $containerName -Blob "$destFolder/workflow.xml" -Context $destContext
             Set-AzureStorageBlobContent -File $hiveQLScript -Container $containerName -Blob "$destFolder/useooziewf.hql" -Context $destContext
         }
                    
         function prepareHiveDataFile()
         {
             Write-Host "Make a copy of the sample.log file ... " -ForegroundColor Green
             Start-CopyAzureStorageBlob -SrcContainer $containerName -SrcBlob "example/data/sample.log" -Context $destContext -DestContainer $containerName -destBlob "$destFolder/data/sample.log" -DestContext $destContext
         }
                    
         function prepareSQLDatabase()
         {
             # SQL query string for creating log4jLogsCount table
             $cmdCreateLog4jCountTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
                     [Level] [nvarchar](10) NOT NULL,
                     [Total] float,
                 CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED   
                 (
                 [Level] ASC
                 )
                 )"
                    
             #Create the log4jLogsCount table
             Write-Host "Create Log4jLogsCount table ..." -ForegroundColor Green
             $conn = New-Object System.Data.SqlClient.SqlConnection
             $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabaseLoginPassword;Encrypt=true;Trusted_Connection=false;"
             $conn.open()
             $cmd = New-Object System.Data.SqlClient.SqlCommand
             $cmd.connection = $conn
             $cmd.commandtext = $cmdCreateLog4jCountTable
             $cmd.executenonquery()
                    
             $conn.close()
         }
                    
         # upload workflow.xml, coordinator.xml, and ooziewf.hql
         uploadOozieFiles;
                    
         # make a copy of example/data/sample.log to example/data/log4j/sample.log
         prepareHiveDataFile;
            
         # create log4jlogsCount table on SQL database
         prepareSQLDatabase;

5.  **스크립트 실행**을 클릭하거나 **F5** 키를 눌러 스크립트를 실행합니다. 다음과 유사하게 출력됩니다.

    ![자습서 준비 출력](./media/hdinsight-use-oozie/HDI.UseOozie.Preparation.Output1.png)

Oozie 프로젝트 실행
-------------------

Azure PowerShell은 Oozie 작업을 정의하는 데 현재 어떤 cmdlet도 제공하지 않습니다. Invoke-RestMethod PowerShell cmdlet을 사용하여 Oozie 웹 서비스를 호출할 수 있습니다. Oozie 웹 서비스 API는 HTTP REST JSON API입니다. Oozie 웹 서비스 API에 대한 자세한 내용은 [Apache Oozie 4.0 설명서](http://oozie.apache.org/docs/4.0.0/)(HDInsight 클러스터 버전 3.0의 경우) 또는 [Apache Oozie 3.3.2 설명서](http://oozie.apache.org/docs/3.3.2/)(HDInsight 클러스터 버전 2.1의 경우)를 참조하십시오.

**Oozie 작업을 제출하려면**

1.  Windows PowerShell ISE(Windows 8 시작 화면에서 **PowerShell\_ISE**를 입력한 후 **Windows PowerShell ISE**를 클릭합니다. [Windows 8 및 Windows에서 Windows PowerShell 시작](http://technet.microsoft.com/en-us/library/hh847889.aspx)(영문)을 참조하십시오.)를 엽니다.

2.  다음 스크립트를 스크립트 창에 복사한 다음 처음 10개의 변수를 설정합니다(여섯 번째 \$storageUri는 건너뜀).

         #HDInsight cluster variables
         $clusterName = "<HDInsightClusterName>"
         $clusterUsername = "<HDInsightClusterUsername>"
         $clusterPassword = "<HDInsightClusterUserPassword>"
            
         #Azure Blob storage (WASB) variables
         $storageAccountName = "<StorageAccountName>"
         $storageContainerName = "<BlobContainerName>"
         $storageUri="wasb://$storageContainerName@$storageAccountName.blob.core.windows.net"
            
         #Azure SQL database variables
         $sqlDatabaseServer = "<SQLDatabaseServerName>"
         $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
         $sqlDatabaseLoginPassword = "<SQLDatabaseloginPassword>"
         $sqlDatabaseName = "<SQLDatabaseName>"  
            
         #Oozie WF variables
         $oozieWFPath="$storageUri/tutorials/useoozie"  # The default name is workflow.xml. And you don't need to specify the file name.
         $waitTimeBetweenOozieJobStatusCheck=10
            
         #Hive action variables
         $hiveScript = "$storageUri/tutorials/useoozie/useooziewf.hql"
         $hiveTableName = "log4jlogs"
         $hiveDataFolder = "$storageUri/tutorials/useoozie/data"
         $hiveOutputFolder = "$storageUri/tutorials/useoozie/output"
            
         #Sqoop action variables
         $sqlDatabaseConnectionString = "jdbc:sqlserver://$sqlDatabaseServer.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServer;password=$sqlDatabaseLoginPassword;database=$sqlDatabaseName"
         $sqlDatabaseTableName = "log4jLogsCount"

         $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
         $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    변수에 대한 자세한 설명은 이 자습서에서 [필수 조건](#prerequisites) 섹션을 참조하십시오.

3.  스크립트에 다음을 추가합니다. 이 부분은 Oozie 페이로드를 정의합니다.

         #OoziePayload used for Oozie web service submission
         $OoziePayload =  @"
         <?xml version="1.0" encoding="UTF-8"?>
         <configuration>
            
            <property>
                <name>nameNode</name>
                <value>$storageUrI</value>
            </property>
            
            <property>
                <name>jobTracker</name>
                <value>jobtrackerhost:9010</value>
            </property>
            
            <property>
                <name>queueName</name>
                <value>default</value>
            </property>
            
            <property>
                <name>oozie.use.system.libpath</name>
                <value>true</value>
            </property>
            
            <property>
                <name>hiveScript</name>
                <value>$hiveScript</value>
            </property>
            
            <property>
                <name>hiveTableName</name>
                <value>$hiveTableName</value>
            </property>
            
            <property>
                <name>hiveDataFolder</name>
                <value>$hiveDataFolder</value>
            </property>
            
            <property>
                <name>hiveOutputFolder</name>
                <value>$hiveOutputFolder</value>
            </property>
            
            <property>
                <name>sqlDatabaseConnectionString</name>
                <value>"$sqlDatabaseConnectionString"</value>
            </property>
            
            <property>
                <name>sqlDatabaseTableName</name>
                <value>$SQLDatabaseTableName</value>
            </property>
            
            <property>
                <name>user.name</name>
                <value>admin</value>
            </property>
            
            <property>
                <name>oozie.wf.application.path</name>
                <value>$oozieWFPath</value>
            </property>
            
         </configuration>
         "@

4.  스크립트에 다음을 추가합니다. 이 부분은 Oozie 웹 서비스 상태를 검사합니다.

         Write-Host "Checking Oozie server status..." -ForegroundColor Green
         $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/admin/status"
         $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds -OutVariable $OozieServerStatus 
            
         $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
         $oozieServerSatus = $jsonResponse[0].("systemMode")
         Write-Host "Oozie server status is $oozieServerSatus..."

5.  스크립트에 다음을 추가합니다. 이 부분은 Oozie 작업을 만들고 시작합니다.

         # create Oozie job
         Write-Host "Sending the following Payload to the cluster:" -ForegroundColor Green
         Write-Host "`n--------`n$OoziePayload`n--------"
         $clusterUriCreateJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/jobs"
         $response = Invoke-RestMethod -Method Post -Uri $clusterUriCreateJob -Credential $creds -Body $OoziePayload -ContentType "application/xml" -OutVariable $OozieJobName #-debug

         $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
         $oozieJobId = $jsonResponse[0].("id")
         Write-Host "Oozie job id is $oozieJobId..."

         # start Oozie job
         Write-Host "Starting the Oozie job $oozieJobId..." -ForegroundColor Green
         $clusterUriStartJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?action=start"
         $response = Invoke-RestMethod -Method Put -Uri $clusterUriStartJob -Credential $creds | Format-Table -HideTableHeaders #-debug

6.  스크립트에 다음을 추가합니다. 이 부분은 Oozie 작업 상태를 검사합니다.

         # get job status
         Write-Host "Sleeping for $waitTimeBetweenOozieJobStatusCheck seconds until the job metadata is populated in the Oozie metastore..." -ForegroundColor Green
         Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck

         Write-Host "Getting job status and waiting for the job to complete..." -ForegroundColor Green
         $clusterUriGetJobStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "
         show=info"
         $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $creds 
         $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
         $JobStatus = $jsonResponse[0].("status")

         while($JobStatus -notmatch "SUCCEEDED|KILLED")
         {
             Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state...waiting $waitTimeBetweenOozieJobStatusCheck seconds for the job to complete..."
             Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck
             $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $creds 
             $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
             $JobStatus = $jsonResponse[0].("status")
         }

         Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state!" -ForegroundColor Green

7.  HDinsight 클러스터 버전이 2.1이면 "https://$clusterName.azurehdinsight.net:443/oozie/v2/"를 "https://$clusterName.azurehdinsight.net:443/oozie/v1/"로 바꿉니다. HDInsight 클러스터 버전 2.1은 웹 서비스의 버전 2를 지원하지 않습니다.

8.  **스크립트 실행**을 클릭하거나 **F5** 키를 눌러 스크립트를 실행합니다. 다음과 유사하게 출력됩니다.

    ![자습서 실행 워크플로 출력](./media/hdinsight-use-oozie/HDI.UseOozie.RunWF.Output.png)

9.  SQL 데이터베이스에 연결하여 내보낸 데이터를 확인합니다.

**작업 오류 로그를 검사하려면**

워크플로의 문제를 해결하려면 Oozie 로그 파일을 클러스터 headnode의 *C:\\apps\\dist\\oozie-3.3.2.1.3.2.0-05\\oozie-win-distro\\logs\\Oozie.log* 또는 *C:\\apps\\dist\\oozie-4.0.0.2.0.7.0-1528\\oozie-win-distro\\logs\\Oozie.log*에서 찾을 수 있습니다. RDP에 대한 자세한 내용은 [관리 포털을 사용하여 HDInsight 클러스터 관리](/ko-kr/documentation/articles/hdinsight-administer-use-management-portal/)를 참조하십시오.

**자습서를 다시 실행하려면**

워크플로를 다시 실행하려면 다음을 수행해야 합니다.

-   Hive 스크립트 출력 파일 삭제
-   log4jLogsCount 테이블의 데이터 삭제

다음은 사용할 수 있는 PowerShell 스크립트 샘플입니다.

    $storageAccountName = "<WindowsAzureStorageAccountName>"
    $containerName = "<ContainerName>"

    #SQL database variables
    $sqlDatabaseServer = "<SQLDatabaseServerName>"
    $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
    $sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>"
    $sqlDatabaseName = "<SQLDatabaseName>"
    $sqlDatabaseTableName = "log4jLogsCount"

    Write-host "Delete the Hive script output file ..." -ForegroundColor Green
    $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey
    Remove-AzureStorageBlob -Context $destContext -Blob "tutorials/useoozie/output/000000_0" -Container $containerName

    Write-host "Delete all the records from the log4jLogsCount table ..." -ForegroundColor Green
    $conn = New-Object System.Data.SqlClient.SqlConnection
    $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabaseLoginPassword;Encrypt=true;Trusted_Connection=false;"
    $conn.open()
    $cmd = New-Object System.Data.SqlClient.SqlCommand
    $cmd.connection = $conn
    $cmd.commandtext = "delete from $sqlDatabaseTableName"
    $cmd.executenonquery()

    $conn.close()

다음 단계
---------

이 자습서에서는 Oozie 워크플로를 정의하는 방법 및 Azure PowerShell을 사용하여 Oozie 작업을 실행하는 방법을 알아보았습니다. 자세한 내용은 다음 문서를 참조하십시오.

-   [HDInsight에서 시간 기준의 Oozie 코디네이터 사용](../hdinsight-use-oozie-coordinator-time/)
-   [HDInsight 시작](/ko-kr/documentation/articles/hdinsight-get-started/)
-   [HDInsight Emulator 시작](/ko-kr/documentation/articles/hdinsight-get-started-emulator/)
-   [HDInsight에서 Azure Blob 저장소 사용](/ko-kr/documentation/articles/hdinsight-use-blob-storage/)
-   [PowerShell을 사용하여 HDInsight 관리](/ko-kr/documentation/articles/hdinsight-administer-use-powershell/)
-   [HDInsight에 데이터 업로드](/ko-kr/documentation/articles/hdinsight-upload-data/)
-   [HDInsight에서 Sqoop 사용](../hdinsight-use-sqoop/)
-   [HDInsight에서 Hive 사용](/ko-kr/documentation/articles/hdinsight-use-hive/)
-   [HDInsight에서 Pig 사용](/ko-kr/documentation/articles/hdinsight-use-pig/)
-   [HDInsight용 C\# Hadoop 스트리밍 작업 개발](/ko-kr/documentation/articles/hdinsight-hadoop-develop-deploy-streaming-jobs/)
-   [HDInsight용 Java MapReduce 프로그램 개발](/ko-kr/documentation/articles/hdinsight-develop-deploy-java-mapreduce/)

