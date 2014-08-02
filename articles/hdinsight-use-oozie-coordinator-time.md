<properties linkid="hdinsight-use-time-based-oozie-coordinator-with-hdinsight" urlDisplayName="Use time-based Oozie Coordinator with HDInsight" pageTitle="Use time-based Oozie Coordinator with HDInsight | Microsoft Azure" metaKeywords="" description="Use time-based Oozie Coordinator with HDInsight, a big data solution. Learn how to define Oozie workflows and coordinators, and submit Oozie coordinator jobs." metaCanonical="" services="hdinsight" documentationCenter="" title="Use time-based Oozie Coordinator with HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

HDInsight에서 시간 기준 Oozie 코디네이터 사용
=============================================

워크플로 및 코디네이터를 정의하는 방법, 시간을 기준으로 코디네이터 작업을 트리거하는 방법을 알아봅니다. 이 문서를 시작하기 전에 [HDInsight에서 Oozie 사용](../hdinsight-use-oozie/)을 확인하는 것이 도움이 됩니다.

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

Apache Oozie는 Hadoop 작업을 관리하는 워크플로/조정 시스템입니다. Hadoop 스택과 통합되며 Apache MapReduce, Apache Pig, Apache Hive 및 Apache Sqoop용 Hadoop 작업을 지원합니다. Java 프로그램이나 셸 스크립트와 같이 시스템에 특정한 작업을 예약하는 데에도 사용할 수 있습니다.

구현할 워크플로는 2가지 작업을 포함합니다.

![워크플로 다이어그램](./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.Workflow.Diagram.png)

1.  Hive 작업이 HiveQL 스크립트를 실행하여 log4j 로그 파일에서 각 로그 수준 유형이 나타나는 횟수를 계산합니다. 각 log4j 로그는 유형과 심각도를 표시하는 [LOG LEVEL] 필드가 포함된 필드의 줄로 구성되어 있습니다. 예를 들면 다음과 같습니다.

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

    Hive에 대한 자세한 내용은 [HDInsight에서 Hive 사용](/en-us/documentation/articles/hdinsight-use-hive/)을 참조하십시오.

2.  HiveQL 작업 출력을 Azure SQL 데이터베이스의 테이블에 내보내는 Sqoop 작업입니다. Sqoop에 대한 자세한 내용은 [HDInsight에서 Sqoop 사용](../hdinsight-use-sqoop/)을 참조하십시오.

> [WACOM.NOTE] HDInsight 클러스터에서 지원되는 Oozie 버전에 대해서는 [HDInsight에서 제공하는 클러스터 버전의 새로운 기능](/en-us/documentation/articles/hdinsight-component-versioning/)을 참조하십시오.

> [WACOM.NOTE] 이 자습서는 HDInsight 클러스터 버전 2.1 및 3.0에 적용됩니다. 이 문서는 HDInsight 에뮬레이터에 대해 테스트되었습니다.

필수 조건
---------

이 자습서를 시작하기 전에 다음이 있어야 합니다.

