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

> [AZURE.NOTE] 이 문서 내의 단계는 SSH를 사용하여 Linux 기반의 HDInsight 클러스터에 연결합니다. Windows 클라이언트는 Linux 기반 클러스터에서 Sqoop으로 작업하는 데 Azure PowerShell 및 HDInsight .NET SDK를 사용할 수도 있습니다. 탭 선택기를 사용하여 해당 문서를 엽니다.

##필수 조건

이 자습서를 시작하기 전에 다음이 있어야 합니다.


- **HDInsight에 Hadoop 클러스터**. [클러스터 및 SQL 데이터베이스 만들기](hdinsight-use-sqoop.md#create-cluster-and-sql-database)를 참조하세요.
- **워크스테이션**: SSH 클라이언트가 있는 컴퓨터입니다.
- **Azure CLI**: 자세한 내용은 [Azure CLI 설치 및 구성](../xplat-cli-install.md)을 참조하십시오.

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

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

<!---HONumber=AcomDC_0420_2016-->