<properties
	pageTitle="Linux 기반 HDInsight에서 Hadoop Sqoop 사용 | Microsoft Azure"
	description="HDInsight 클러스터의 Linux 기반 Hadoop과 Azure SQL 데이터베이스 사이에서 Sqoop 가져오기/내보내기를 실행하는 방법을 알아봅니다."
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/09/2016"
	ms.author="larryfr"/>

#HDInsight에서 Hadoop과 Sqoop 사용(SSH)

[AZURE.INCLUDE [sqoop 선택기](../../includes/hdinsight-selector-use-sqoop.md)]

Sqoop을 사용하여 Linux 기반 HDInsight 클러스터와 Azure SQL 데이터베이스 또는 SQL 서버 데이터베이스 사이에서 가져오기 및 내보내기하는 방법을 알아봅니다.

> [AZURE.NOTE] 이 문서 내의 단계는 SSH를 사용하여 Linux 기반의 HDInsight 클러스터에 연결합니다. Windows 클라이언트는 또한 Azure PowerShell을 사용하여 [HDInsight에서 Hadoop과 함께 Sqoop 사용(PowerShell)](hdinsight-use-sqoop.md)에 문서화된 대로 Linux 기반 클러스터에서 Sqoop을 가지고 작업할 수도 있습니다.

##Sqoop 정의

비구조적 및 반구조적 데이터(예: 로그 및 파일)를 처리하기 위해 당연히 Hadoop을 선택하지만 관계형 데이터베이스에 저장된 구조적 데이터를 처리해야 할 경우도 있습니다.

[Sqoop][sqoop-user-guide-1.4.4]은 Hadoop 클러스터와 관계형 데이터베이스 간 데이터 전송을 위해 설계된 도구입니다. 이 도구를 사용하면 SQL Server, MySQL, Oracle 등의 RDBMS(관계형 데이터베이스 관리 시스템)에서 HDFS(Hadoop Distributed File System)로 데이터를 가져오고, MapReduce 또는 Hive로 Hadoop의 데이터를 변환한 후 데이터를 RDBMS로 다시 내보낼 수 있습니다. 이 자습서에서는 관계형 데이터베이스에 SQL Server 데이터베이스를 사용합니다.

HDInsight 클러스터에서 지원되는 Sqoop 버전을 보려면 [HDInsight에서 제공하는 클러스터 버전의 새로운 기능][hdinsight-versions]을 참조하세요.


##필수 조건

이 자습서를 시작하기 전에 다음이 있어야 합니다.

- **워크스테이션**: SSH 클라이언트가 있는 컴퓨터입니다.

- **Azure CLI**: 자세한 내용은 [Azure CLI 설치 및 구성](../xplat-cli-install.md)을 참조하십시오.

##시나리오 이해

HDInsight 클러스터는 일부 샘플 데이터와 함께 제공됩니다. ****wasb:///hive/warehouse/hivesampletable**에 위치한 데이터 파일을 참조하는 **hivesampletable**이라는 이름의 Hive 테이블을 사용합니다. 이 테이블에는 일부 모바일 장치 데이터가 포함되어 있습니다. 다음은 Hive 테이블 스키마입니다.

| 필드 | 데이터 형식 |
| ----- | --------- |
| clientid | string |
| querytime | string |
| market | string |
| deviceplatform | string |
| devicemake | string |
| devicemodel | string |
| state | string |
| country | string |
| querydwelltime | double |
| sessionid | bigint |
| sessionpagevieworder | bigint |

먼저 **hivesampletable**을 Azure SQL 데이터베이스 또는 **mobiledata**라는 이름의 테이블에 있는 SQL Server로 내보낸 다음 테이블을 다시 ****wasb:///tutorials/usesqoop/importeddata**에 있는 HDInsight로 가져옵니다.


## 클러스터 및 SQL 데이터베이스 만들기