-   Azure PowerShell이 설치 및 구성된 **워크스테이션**. 자세한 내용은 [Azure PowerShell 설치 및 구성](/en-us/manage/install-and-configure-windows-powershell/)을 참조하십시오. PowerShell 스크립트를 실행하려면 관리자로 Azure PowerShell을 실행하고 실행 정책을 *RemoteSigned*로 설정해야 합니다. [Windows PowerShell 스크립트 실행](http://technet.microsoft.com/en-us/library/ee176949.aspx)(영문)을 참조하십시오.
-   **HDInsight 클러스터**. HDInsight 클러스터 만들기에 대한 자세한 내용은 [HDInsight 클러스터 프로비전](/en-us/documentation/articles/hdinsight-provision-clusters/) 또는 [HDInsight 시작](/en-us/documentation/articles/hdinsight-get-started/)을 참조하십시오. 자습서를 완료하려면 다음 데이터가 필요합니다.

   <table data-morhtml="true" border="1">
  <tr data-morhtml="true"><th data-morhtml="true">클러스터 속성</th><th data-morhtml="true">PowerShell 변수 이름</th><th data-morhtml="true">값</th><th data-morhtml="true">설명</th></tr>
  <tr data-morhtml="true"><td data-morhtml="true">HDInsight 클러스터 이름</td><td data-morhtml="true">$clusterName</td><td data-morhtml="true"></td><td data-morhtml="true">이 자습서를 실행할 HDInsight 클러스터입니다.</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">HDInsight 클러스터 사용자 이름</td><td data-morhtml="true">$clusterUsername</td><td data-morhtml="true"></td><td data-morhtml="true">HDInsight 클러스터 사용자의 사용자 이름입니다. </td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">HDInsight 클러스터 사용자 암호 </td><td data-morhtml="true">$clusterPassword</td><td data-morhtml="true"></td><td data-morhtml="true">HDInsight 클러스터 사용자 암호입니다.</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">Azure 저장소 계정 이름</td><td data-morhtml="true">$storageAccountName</td><td data-morhtml="true"></td><td data-morhtml="true">Azure 저장소 계정은 HDInsight 클러스터에서 사용할 수 있습니다. 이 자습서의 경우 클러스터 프로비전 프로세스 도중에 지정된 기본 저장소 계정을 사용합니다.</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">Azure Blob 컨테이너 이름</td><td data-morhtml="true">$containerName</td><td data-morhtml="true"></td><td data-morhtml="true">이 예에서는 기본 HDInsight 클러스터 파일 시스템에 사용되는 Azure Blob 저장소 컨테이너를 사용합니다. 기본적으로 컨테이너 이름은 HDInsight 클러스터 이름과 동일합니다.</td></tr>
  </table>

-   **Azure SQL 데이터베이스**. 워크스테이션에서 액세스할 수 있도록 SQL 데이터베이스 서버의 방화벽 규칙을 구성해야 합니다. SQL 데이터베이스 만들기 및 방화벽 구성에 대한 자세한 내용은 [Azure SQL 데이터베이스 사용 시작](../sql-database-get-started/)을 참조하십시오. 이 문서에는 이 자습서에 필요한 SQL 데이터베이스 테이블을 만들기 위한 PowerShell 스크립트가 있습니다.

   <table data-morhtml="true" border="1">
  <tr data-morhtml="true"><th data-morhtml="true">SQL 데이터베이스 속성</th><th data-morhtml="true">PowerShell 변수 이름</th><th data-morhtml="true">값</th><th data-morhtml="true">설명</th></tr>
  <tr data-morhtml="true"><td data-morhtml="true">SQL 데이터베이스 서버 이름</td><td data-morhtml="true">$sqlDatabaseServer</td><td data-morhtml="true"></td><td data-morhtml="true">Sqoop이 데이터를 내보낼 SQL 데이터베이스 서버입니다. </td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">SQL 데이터베이스 로그인 이름</td><td data-morhtml="true">$sqlDatabaseLogin</td><td data-morhtml="true"></td><td data-morhtml="true">SQL 데이터베이스 로그인 이름입니다.</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">SQL 데이터베이스 로그인 암호</td><td data-morhtml="true">$sqlDatabaseLoginPassword</td><td data-morhtml="true"></td><td data-morhtml="true">SQL 데이터베이스 로그인 암호입니다.</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">SQL 데이터베이스 이름</td><td data-morhtml="true">$sqlDatabaseName</td><td data-morhtml="true"></td><td data-morhtml="true">Sqoop이 데이터를 내보낼 Azure SQL 데이터베이스입니다. </td></tr>
  </table>

    > [WACOM.NOTE] 기본적으로 Azure SQL 데이터베이스는 Azure HDInsight 같은 Azure 서비스로부터의 연결을 허용합니다. 이 방화벽 설정을 사용하지 않도록 설정한 경우 Azure 관리 포털에서 사용하도록 설정해야 합니다. SQL 데이터베이스 만들기 및 방화벽 규칙 구성에 대한 지침은 [SQL 데이터베이스 만들기 및 구성](../sql-database-create-configure/)을 참조하십시오.

> [WACOM.NOTE] 테이블에 값을 채우십시오. 이 자습서를 완료하는 데 유용합니다.

Oozie 워크플로 및 관련 HiveQL 스크립트 정의
-------------------------------------------

Oozie 워크플로 정의는 hPDL(XML Process Definition Language)로 작성됩니다. 기본 워크플로 파일 이름은 *workflow.xml*입니다. 이 워크플로 파일을 로컬에서 저장하고 이 자습서 뒷부분에서 Azure PowerShell을 사용하여 HDInsight 클러스터에 배포합니다.

워크플로의 Hive 작업은 HiveQL 스크립트 파일을 호출합니다. 스크립트 파일에는 3개의 HiveQL 문이 포함됩니다.

1.  **DROP TABLE 문**은 log4j Hive 테이블이 있는 경우 이 테이블을 삭제합니다.
2.  **CREATE TABLE 문**은 log4j 로그 파일 위치를 가리키는 log4j Hive 외부 테이블을 만듭니다.
3.  필드 구분 기호는 ","입니다. 기본 줄 구분 기호는 "\\n"입니다. Hive 외부 테이블은 Oozie 워크플로를 여러 번 실행하려는 경우 데이터 파일이 원래 위치에서 제거되지 않도록 하기 위해 사용됩니다.
4.  **INSERT OVERWRITE 문**은 log4j Hive 테이블에서 각 로그 수준 유형이 나타나는 횟수를 계산하고 출력을 Azure 저장소 - Blob(WASB) 위치에 저장합니다.

알려진 Hive 경로 문제가 있습니다. Oozie 작업을 제출할 때 이 문제가 발생합니다. 이 문제를 해결하기 위한 지침은 [TechNet Wiki](http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx)(영문)에서 확인할 수 있습니다.

**워크플로에서 호출할 HiveQL 스크립트 파일을 정의하려면**

1.  다음 콘텐츠가 있는 텍스트 파일을 만듭니다.

         DROP TABLE ${hiveTableName};
         CREATE EXTERNAL TABLE ${hiveTableName}(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
         INSERT OVERWRITE DIRECTORY '${hiveOutputFolder}' SELECT t4 AS sev, COUNT(*) AS cnt FROM ${hiveTableName} WHERE t4 LIKE '[%' GROUP BY t4;

    스크립트에서 사용되는 3개의 변수가 있습니다.

    -   \${hiveTableName}
    -   \${hiveDataFolder}
    -   \${hiveOutputFolder}

    워크플로 정의 파일(이 자습서의 workflow.xml)이 런타임에 이 값을 이 HiveQL 스크립트에 전달합니다.

2.  **ANSI(ASCII)** 인코딩을 사용하여 파일을 **C:\\Tutorials\\UseOozie\\useooziewf.hql**로 저장합니다(텍스트 편집기에 옵션이 없는 경우 메모장 사용). 자습서 뒷부분에서 이 스크립트 파일을 HDInsight 클러스터에 배포합니다.

**워크플로를 정의하려면**

1.  다음 콘텐츠가 있는 텍스트 파일을 만듭니다.

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
                 <arg>" <workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
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
        01"</arg>
                 </sqoop>
                 <ok to="end"/>
                 <error to="fail"/>
             </action>
            
             <kill name="fail">
                 <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
             </kill>
            
            <end name="end"/>
         </workflow-app>

    워크플로에 2가지 작업이 정의되어 있습니다. start-to 작업은 *RunHiveScript*입니다. 이 작업이 *양호*하게 실행되는 경우 다음 작업은 *RunSqoopExport*입니다.

    RunHiveScript에는 몇 가지 변수가 있습니다. Azure PowerShell을 사용하여 워크스테이션에서 Oozie 작업을 제출하면 해당 값이 전달됩니다.

    <table data-morhtml="true" border="1">
 <tr data-morhtml="true"><th data-morhtml="true">워크플로 변수</th><th data-morhtml="true">설명</th></tr>
 <tr data-morhtml="true"><td data-morhtml="true">${jobTracker}</td><td data-morhtml="true">Hadoop 작업 추적기의 URL을 지정합니다. HDInsight 클러스터 버전 2.0 및 3.0에는 <strong data-morhtml="true">jobtrackerhost:9010</strong>을 사용합니다.</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">${nameNode}</td><td data-morhtml="true">Hadoop namenode의 URL을 지정합니다. 기본 파일 시스템 WASB 주소를 사용합니다. 예를 들어 <i data-morhtml="true">wasb://&lt;containerName&gt;@&lt;storageAccountName&gt;.blob.core.windows.net</i>입니다.</td></tr>
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
 <tr data-morhtml="true"><td data-morhtml="true">${sqlDatabaseTableName}</td><td data-morhtml="true">내보내는 데이터를 넣을 SQL 데이터베이스 테이블입니다.</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">${hiveOutputFolder}</td><td data-morhtml="true">Hive INSERT OVERWRITE 문의 출력 폴더입니다. Sqoop 내보내기 export-dir용 폴더와 동일한 폴더입니다.</td></tr>
 </table>

    Oozie 워크플로 및 워크플로 동작 사용에 대한 자세한 내용은 HDInsight 클러스터 버전 3.0의 경우 [Apache Oozie 4.0 설명서](http://oozie.apache.org/docs/4.0.0/)(영문) 또는 HDInsight 클러스터 버전 2.1의 경우 [Apache Oozie 3.3.2 설명서](http://oozie.apache.org/docs/3.3.2/)(영문)를 참조하십시오.

2.  ANSI(ASCII) 인코딩을 사용하여 파일을 **C:\\Tutorials\\UseOozie\\workflow.xml**로 저장합니다(텍스트 편집기에 옵션이 없는 경우 메모장 사용).

**코디네이터를 정의하려면**

1.  다음 콘텐츠가 있는 텍스트 파일을 만듭니다.

         <coordinator-app name="my_coord_app" frequency="${coordFrequency}" start="${coordStart}" end="${coordEnd}" timezone="${coordTimezone}" xmlns="uri:oozie:coordinator:0.4">
            <action>
               <workflow>
                  <app-path>${wfPath}</app-path>
               </workflow>
            </action>
         </coordinator-app>

    정의 파일에는 5개의 변수가 사용됩니다.

    변수 | 설명 ------------------|------------ \${coordFrequency} | 작업 일시 중지 시간입니다. 빈도는 항상 분 단위로 표시됩니다. \${coordStart} | 작업 시작 시간입니다. \${coordEnd} | 작업 종료 시간입니다. \${coordTimezone} | Oozie는 DST(일반적으로 UTC)를 사용하지 않고 고정된 표준 시간대에서 코디네이터 작업을 처리합니다. 이 표준 시간대를 'Oozie 처리 표준 시간대'라고 합니다. \${wfPath} | workflow.xml의 경로입니다. 워크플로 파일 이름이 기본 파일 이름(workflow.xml)이 아닌 경우 기본 파일 이름을 지정해야 합니다.

2.  ANSI(ASCII) 인코딩을 사용하여 파일을 **C:\\Tutorials\\UseOozie\\coordinator.xml**로 저장합니다(텍스트 편집기에 옵션이 없는 경우 메모장 사용).

Oozie 프로젝트 배포 및 자습서 준비
----------------------------------

Azure PowerShell 스크립트를 실행하여 다음을 수행합니다.

-   HiveQL 스크립트(useoozie.hql)를 Azure Blob 저장소 wasb:///tutorials/useoozie/useoozie.hql에 복사합니다.
-   workflow.xml을 wasb:///tutorials/useoozie/workflow.xml에 복사합니다.
-   coordinator.xml을 wasb:///tutorials/useoozie/coordinator.xml에 복사합니다.
-   데이터 파일(/example/data/sample.log)을 wasb:///tutorials/useoozie/data/sample.log에 복사합니다.
-   Sqoop 내보내기 데이터를 저장하기 위한 SQL 데이터베이스 테이블을 만듭니다. 테이블 이름은 *log4jLogCount*입니다.

**HDInsight 저장소 이해**

HDInsight는 데이터 저장소로 Azure Blob 저장소를 사용합니다. 이를 *WASB* 또는 *Azure 저장소 - Blob*이라고 합니다. WASB는 Azure Blob 저장소에 구현한 Microsoft의 HDFS입니다. 자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용](/en-us/documentation/articles/hdinsight-use-blob-storage/)을 참조하십시오.

HDInsight 클러스터를 프로비전할 때 Azure 저장소 계정 및 이 계정에서 오는 특정 Blob 저장소 컨테이너가 HDFS의 경우와 같이 기본 파일 시스템으로 지정됩니다. 프로비전 프로세스 중에 이 저장소 계정 외에도 동일한 Azure 구독 또는 다른 Azure 구독에서 저장소 계정을 추가할 수 있습니다. 저장소 계정 추가에 대한 지침은 [HDInsight 클러스터 프로비전](/en-us/documentation/articles/hdinsight-provision-clusters/)을 참조하십시오. 이 자습서에서 사용되는 PowerShell 스크립트를 간소화하기 위해 모든 파일이 */tutorials/useoozie*에 위치한 기본 파일 시스템 컨테이너에 저장됩니다. 기본적으로 이 컨테이너 이름은 HDInsight 클러스터 이름과 동일합니다. WASB 구문은 다음과 같습니다.

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] HDInsight 클러스터 버전 3.0에서는 *wasb://* 구문만 지원됩니다. 이전 *asv://* 구문은 HDInsight 2.1 및 1.6 클러스터에서 지원되지만 HDInsight 3.0 클러스터에서는 지원되지 않으며 이후 버전에서도 지원되지 않을 것입니다.

> [WACOM.NOTE] WASB 경로는 가상 경로입니다. 자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용](/en-us/documentation/articles/hdinsight-use-blob-storage/)을 참조하십시오.

