<properties
	pageTitle="HDInsight에서 Hadoop Oozie 사용 | Microsoft Azure"
	description="빅데이터 서비스인 HDInsight에서 Hadoop Oozie를 사용하는 방법을 알아봅니다. 또한 Oozie 워크플로를 정의하고 Oozie 작업을 제출하는 방법에 대해서도 살펴봅니다."
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/28/2015"
	ms.author="jgao"/>


# Hadoop과 함께 Oozie를 사용하여 HDInsight에서 워크플로 정의 및 실행

[AZURE.INCLUDE [oozie-selector](../../includes/hdinsight-oozie-selector.md)]

##개요
워크플로를 정의하고 HDInsight에서 워크플로를 실행하기 위해 Apache Oozie를 사용하는 방법을 알아보세요. Oozie 코디네이터에 대해 알아보려면 [HDInsight에서 시간 기준의 Oozie 코디네이터 사용][hdinsight-oozie-coordinator-time]을 참조하세요. Azure 데이터 팩터리를 알아보려면 [데이터 팩터리에서 Pig 및 Hive 사용][azure-data-factory-pig-hive]을 참조하세요.

##Oozie 정의

Apache Oozie는 Hadoop 작업을 관리하는 워크플로/코디네이션 시스템입니다. Hadoop 스택과 통합되며 Apache MapReduce, Apache Pig, Apache Hive, Apache Sqoop에 Hadoop 작업을 지원합니다. 또한 Java 프로그램이나 셸 스크립트와 같이 시스템에 고유한 작업을 예약하는 데 사용할 수 있습니다.

두 동작을 포함하는 해당 자습서의 지침에 따라 사용자가 구현할 워크플로입니다:

![워크플로 다이어그램][img-workflow-diagram]

1. log4j 파일에서 각 로그 수준 유형의 수를 계산하는 HiveQL 스크립트를 실행하는 Hive 작업. 각 log4j 파일은 유형과 심각도를 표시하는 [LOG LEVEL] 필드가 포함된 필드의 줄로 구성되어 있습니다. 예를 들어 다음과 같습니다.

		2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
		2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
		2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
		...

	Hive 스크립트 출력은 다음과 같습니다.

		[DEBUG] 434
		[ERROR] 3
		[FATAL] 1
		[INFO]  96
		[TRACE] 816
		[WARN]  4

	Hive에 대한 자세한 내용은 [HDInsight와 함께 Hive 사용][hdinsight-use-hive]을 참조하세요.

2.  HiveQL 출력을 Azure SQL 데이터베이스의 테이블에 내보내는 Sqoop 작업. Sqoop에 대한 자세한 내용은 [HDInsight에서 Hadoop Sqoop 사용][hdinsight-use-sqoop]을 참조하세요.

> [AZURE.NOTE]HDInsight 클러스터에서 지원되는 Oozie 버전에 대해서는 [HDInsight에서 제공하는 Hadoop 클러스터 버전의 새로운 기능][hdinsight-versions]을 참조하세요.

> [AZURE.NOTE]이 자습서는 HDInsight 버전 3.0 및 2.1에서 작동합니다. 이 자습서는 HDInsight 에뮬레이터에서 테스트되지 않았습니다.


##필수 조건

이 자습서를 시작하기 전에 다음이 있어야 합니다.