1. Azure 포털에서 ARM 템플릿을 열려면 다음 이미지를 클릭합니다.         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fusesqoop%2Fcreate-linux-based-hadoop-cluster-in-hdinsight-and-sql-database.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/ko-KR/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    ARM 템플릿은 공용 Blob 컨테이너에 있습니다. **https://hditutorialdata.blob.core.windows.net/usesqoop/create-linux-based-hadoop-cluster-in-hdinsight-and-sql-database.json*.
    
    ARM 템플릿은 SQL 데이터베이스에 테이블 스키마를 배포하는 bacpac 패키지를 호출합니다. bacpac 패키지는 공용 Blob 컨테이너에도 있습니다. https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpac Bacpac 파일에 대한 개인 컨테이너를 사용하려는 경우 템플릿에 다음 값을 사용합니다.
    
        "storageKeyType": "Primary",
        "storageKey": "<TheAzureStorageAccountKey>",
    
2. 매개 변수 블레이드에서 다음을 입력합니다.

    - **ClusterName**: 만들려는 Hadoop 클러스터의 이름을 입력합니다.
    - **클러스터 로그인 이름 및 암호**: 기본 로그인 이름은 admin입니다.
    - **SSH 사용자 이름 및 암호**입니다.
    - **SQL 데이터베이스 서버 로그인 이름 및 암호**입니다.

    다음 값은 변수 섹션에서 하드 코드합니다.
    
    |기본 저장소 계정 이름|<CluterName>store|
    |----------------------------|-----------------|
    |Azure SQL 데이터베이스 서버 이름|<ClusterName>dbserver|
    |Azure SQL 데이터베이스 이름|<ClusterName>db|
    
    이러한 값을 기록해 두십시오. 이 정보는 자습서의 뒷부분에서 필요합니다.
    
3\. **확인**을 클릭하여 매개 변수를 저장합니다.

4\. **사용자 지정 배포** 블레이드에서 **리소스 그룹** 드롭다운 상자를 클릭한 후 **새로 만들기**를 클릭하여 새 리소스 그룹을 만듭니다. 리소스 그룹은 클러스터, 종속 저장소 계정 및 기타 연결된 리소스를 그룹화하는 컨테이너입니다.

5\. **약관**을 클릭한 다음 **만들기**를 클릭합니다.

6\.**만들기**를 클릭합니다. 템플릿 배포에 배포 제출 중이라는 제목의 새 타일이 표시됩니다. 클러스터 및 SQL 데이터베이스를 만들려면 20분 정도가 걸립니다.

기존 Azure SQL 데이터베이스 또는 Microsoft SQL Server를 사용하기로 선택하는 경우

- **Azure SQL 데이터베이스**: 워크스테이션에서 액세스할 수 있도록 Azure SQL 데이터베이스 서버의 방화벽 규칙을 구성해야 합니다. Azure SQL 데이터베이스 만들기 및 방화벽 구성에 대한 자세한 내용은 [Azure SQL 데이터베이스 사용 시작][sqldatabase-get-started]을 참조하세요. 

    > [AZURE.NOTE] 기본적으로 Azure SQL 데이터베이스는 Azure HDInsight 같은 Azure 서비스로부터의 연결을 허용합니다. 이 방화벽 설정을 사용하지 않도록 설정한 경우 Azure Preview 포털에서 사용하도록 설정해야 합니다. Azure SQL 데이터베이스 만들기 및 방화벽 규칙 구성에 대한 지침은 [SQL 데이터베이스 만들기 및 구성][sqldatabase-create-configue]을 참조하세요.