기본 파일 시스템 컨테이너에 저장된 파일은 HDInsight에서 다음 URI를 사용하여 액세스할 수 있습니다(workflow.xml을 예로 사용함).

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/workflow.xml
    wasb:///tutorials/useoozie/workflow.xml
    /tutorials/useoozie/workflow.xml

저장소 계정에서 바로 파일에 액세스하려는 경우 파일의 Blob 이름은 다음과 같습니다.

    tutorials/useoozie/workflow.xml

**Hive 내부 테이블 및 외부 테이블 이해**

Hive 내부 테이블 및 외부 테이블에 대해 알아야 할 몇 가지 사항이 있습니다.

-   CREATE TABLE 명령은 관리 테이블이라고도 하는 내부 테이블을 만듭니다. 데이터 파일은 기본 컨테이너에 위치해야 합니다.
-   CREATE TABLE 명령은 데이터 파일을 기본 컨테이너의 /hive/warehouse/ 폴더로 이동합니다.
-   CREATE EXTERNAL TABLE 명령은 외부 테이블을 만듭니다. 데이터 파일은 기본 컨테이너 외부에 있을 수 있습니다.
-   CREATE EXTERNAL TABLE 명령은 데이터 파일을 이동하지 않습니다.
-   CREATE EXTERNAL TABLE 명령은 LOCATION 절에 지정된 폴더 아래에 하위 폴더를 허용하지 않습니다. 이 때문에 자습서에서는 sample.log 파일의 복사본을 만듭니다.