- **Azure PowerShell이 포함된 워크스테이션**. [Azure PowerShell 설치 및 사용](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/)을 참조하세요. Windows PowerShell 스크립트를 실행하려면 관리자로서 실행하고 실행 정책을 *RemoteSigned*로 설정해야 합니다. 자세한 내용은 [Windows PowerShell 스크립트 실행][powershell-script]을 참조하세요.
- **HDInsight 클러스터**. HDInsight 클러스터 만들기에 대한 내용은 [사용자 지정 옵션을 사용하여 HDInsight에서 Hadoop 클러스터를 프로비전][hdinsight-provision] 또는 [휴대폰 사용을 분석하기 위해 HDInsight에서 Hive와 함께 Hadoop을 사용 시작][hdinsight-get-started]을 참조하세요. 자습서를 완료하려면 다음 데이터가 필요합니다.

	<table border = "1">
	<tr><th>클러스터 속성</th><th>Windows PowerShell 변수 이름</th><th>값</th><th>설명</th></tr>
	<tr><td>HDInsight 클러스터 이름</td><td>$clusterName</td><td></td><td>이 자습서를 실행할 HDInsight 클러스터입니다.</td></tr>
	<tr><td>HDInsight 클러스터 사용자 이름</td><td>$clusterUsername</td><td></td><td>HDInsight 클러스터 사용자 이름. </td></tr>
	<tr><td>HDInsight 클러스터 사용자 암호 </td><td>$clusterPassword</td><td></td><td>HDInsight 클러스터 사용자의 암호입니다.</td></tr>
	<tr><td>Azure 저장소 계정 이름</td><td>$storageAccountName</td><td></td><td>Azure 저장소 계정은 HDInsight 클러스터에서 사용할 수 있습니다. 이 자습서의 경우 클러스터 프로비전 프로세스 도중에 지정된 기본 저장소 계정을 사용합니다.</td></tr>
	<tr><td>Azure Blob 컨테이너 이름</td><td>$containerName</td><td></td><td>이 예에서는 기본 HDInsight 클러스터 파일 시스템에 사용되는 Blob 컨테이너 이름을 사용합니다. 기본적으로 컨테이너 이름은 HDInsight 클러스터 이름과 동일합니다.</td></tr>
	</table>

- **Azure SQL 데이터베이스**입니다. 워크스테이션에서 액세스할 수 있도록 Azure SQL 데이터베이스의 방화벽 규칙을 구성해야 합니다. Azure SQL 데이터베이스 만들기 및 방화벽 구성에 대한 자세한 내용은 [Microsoft Azure SQL 데이터베이스 사용 시작][sqldatabase-get-started]을 참조하세요. 해당 문서에는 이 자습서에 필요한 Azure SQL 데이터베이스 테이블을 만들기 위한 Windows PowerShell 스크립트가 있습니다.

	<table border = "1">
	<tr><th>SQL 데이터베이스 속성</th><th>Windows PowerShell 변수 이름</th><th>값</th><th>설명</th></tr>
	<tr><td>SQL 데이터베이스 서버 이름</td><td>$sqlDatabaseServer</td><td></td><td>Sqoop에서 데이터를 내보낼 Azure SQL 데이터베이스입니다. </td></tr>
	<tr><td>SQL 데이터베이스 로그인 이름</td><td>$sqlDatabaseLogin</td><td></td><td>Azure SQL 데이터베이스 로그인 이름.</td></tr>
	<tr><td>SQL 데이터베이스 로그인 암호</td><td>$sqlDatabaseLoginPassword</td><td></td><td>Azure SQL 데이터베이스 로그인 암호.</td></tr>
	<tr><td>SQL 데이터베이스 이름</td><td>$sqlDatabaseName</td><td></td><td>Sqoop에서 데이터를 내보낼 Azure SQL 데이터베이스입니다. </td></tr>
	</table>

	> [AZURE.NOTE]기본적으로 Azure SQL 데이터베이스는 Azure HDInsight 같은 Azure 서비스로부터의 연결을 허용합니다. 이 방화벽 설정을 사용하지 않도록 설정한 경우 Azure 포털에서 사용하도록 설정해야 합니다. SQL 데이터베이스 만들기 및 방화벽 규칙 구성에 대한 지침은 [Azure SQL 데이터베이스 만들기 및 구성법][sqldatabase-create-configue]을 참조하세요.


> [AZURE.NOTE]위의 테이블에 값을 작성하면 이 자습서를 완료하는 데 유용합니다.


##Oozie 워크플로 및 관련 HiveQL 스크립트 정의

Oozie 워크플로 정의는 hPDL(XML 프로세스 정의 언어)로 작성되었습니다. 기본 워크플로 파일 이름은 *workflow.xml*입니다. 워크플로 파일을 로컬로 저장하고 이 자습서의 뒷부분에 나오는 Windows PowerShell을 사용하여 HDInsight 클러스터에 배포합니다.