- **SQL Server**: HDInsight 클러스터가 SQL Server와 같은 Azure의 가상 네트워크에 있으면 이 문서의 단계를 사용하여 SQL Server 데이터베이스에 대해 데이터 가져오기 및 내보내기를 수행할 수 있습니다.

    > [AZURE.NOTE] HDInsight는 위치 기반 가상 네트워크만 지원하며 현재 선호도 그룹 기반 가상 네트워크와는 연동되지 않습니다.

    * 가상 네트워크를 만들고 구성하려면 [가상 네트워크 구성 작업](../services/virtual-machines/)을 참조하세요.

        * 데이터 센터에서 SQL Server를 사용할 때는 가상 네트워크를 *사이트 간* 또는 *지점 및 사이트 간*으로 구성해야 합니다.

            > [AZURE.NOTE] **지점 및 사이트 간** 가상 네트워크의 경우 SQL Server가 VPN 클라이언트 구성 응용 프로그램을 실행해야 합니다. 이 응용 프로그램은 Azure 가상 네트워크 구성의 **대시보드**에서 사용 가능합니다.

        * Azure 가상 컴퓨터에서 SQL Server를 사용할 때는 SQL Server를 호스트하는 가상 컴퓨터가 HDInsight와 같은 가상 네트워크의 멤버이면 모든 가상 네트워크 구성을 사용할 수 있습니다.

    * 가상 네트워크에 HDInsight 클러스터를 만들려면 [사용자 지정 옵션을 사용하여 HDInsight의 Hadoop 클러스터 만들기](hdinsight-provision-clusters.md)를 참조하세요.

    > [AZURE.NOTE] SQL Server는 인증도 허용해야 합니다. 이 문서의 단계를 완료하려면 SQL 서버 로그인을 사용해야 합니다.
	

##Sqoop 내보내기

2. 다음 명령을 사용하여 Sqoop lib 디렉터리에서 SQL Server JDBC 드라이버에 링크를 만듭니다. 이를 통해 Sqoop이 다음 드라이버를 사용하여 SQL 데이터베이스와 통신할 수 있습니다.

        sudo ln /usr/share/java/sqljdbc_4.1/enu/sqljdbc41.jar /usr/hdp/current/sqoop-client/lib/sqljdbc41.jar

3. 다음 명령을 사용하여 Sqoop이 SQL 데이터베이스를 볼 수 있는지 확인합니다.

        sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>

    그러면 앞에서 만든 **sqooptest** 데이터베이스를 포함한 데이터베이스 목록이 반환됩니다.

4. 다음 명령을 사용하여 **hivesampletable**에서 **mobiledata** 테이블로 데이터를 내보냅니다.

        sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --export-dir 'wasb:///hive/warehouse/hivesampletable' --fields-terminated-by '\t' -m 1

    이것은 Sqoop에게 SQL 데이터베이스, **sqooptest** 데이터베이스에 연결하고 **wasb:///hive/warehouse/hivesampletable**(*hivesampletable*에 대한 물리적 파일)에서 **mobiledata** 테이블로 데이터를 내보내도록 지시합니다.

5. 명령이 완료되면 다음을 통해 TSQL을 사용하여 데이터베이스에 연결합니다.

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    연결되면 다음 명령문을 사용하여 데이터가 **mobiledata** 테이블로 내보내기되었는지 확인합니다.

        SELECT * FROM mobiledata
        GO

    테이블에 데이터 목록이 표시됩니다. `exit`를 입력하여 tsql 유틸리티를 종료합니다.

##Sqoop 가져오기

1. 다음을 사용하여 SQL 데이터베이스에 있는 **mobiledata** 테이블로부터 데이터를 HDInsight 내 **wasb:///tutorials/usesqoop/importeddata** 디렉터리로 가져옵니다.

        sqoop import --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1

    가져온 데이터에는 탭 문자로 구분된 필드가 있으며 줄이 새로운 줄 문자로 종료됩니다.

2. 가져오기가 완료되면 다음 명령을 사용하여 새로운 디렉터리에 데이터를 나열합니다.

        hadoop fs -text wasb:///tutorials/usesqoop/importeddata/part-m-00000

##SQL Server 사용하기

또한 Sqoop을 사용하여 데이터 센터 내 또는 Azure에 호스팅된 가상 컴퓨터에서 SQL Server로부터 데이터를 가져오기/내보내기할 수 있습니다. SQL 데이터베이스와 SQL Server의 차이점은 다음과 같습니다.