자세한 내용은 [HDInsight: Hive 내부 및 외부 테이블 소개](http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx)(영문)를 참조하십시오.

**자습서를 준비하려면**

1.  Windows PowerShell ISE를 엽니다. Windows 8 시작 화면에서 **PowerShell\_ISE**를 입력한 후 **Windows PowerShell ISE**를 클릭하면 됩니다. [Windows 8 및 Windows에서 Windows PowerShell 시작](http://technet.microsoft.com/en-us/library/hh847889.aspx)(영문)을 참조하십시오.
2.  아래쪽 창에서 다음 명령을 실행하여 Azure 구독에 연결합니다.

         Add-AzureAccount

    Azure 계정 자격 증명을 입력하라는 메시지가 표시됩니다. 구독 연결을 추가하는 이 메서드의 시간이 초과하고 12시간 후에 cmdlet을 다시 실행해야 합니다.

    > [WACOM.NOTE] 여러 Azure 구독이 있는 경우 기본 구독이 사용하려는 구독이 아니면 **Select-AzureSubscription** cmdlet을 사용하여 현재 구독을 선택합니다.

3.  다음 스크립트를 스크립트 창에 복사한 후 처음 6개 변수를 설정합니다.

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
         $hiveQLScript = "C:\Tutorials\UseOozie\useooziewf.hql"
         $workflowDefinition = "C:\Tutorials\UseOozie\workflow.xml"
         $coordDefinition =  "C:\Tutorials\UseOozie\coordinator.xml"
            
         # WASB folder for storing the Oozie tutorial files.
         $destFolder = "tutorials/useoozie"  # Do NOT use the long path here

    변수에 대한 자세한 내용은 이 자습서의 [필수 조건](#prerequisites) 섹션을 참조하십시오.

4.  다음을 스크립트 창에서 스크립트에 추가합니다.

         # Create a storage context object
         $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
         $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey
            
         function uploadOozieFiles()
         {      
             Write-Host "Copy HiveQL script, workflow definition and coordinator definition ..." -ForegroundColor Green
             Set-AzureStorageBlobContent -File $hiveQLScript -Container $containerName -Blob "$destFolder/useooziewf.hql" -Context $destContext
             Set-AzureStorageBlobContent -File $workflowDefinition -Container $containerName -Blob "$destFolder/workflow.xml" -Context $destContext
             Set-AzureStorageBlobContent -File $coordDefinition -Container $containerName -Blob "$destFolder/coordinator.xml" -Context $destContext
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

    ![자습서 준비 출력](./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.Preparation.Output1.png)

Oozie 프로젝트 실행
-------------------

Azure PowerShell은 현재 Oozie 작업 정의용 cmdlet을 제공하지 않습니다. Invoke-RestMethod PowerShell cmdlet을 사용하여 Oozie 웹 서비스를 호출할 수 있습니다. Oozie 웹 서비스 API는 HTTP REST JSON API입니다. Oozie 웹 서비스 API에 대한 자세한 내용은 HDInsight 클러스터 버전 3.0의 경우 [Apache Oozie 4.0 설명서](http://oozie.apache.org/docs/4.0.0/)(영문) 또는 HDInsight 클러스터 버전 2.1의 경우 [Apache Oozie 3.3.2 설명서](http://oozie.apache.org/docs/3.3.2/)(영문)를 참조하십시오.

**Oozie 작업을 제출하려면**

1.  Windows PowerShell ISE를 엽니다. Windows 8 시작 화면에서 **PowerShell\_ISE**를 입력한 후 **Windows PowerShell ISE**를 클릭하면 됩니다. [Windows 8 및 Windows에서 Windows PowerShell 시작](http://technet.microsoft.com/en-us/library/hh847889.aspx)(영문)을 참조하십시오.

2.  다음 스크립트를 스크립트 창에 복사한 후 처음 14개 변수를 설정합니다(6번째인 \$storageUri 건너뜀).

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
            
         #Oozie WF/coordinator variables
         $coordStart = "2014-03-21T13:45Z"
         $coordEnd = "2014-03-21T13:45Z"
         $coordFrequency = "1440" # in minutes, 24h x 60m = 1440m
         $coordTimezone = "UTC"   #UTC/GMT

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

    변수에 대한 자세한 내용은 이 자습서의 [필수 조건](#prerequisites) 섹션을 참조하십시오.

    \$coordstart 및 \$coordend는 워크플로 시작 시간 및 끝 시간입니다. UTC/GMT 시간을 알아보려면 bing.com에서 "utc time"을 검색합니다. \$coordFrequency는 워크플로를 실행할 빈도(분 단위)입니다.

3.  스크립트에 다음을 추가합니다. 이 부분은 Oozie 페이로드를 정의합니다.

         #OoziePayload used for Oozie web service submission
         $OoziePayload =  @"
         <
         xml version="1.0" encoding="UTF-8"
         >
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
                <name>oozie.coord.application.path</name>
                <value>$oozieWFPath</value>
            </property>

            <property>
                <name>wfPath</name>
                <value>$oozieWFPath</value>
            </property>

            <property>
                <name>coordStart</name>
                <value>$coordStart</value>
            </property>

            <property>
                <name>coordEnd</name>
                <value>$coordEnd</value>
            </property>

            <property>
                <name>coordFrequency</name>
                <value>$coordFrequency</value>
            </property>

            <property>
                <name>coordTimezone</name>
                <value>$coordTimezone</value>
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
            
         </configuration>
         "@

    > [WACOM.NOTE] 워크플로 제출 페이로드 파일과 비교할 때 주요 차이점은 **oozie.coord.application.path** 변수입니다. 워크플로 작업을 제출할 때는 대신 **oozie.wf.application.path**를 사용합니다.

4.  스크립트에 다음을 추가합니다. 이 부분은 Oozie 웹 서비스 상태를 확인합니다.

         function checkOozieServerStatus()
         {
             Write-Host "Checking Oozie server status..." -ForegroundColor Green
             $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/admin/status"
             $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds -OutVariable $OozieServerStatus 
                
             $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
             $oozieServerSatus = $jsonResponse[0].("systemMode")
             Write-Host "Oozie server status is $oozieServerSatus..."
            
             if($oozieServerSatus -notmatch "NORMAL")
             {
                 Write-Host "Oozie server status is $oozieServerSatus...cannot submit Oozie jobs. Check the server status and re-run the job."
                 exit 1
             }
         }

5.  스크립트에 다음을 추가합니다. 이 부분은 Oozie 작업을 만듭니다.

         function createOozieJob()
         {
             # create Oozie job
             Write-Host "Sending the following Payload to the cluster:" -ForegroundColor Green
             Write-Host "`n--------`n$OoziePayload`n--------"
             $clusterUriCreateJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/jobs"
             $response = Invoke-RestMethod -Method Post -Uri $clusterUriCreateJob -Credential $creds -Body $OoziePayload -ContentType "application/xml" -OutVariable $OozieJobName -debug -Verbose
            
             $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
             $oozieJobId = $jsonResponse[0].("id")
             Write-Host "Oozie job id is $oozieJobId..."
            
             return $oozieJobId
         }

    > [WACOM.NOTE] 워크플로 작업을 제출하는 경우 이 작업이 만들어진 후에 시작되도록 다른 웹 서비스를 호출해야 합니다. 이 경우 코디네이터 작업은 시간별로 트리거됩니다. 작업이 자동으로 시작됩니다.

6.  스크립트에 다음을 추가합니다. 이 부분은 Oozie 작업 상태를 확인합니다.

         function checkOozieJobStatus($oozieJobId)
         {
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
            
             Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state!"
             if($JobStatus -notmatch "SUCCEEDED")
             {
                 Write-Host "Check logs at http://headnode0:9014/cluster for detais."
                 exit -1
             }
         }

7.  (선택 사항) 스크립트에 다음을 추가합니다.

         function listOozieJobs()
         {
             Write-Host "Listing Oozie jobs..." -ForegroundColor Green
             $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/jobs"
             $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds 
                
             write-host "Job ID                                   App Name        Status      Started                         Ended"
             write-host "----------------------------------------------------------------------------------------------------------------------------------"
             foreach($job in $response.workflows)
             {
                 Write-Host $job.id "`t" $job.appName "`t" $job.status "`t" $job.startTime "`t" $job.endTime
             }
         }

         function ShowOozieJobLog($oozieJobId)
         {
             Write-Host "Showing Oozie job info..." -ForegroundColor Green
             $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/$oozieJobId" + "
         show=log"
             $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds 
             write-host $response
         }

         function killOozieJob($oozieJobId)
         {
             Write-Host "Killing the Oozie job $oozieJobId..." -ForegroundColor Green
             $clusterUriStartJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "
         action=kill" #Valid values for the 'action' parameter are 'start', 'suspend', 'resume', 'kill', 'dryrun', 'rerun', and 'change'.
             $response = Invoke-RestMethod -Method Put -Uri $clusterUriStartJob -Credential $creds | Format-Table -HideTableHeaders -debug
         }

8.  스크립트에 다음을 추가합니다.

         checkOozieServerStatus
         # listOozieJobs
         $oozieJobId = createOozieJob($oozieJobId)
         checkOozieJobStatus($oozieJobId)
         # ShowOozieJobLog($oozieJobId)
         # killOozieJob($oozieJobId)

    추가 함수를 실행하려는 경우 \# 기호를 제거합니다.

9.  HDinsight 클러스터 버전이 2.1인 경우 "https://\$clusterName.azurehdinsight.net:443/oozie/v2/"를 "https://\$clusterName.azurehdinsight.net:443/oozie/v1/"로 바꿉니다. HDInsight 클러스터 버전 2.1은 웹 서비스 버전 2를 지원하지 않습니다.

10. **스크립트 실행**을 클릭하거나 **F5** 키를 눌러 스크립트를 실행합니다. 다음과 유사하게 출력됩니다.

    ![자습서 실행 워크플로 출력](./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.RunCoord.Output.png)

11. SQL 데이터베이스에 연결하여 내보낸 데이터를 표시합니다.

**작업 오류 로그를 확인하려면**

워크플로 문제를 해결하기 위해 클러스터 headnode에서 C:\\apps\\dist\\oozie-3.3.2.1.3.2.0-05\\oozie-win-distro\\logs\\Oozie.log에 있는 Oozie 로그 파일을 확인할 수 있습니다. RDP에 대한 자세한 내용은 [관리 포털을 사용하여 HDInsight 클러스터 관리](/en-us/documentation/articles/hdinsight-administer-use-management-portal/)를 참조하십시오.

**자습서를 다시 실행하려면**

워크플로를 다시 실행하려면 다음을 수행해야 합니다.

-   Hive 스크립트 출력 파일 삭제
-   log4jLogsCount 테이블의 데이터 삭제

다음은 사용할 수 있는 샘플 PowerShell 스크립트입니다.

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

-   [HDInsight 시작](/en-us/documentation/articles/hdinsight-get-started/)
-   [HDInsight Emulator 시작](/en-us/documentation/articles/hdinsight-get-started-emulator/)
-   [HDInsight에서 Azure Blob 저장소 사용](/en-us/documentation/articles/hdinsight-use-blob-storage/)
-   [PowerShell을 사용하여 HDInsight 관리](/en-us/documentation/articles/hdinsight-administer-use-powershell/)
-   [HDInsight에 데이터 업로드](/en-us/documentation/articles/hdinsight-upload-data/)
-   [HDInsight에서 Sqoop 사용](../hdinsight-use-sqoop/)
-   [HDInsight에서 Hive 사용](/en-us/documentation/articles/hdinsight-use-hive/)
-   [HDInsight에서 Pig 사용](/en-us/documentation/articles/hdinsight-use-pig/)
-   [HDInsight용 C\# Hadoop 스트리밍 작업 개발](/en-us/documentation/articles/hdinsight-hadoop-develop-deploy-streaming-jobs/)
-   [HDInsight용 Java MapReduce 프로그램 개발](/en-us/documentation/articles/hdinsight-develop-deploy-java-mapreduce/)