워크플로의 Hive 작업은 HiveQL 스크립트 파일을 호출합니다. 이 스크립트 파일에는 세 개의 HiveQL 문이 포함되어 있습니다.

1. **DROP TABLE 문**은 log4j Hive 테이블이 있는 경우 이 테이블을 삭제합니다.
2. **CREATE TABLE 문**은 log4j 로그 파일 위치를 가리키는 log4j Hive 외부 테이블을 만듭니다. 필드 구분 기호는 ","입니다. 기본 줄 구분 기호는 "\n"입니다. Oozie 워크플로를 여러 번 실행하려는 경우 데이터 파일이 원래 위치에서 제거되지 않도록 하는 데 Hive 외부 테이블이 사용됩니다.
3. **INSERT OVERWRITE 문**은 log4j Hive 테이블에서 각 로그 수준 유형의 수를 계산하고 그 출력 결과를 Azure 저장소의 blob에 저장합니다.

알려진 Hive 경로 문제가 있습니다. Oozie 작업을 제출하는 경우에 이 문제와 충돌할 수 있습니다. TechNet Wiki에서 해결을 위한 지침을 찾을 수 있습니다: [HDInsight 하이브 오류:이름을 바꿀 수 없습니다][technetwiki-hive-error].

**HiveQL 스크립트 파일을 워크플로에서 호출하도록 정의하려면**