* HDInsight와 SQL Server가 모두 동일한 Azure 가상 네트워크에 있어야 합니다.

    > [AZURE.NOTE] HDInsight는 위치 기반 가상 네트워크만 지원하며 현재 선호도 그룹 기반 가상 네트워크와는 연동되지 않습니다.

    데이터 센터에서 SQL Server를 사용할 때는 가상 네트워크를 *사이트 간* 또는 *지점 및 사이트 간*으로 구성해야 합니다.

    > [AZURE.NOTE] **지점 및 사이트 간** 가상 네트워크의 경우 SQL Server가 VPN 클라이언트 구성 응용 프로그램을 실행해야 합니다. 이 응용 프로그램은 Azure 가상 네트워크 구성의 **대시보드**에서 사용 가능합니다.

    가상 네트워크 만들기 및 구성에 대한 자세한 내용은 [가상 네트워크 구성 작업](../services/virtual-machines/)을 참조하십시오.

* SQL Server는 SQL 인증을 허용하도록 구성되어야 합니다. 자세한 내용은 [인증 모드 선택](https://msdn.microsoft.com/ms144284.aspx)을 참조하십시오.  


* SQL Server를 원격 연결을 허용하도록 구성해야 할 수 있습니다. 자세한 내용은 [SQL Server 데이터베이스 엔진에 연결하는 문제를 해결하는 방법](http://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx)을 참조하십시오.

* **SQL Server Management Studio** 또는 **tsql**과 같은 유틸리티를 사용하여 SQL Server 내에 **sqooptest** 데이터베이스를 만들어야 합니다. Azure CLI 사용 단계는 Azure SQL 데이터베이스에만 적용됩니다.

    **mobiledata** 테이블을 만드는 TSQL 명령문은 SQL 데이터베이스에 사용되는 명령문과 유사합니다. 단, 클러스터형 인덱스 생성은 예외입니다. 이것은 SQL Server에 필요하지 않습니다.

        CREATE TABLE [dbo].[mobiledata](
        [clientid] [nvarchar](50),
        [querytime] [nvarchar](50),
        [market] [nvarchar](50),
        [deviceplatform] [nvarchar](50),
        [devicemake] [nvarchar](50),
        [devicemodel] [nvarchar](50),
        [state] [nvarchar](50),
        [country] [nvarchar](50),
        [querydwelltime] [float],
        [sessionid] [bigint],
        [sessionpagevieworder] [bigint])

* HDInsight로부터 SQL Server에 연결할 때는 Azure 가상 네트워크에서 이름을 확인하도록 도메인 이름 시스템(DNS)을 구성하지 않은 한, SQL Server의 IP 주소를 사용해야 할 수도 있습니다. 예:

        sqoop import --connect 'jdbc:sqlserver://10.0.1.1:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1

##다음 단계

이제 Sqoop을 사용하는 방법에 대해 알아봤습니다. 자세한 내용은 다음을 참조하세요.

- [HDInsight와 함께 Oozie 사용][hdinsight-use-oozie]\: Oozie 워크플로에서 Sqoop 작업을 사용합니다.
- [HDInsight를 사용하여 비행 지연 데이터 분석][hdinsight-analyze-flight-data]\: Hive를 사용하여 비행 지연 데이터를 분석한 후 Sqoop을 사용하여 데이터를 Azure SQL 데이터베이스로 내보냅니다.
- [HDInsight에 데이터 업로드][hdinsight-upload-data]\: HDInsight/Azure Blob 저장소에 데이터를 업로드하는 다른 방법을 찾습니다.



[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[sqldatabase-get-started]: ../sql-database-get-started.md
[sqldatabase-create-configue]: ../sql-database-create-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: powershell-install-configure.md
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

<!---HONumber=AcomDC_0316_2016-->