1. 다음 내용이 포함된 텍스트 파일을 만듭니다.

		DROP TABLE ${hiveTableName};
		CREATE EXTERNAL TABLE ${hiveTableName}(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
		INSERT OVERWRITE DIRECTORY '${hiveOutputFolder}' SELECT t4 AS sev, COUNT(*) AS cnt FROM ${hiveTableName} WHERE t4 LIKE '[%' GROUP BY t4;

	스크립트에서 사용되는 3개의 변수가 있습니다.

	- ${hiveTableName}
	- ${hiveDataFolder}
	- ${hiveOutputFolder}

	워크플로 정의 파일(이 자습서에서는 workflow.xml)은 런타임 시 이러한 값을 이 HiveQL 스크립트에 전달합니다.

2. **ANSI(ASCII)** 인코딩을 사용하여 파일을 **C:\Tutorials\UseOozie\useooziewf.hql**로 저장하세요. (텍스트 편집기에서 이 옵션을 제공하지 않는 경우 메모장을 사용하세요.) 이 스크립트 파일은 자습서의 뒷부분에 나오는 HDInsight 클러스터에 배포됩니다.



**워크플로를 정의하려면**

1. 다음 콘텐츠가 있는 텍스트 파일을 만듭니다.

		<workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
		    <start to = "RunHiveScript"/>

		    <action name="RunHiveScript">
		        <hive xmlns="uri:oozie:hive-action:0.2">
		            <job-tracker>${jobTracker}</job-tracker>
		            <name-node>${nameNode}</name-node>
		            <configuration>
		                <property>
		                    <name>mapred.job.queue.name</name>
		                    <value>${queueName}</value>
		                </property>
		            </configuration>
		            <script>${hiveScript}</script>
			    	<param>hiveTableName=${hiveTableName}</param>
		            <param>hiveDataFolder=${hiveDataFolder}</param>
		            <param>hiveOutputFolder=${hiveOutputFolder}</param>
		        </hive>
		        <ok to="RunSqoopExport"/>
		        <error to="fail"/>
		    </action>

		    <action name="RunSqoopExport">
		        <sqoop xmlns="uri:oozie:sqoop-action:0.2">
		            <job-tracker>${jobTracker}</job-tracker>
		            <name-node>${nameNode}</name-node>
		            <configuration>
		                <property>
		                    <name>mapred.compress.map.output</name>
		                    <value>true</value>
		                </property>
		            </configuration>
			    <arg>export</arg>
			    <arg>--connect</arg>
			    <arg>${sqlDatabaseConnectionString}</arg>
			    <arg>--table</arg>
			    <arg>${sqlDatabaseTableName}</arg>
			    <arg>--export-dir</arg>
			    <arg>${hiveOutputFolder}</arg>
			    <arg>-m</arg>
			    <arg>1</arg>
			    <arg>--input-fields-terminated-by</arg>
			    <arg>"\001"</arg>
		        </sqoop>
		        <ok to="end"/>
		        <error to="fail"/>
		    </action>

		    <kill name="fail">
		        <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
		    </kill>

		   <end name="end"/>
		</workflow-app>

	워크플로에 2가지 작업이 정의되어 있습니다. 시작 작업은 *RunHiveScript*입니다. 작업이 성공적으로 실행되면 다음 작업은 *RunSqoopExport*입니다.

	RunHiveScript에는 몇 개의 변수가 있습니다. Windows PowerShell을 사용하여 워크스테이션에서 Oozie 작업을 제출할 때 이러한 값을 전달합니다.

	<table border = "1">
	<tr><th>워크플로 변수</th><th>설명</th></tr>
	<tr><td>${jobTracker}</td><td>Hadoop 작업 추적기의 URL을 지정합니다. HDInsight 버전 2.1 및 3.0에서 <strong>jobtrackerhost: 9010</strong>를 사용하세요.</td></tr>
	<tr><td>${nameNode}</td><td>Hadoop 이름 노드의 URL을 지정합니다. 기본 파일 시스템 주소를 사용하세요. 예: <i>wasb://&lt;containerName>@&lt;storageAccountName>.blob.core.windows.net</i>.</td></tr>
	<tr><td>${queueName}</td><td>작업을 제출할 큐 이름을 지정합니다. <strong>기본</strong>을 사용하세요.</td></tr>
	</table>

	<table border = "1">
	<tr><th>Hive 작업 변수</th><th>설명</th></tr>
	<tr><td>${hiveDataFolder}</td><td>Hive Create Table 명령의 소스 디렉터리를 지정합니다.</td></tr>
	<tr><td>${hiveOutputFolder}</td><td>INSERT OVERWRITE 문의 출력 폴더를 지정합니다.</td></tr>
	<tr><td>${hiveTableName}</td><td>log4j 데이터 파일을 참조하는 Hive 테이블의 이름을 지정합니다.</td></tr>
	</table>

	<table border = "1">
	<tr><th>Sqoop 작업 변수</th><th>설명</th></tr>
	<tr><td>${sqlDatabaseConnectionString}</td><td>SQL 데이터베이스 연결 문자열을 지정합니다.</td></tr>
	<tr><td>${sqlDatabaseTableName}</td><td>데이터를 내보낼 SQL 데이터베이스 테이블을 지정합니다.</td></tr>
	<tr><td>${hiveOutputFolder}</td><td>Hive INSERT OVERWRITE 문의 출력 폴더를 지정합니다. 이 폴더는 Sqoop 내보내기(내보내기 디렉터리)와 동일한 폴더입니다.</td></tr>
	</table>

	Oozie 워크플로 및 워크플로 작업 사용에 대한 자세한 내용은 [Apache Oozie 4.0 설명서][apache-oozie-400](HDInsight 버전 3.0의 경우) 또는 [Apache Oozie 3.3.2 설명서][apache-oozie-332](HDInsight 버전 2.1의 경우)를 참조하세요.

2. ANSI(ASCII) 인코딩을 사용하여 파일을**C:\Tutorials\UseOozie\workflow.xml**로 저장하세요. (텍스트 편집기에서 이 옵션을 제공하지 않는 경우 메모장을 사용하세요.)

##Oozie 프로젝트 배포 및 자습서 준비

Windows PowerShell 스크립트를 실행하여 다음을 수행합니다:

- HiveQL 스크립트(useoozie.hql)를 Azure 저장소에 복사합니다 (wasb:///tutorials/useoozie/useoozie.hql).
- workflow.xml를 wasb:///tutorials/useoozie/workflow.xml에 복사합니다.
- 데이터 파일(/ example/data/sample.log)을 wasb:///tutorials/useoozie/data/sample.log에 복사합니다.
- Sqoop 내보내기 데이터를 저장할 Azure SQL 데이터베이스 테이블을 만듭니다. 테이블 이름은 *log4jLogCount*입니다.

**HDInsight 저장소 이해**

HDInsight는 데이터 저장소로 Azure 저장소의 blob을 사용합니다. 자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용][hdinsight-storage]을 참조하세요.

HDInsight 클러스터를 프로비전할 때 Azure 저장소 계정 및 이 계정에서 오는 특정 blob 컨테이너가 HDFS의 경우와 같이 기본 파일 시스템으로 지정됩니다. 프로비전 프로세스 중에 이 저장소 계정 외에도 동일한 Azure 구독 또는 다른 Azure 구독에서 저장소 계정을 추가할 수 있습니다.프로비전 프로세스 중에 이 저장소 계정 외에도 동일한 Azure 구독 또는 다른 Azure 구독에서 저장소 계정을 추가할 수 있습니다. 저장소 계정 추가에 대한 지침은 [HDInsight에서 Hadoop 클러스터 프로비전][hdinsight-provision]을 참조하세요. 이 자습서에 사용된 Windows PowerShell 스크립트를 간소화하려면 모든 파일이 */tutorials/useoozie*에 있는 기본 파일 저장 컨테이너에 저장되어야 합니다. 기본적으로 이 컨테이너 이름은 HDInsight 클러스터 이름과 동일합니다. 구문은 다음과 같습니다:

	wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [AZURE.NOTE]HDInsight 클러스터 버전 3.0에서는 *wasb://* 구문만 지원됩니다. 이전 *asv://* 구문은 HDInsight 2.1 및 1.6 클러스터에서 지원되지만, HDInsight 3.0 클러스터 이상 버전에서는 지원되지 않습니다.

> [AZURE.NOTE]wasb:// 경로는 가상 경로입니다. 자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용][hdinsight-storage]을 참조하세요.

기본 파일 시스템 컨테이너에 저장된 파일은 다음 URI 중 아무거나 사용하여 HDInsight에서 액세스할 수 있습니다(다음 코드는 예제로 workflow.xml 사용):

	wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/workflow.xml
	wasb:///tutorials/useoozie/workflow.xml
	/tutorials/useoozie/workflow.xml

저장소 계정에서 바로 파일에 액세스하려는 경우 파일의 Blob 이름은 다음과 같습니다.

	tutorials/useoozie/workflow.xml

**Hive 내부 테이블 및 외부 테이블 이해**

Hive 내부 테이블 및 외부 테이블에 대해 알아야 할 사항이 몇 가지 있습니다:

- CREATE TABLE 명령은 관리되는 테이블이라고도 하는 내부 테이블을 만듭니다. 데이터 파일은 기본 컨테이너에 있어야 합니다.
- CREATE TABLE 명령은 데이터 파일을 기본 컨테이너의 /hive/warehouse/<TableName> 폴더로 이동합니다.
- CREATE EXTERNAL TABLE 명령은 외부 테이블을 만듭니다. 데이터 파일은 기본 컨테이너가 아닌 곳에 있을 수 있습니다.
- CREATE EXTERNAL TABLE 명령은 데이터 파일을 이동하지 않습니다.
- CREATE EXTERNAL TABLE 명령은 LOCATION 절에 지정된 폴더 아래에 하위 폴더를 허용하지 않습니다. 이는 자습서에서 sample.log 파일의 복사본을 만들기 때문입니다.

자세한 내용은 [HDInsight: Hive 내부 및 외부 테이블 소개][cindygross-hive-tables]를 참조하세요.

**자습서를 준비하려면**

1. Windows PowerShell ISE를 엽니다. (Windows 8 시작 화면에서 **PowerShell_ISE**를 입력한 후 **Windows PowerShell ISE**를 클릭하면 됩니다. 자세한 내용은 [Windows 8 및 Windows에서 Windows PowerShell 시작][powershell-start]을 참조하세요.)
2. 아래 창에서 다음 명령을 실행하여 Azure 구독에 연결합니다.

		Add-AzureAccount

	Azure 계정 자격 증명을 입력하라는 메시지가 표시됩니다. 구독 연결을 추가하는 이 메서드의 시간이 초과하고 12시간 후에 cmdlet을 다시 실행해야 합니다.

	> [AZURE.NOTE]여러 Azure 구독이 있는 경우 기본 구독이 사용하려는 구독이 아니면 <strong>Select-AzureSubscription</strong> cmdlet을 사용하여 현재 구독을 선택합니다.

3. 다음 스크립트를 스크립트 창에 복사한 다음 처음 6개의 변수를 설정합니다:

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


	변수에 대한 자세한 설명은 이 자습서에서 [필수 조건](#prerequisites) 섹션을 참조하세요.

3. 다음을 스크립트 창에서 스크립트에 추가합니다.

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

4. **스크립트 실행**을 클릭하거나 **F5** 키를 눌러 스크립트를 실행합니다. 다음과 유사하게 출력됩니다:

	![자습서 준비 출력][img-preparation-output]

##Oozie 프로젝트 실행

Azure PowerShell은 Oozie 작업을 정의하는 데 현재 어떤 cmdlet도 제공하지 않습니다. **Invoke-RestMethod** cmdlet을 사용하여 Oozie 웹 서비스를 불러올 수 있습니다. Oozie 웹 서비스 API는 HTTP REST JSON API입니다. Oozie 웹 서비스 API에 대한 자세한 내용은 [Apache Oozie 4.0 설명서][apache-oozie-400](HDInsight 버전 3.0의 경우) 또는 [Apache Oozie 3.3.2 설명서][apache-oozie-332](HDInsight 버전 2.1의 경우)를 참조하세요.

**Oozie 작업을 제출하려면**

1. Windows PowerShell ISE를 엽니다. (Windows 8 시작 화면에서 **PowerShell\_ISE**를 입력한 후 **Windows PowerShell ISE**를 클릭하면 됩니다. 자세한 내용은 [Windows 8 및 Windows에서 Windows PowerShell 시작][powershell-start]을 참조하세요.)

3. 다음 스크립트를 스크립트 창에 복사한 다음 처음 10개의 변수를 설정합니다($storageUri 변수는 건너뜀).

		#HDInsight cluster variables
		$clusterName = "<HDInsightClusterName>"
		$clusterUsername = "<HDInsightClusterUsername>"
		$clusterPassword = "<HDInsightClusterUserPassword>"

		#Azure Blob storage variables
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


	변수에 대한 자세한 설명은 이 자습서에서 [필수 조건](#prerequisites) 섹션을 참조하세요.

3. 스크립트에 다음을 추가합니다. 이 스크립트는 Oozie 페이로드를 정의합니다.

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
		       <value>";$sqlDatabaseConnectionString";</value>
		   </property>

		   <property>
		       <name>sqlDatabaseTableName</name>
		       <value>$SQLDatabaseTableName</value>
		   </property>

		   <property>
		       <name>user.name</name>
		       <value>$clusterUsername</value>
		   </property>

		   <property>
		       <name>oozie.wf.application.path</name>
		       <value>$oozieWFPath</value>
		   </property>

		</configuration>
		"@

4. 스크립트에 다음을 추가합니다. 이 스크립트는 Oozie 웹 서비스 상태를 검사합니다.

	    Write-Host "Checking Oozie server status..." -ForegroundColor Green
	    $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/admin/status"
	    $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds -OutVariable $OozieServerStatus

	    $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
	    $oozieServerSatus = $jsonResponse[0].("systemMode")
	    Write-Host "Oozie server status is $oozieServerSatus..."

5. 스크립트에 다음을 추가합니다. 이 부분은 Oozie 작업을 만들고 시작합니다.

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

6. 스크립트에 다음을 추가합니다. 이 스크립트는 Oozie 작업 상태를 검사합니다.

	    # get job status
	    Write-Host "Sleeping for $waitTimeBetweenOozieJobStatusCheck seconds until the job metadata is populated in the Oozie metastore..." -ForegroundColor Green
	    Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck

	    Write-Host "Getting job status and waiting for the job to complete..." -ForegroundColor Green
	    $clusterUriGetJobStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?show=info"
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

7. HDinsight 클러스터 버전이 2.1이면 "https://$clusterName.azurehdinsight.net:443/oozie/v2/"를 "https://$clusterName.azurehdinsight.net:443/oozie/v1/"로 바꿉니다. HDInsight 클러스터 버전 2.1은 웹 서비스의 버전 2를 지원하지 않습니다.

8. **스크립트 실행**을 클릭하거나 **F5** 키를 눌러 스크립트를 실행합니다. 다음과 유사하게 출력됩니다:

	![자습서 실행 워크플로 출력][img-runworkflow-output]

8. Azure SQL 데이터베이스에 연결하여 내보낸 데이터를 확인합니다.

**작업 오류 로그를 검사하려면**

워크플로의 문제를 해결하려면 Oozie 로그 파일을 클러스터 헤드 노드의 *C:\\apps\\dist\\oozie-3.3.2.1.3.2.0-05\\oozie-win-distro\\logs\\Oozie.log* 또는 *C:\\apps\\dist\\oozie-4.0.0.2.0.7.0-1528\\oozie-win-distro\\logs\\Oozie.log*에서 찾을 수 있습니다. RDP에 대한 자세한 내용은 [Azure Preview 포털을 사용하여 HDInsight의 Hadoop 클러스터 관리][hdinsight-admin-portal]를 참조하세요.

**자습서를 다시 실행하려면**

워크플로를 다시 실행하려면 다음을 삭제해야 합니다:

- Hive 스크립트 출력 파일
- log4jLogsCount 테이블의 데이터

다음은 사용할 수 있는 Windows PowerShell 스크립트 샘플입니다:

	$storageAccountName = "<AzureStorageAccountName>"
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


##다음 단계
이 자습서에서는 Oozie 워크플로를 정의하는 방법 및 Windows PowerShell을 사용하여 Oozie 작업을 실행하는 방법을 알아보았습니다. 자세한 내용은 다음 문서를 참조하세요.

- [HDInsight에서 시간 기준의 Oozie 코디네이터 사용][hdinsight-oozie-coordinator-time]
- [휴대폰 사용을 분석하기 위해 HDInsight에서 Hive와 함께 Hadoop 사용 시작][hdinsight-get-started]
- [HDInsight Emulator 시작][hdinsight-get-started-emulator]
- [HDInsight에서 Azure Blob 저장소 사용][hdinsight-storage]
- [PowerShell을 사용하여 HDInsight 관리][hdinsight-admin-powershell]
- [HDInsight에서 Hadoop 작업용 데이터 업로드][hdinsight-upload-data]
- [HDInsight에서 Hadoop과 Sqoop 사용][hdinsight-use-sqoop]
- [HDInsight에서 Hadoop과 Hive 사용][hdinsight-use-hive]
- [HDInsight에서 Hadoop과 Pig 사용][hdinsight-use-pig]
- [HDInsight용 C# Hadoop 스트리밍 작업 개발][hdinsight-develop-streaming-jobs]
- [HDInsight용 Java MapReduce 프로그램 개발][hdinsight-develop-mapreduce]


[hdinsight-cmdlets-download]: http://go.microsoft.com/fwlink/?LinkID=325563



[azure-data-factory-pig-hive]: ../data-factory/data-factory-pig-hive-activities.md
[hdinsight-oozie-coordinator-time]: hdinsight-use-oozie-coordinator-time.md
[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md


[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-get-started-emulator]: ../hdinsight-get-started-emulator.md

[hdinsight-develop-streaming-jobs]: hdinsight-hadoop-develop-deploy-streaming-jobs.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce.md

[sqldatabase-create-configue]: ../sql-database-create-configure.md
[sqldatabase-get-started]: ../sql-database-get-started.md

[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]: ../storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
[apache-oozie-400]: http://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: http://oozie.apache.org/docs/3.3.2/

[powershell-download]: http://azure.microsoft.com/downloads/
[powershell-about-profiles]: http://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: ../powershell-install-configure.md
[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-script]: https://technet.microsoft.com/en-us/library/ee176961.aspx

[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.Preparation.Output1.png
[img-runworkflow-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.RunWF.Output.png

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx

<!---HONumber=Oct15_HO3-->