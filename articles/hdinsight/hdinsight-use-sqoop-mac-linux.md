---
title: "Hadoop과 Apache Sqoop - Azure HDInsight | Microsoft Docs"
description: "Apache Sqoop을 사용하여 HDInsight의 Hadoop과 Azure SQL Database 간에 가져오기 및 내보내기를 수행하는 방법을 알아봅니다."
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: 
author: Blackmist
tags: azure-portal
keywords: hadoop sqoop,sqoop
ms.assetid: 303649a5-4be5-4933-bf1d-4b232083c354
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/14/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: 8d71c1d870083365a1c106507ceb5f2661ee4ac6
ms.contentlocale: ko-kr
ms.lasthandoff: 05/18/2017


---
# <a name="use-apache-sqoop-to-import-and-export-data-between-hadoop-in-hdinsight-and-sql-database"></a>Apache Sqoop을 사용하여 HDInsight의 Hadoop과 SQL Database 간에 데이터 가져오기 및 내보내기

[!INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Apache Sqoop을 사용하여 Azure HDInsight의 Hadoop 클러스터와 Azure SQL Database 또는 Microsoft SQL Server Database 사이에서 가져오기 및 내보내기를 수행하는 방법을 알아봅니다. 이 문서의 단계에서는 Hadoop 클러스터의 헤드에서 직접 `sqoop` 명령을 사용합니다. SSH를 사용하여 헤드 노드에 연결하고 이 문서의 명령을 실행합니다.

> [!IMPORTANT]
> 이 문서의 단계는 Linux를 사용하는 HDInsight 클러스터에만 적용됩니다. Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [Windows에서 HDInsight 사용 중지](hdinsight-component-versioning.md#hdi-version-33-nearing-retirement-date)를 참조하세요.

## <a name="install-freetds"></a>FreeTDS 설치

1. SSH를 사용하여 HDInsight 클러스터에 연결합니다. 예를 들어 다음 명령은 `mycluster`라는 클러스터의 기본 헤드 노드에 연결합니다.

    ```bash
    ssh CLUSTERNAME-ssh.azurehdinsight.net
    ```

    자세한 내용은 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

2. 다음 명령을 사용하여 FreeTDS:를 설치합니다.

    ```bash
    sudo apt install --assume-yes install freetds-dev freetds-bin
    ```

    FreeTDS는 SQL Database에 연결하기 위해 여러 단계에서 사용됩니다.

## <a name="create-the-table-in-sql-database"></a>SQL 데이터베이스에 테이블 만들기

> [!IMPORTANT]
> [클러스터 및 SQL Database 만들기](hdinsight-use-sqoop.md)에서 만든 HDInsight 클러스터 및 SQL Database를 사용하고 있는 경우 이 섹션의 단계를 무시하십시오. 데이터베이스 테이블은 [클러스터 및 SQL Database 만들기](hdinsight-use-sqoop.md)의 단계 중에 생성된 것입니다.

1. SSH 세션에서 다음 명령을 사용하여 SQL Database 서버에 연결합니다.

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    다음 텍스트와 비슷한 출력이 표시됩니다.

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to sqooptest
        1>

2. `1>` 프롬프트에 다음 쿼리를 입력합니다.

    ```sql
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
    GO
    CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(clientid)
    GO
    ```

    `GO` 문을 입력하면 이전 문이 평가됩니다. 먼저 **mobiledata** 테이블이 생성된 다음 클러스터형 인덱스가 추가됩니다(SQL 데이터베이스에 필수).

    다음 쿼리를 사용하여 테이블이 생성되었는지 확인합니다.

    ```sql
    SELECT * FROM information_schema.tables
    GO
    ```

    그러면 다음 텍스트와 유사한 출력이 표시됩니다.

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        sqooptest       dbo     mobiledata      BASE TABLE

3. `exit` at the `1>` 를 입력하여 tsql 유틸리티를 종료합니다.

## <a name="sqoop-export"></a>Sqoop 내보내기

1. SSH와 클러스터 간 연결에서 다음 명령을 사용하여 Sqoop이 SQL Database를 볼 수 있는지 확인합니다.

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>
    ```

    이 명령은 앞에서 만든 **sqooptest** 데이터베이스를 포함한 데이터베이스 목록이 반환됩니다.

2. **hivesampletable**에서 **mobiledata** 테이블로 데이터를 내보내려면 다음 명령을 사용합니다.

    ```bash
    sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --export-dir 'wasbs:///hive/warehouse/hivesampletable' --fields-terminated-by '\t' -m 1
    ```

    이 명령은 Sqoop에 **sqooptest** 데이터베이스에 연결하도록 지시합니다. 그러면 Sqoop은 **wasbs:///hive/warehouse/hivesampletable**에서 **mobiledata** 테이블로 데이터를 내보냅니다.

3. 명령이 완료되면 다음 명령과 TSQL을 사용하여 데이터베이스에 연결합니다.

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest
    ```

    연결되면 다음 명령문을 사용하여 데이터가 **mobiledata** 테이블로 내보내기되었는지 확인합니다.

    ```sql
    SELECT * FROM mobiledata
    GO
    ```

    테이블에 데이터 목록이 표시됩니다. `exit` 를 입력하여 tsql 유틸리티를 종료합니다.

## <a name="sqoop-import"></a>Sqoop 가져오기

1. 다음 명령을 사용하여 SQL 데이터베이스의 **mobiledata** 테이블에서 HDInsight의 **wasbs:///tutorials/usesqoop/importeddata** 디렉터리로 데이터를 가져옵니다.

    ```bash
    sqoop import --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasbs:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

    데이터의 필드는 탭 문자로 구분되어 있으며 줄은 줄 바꿈 문자로 종료됩니다.

2. 가져오기가 완료되면 다음 명령을 사용하여 새로운 디렉터리에 데이터를 나열합니다.

    ```bash
    hdfs dfs -text /tutorials/usesqoop/importeddata/part-m-00000
    ```

## <a name="using-sql-server"></a>SQL Server 사용하기

또한 Sqoop을 사용하여 데이터 센터 내 또는 Azure에 호스팅된 가상 컴퓨터에서 SQL Server로부터 데이터를 가져오기/내보내기할 수 있습니다. SQL 데이터베이스와 SQL Server의 차이점은 다음과 같습니다.

* HDInsight와 SQL Server가 모두 동일한 Azure Virtual Network에 있어야 합니다.

    데이터 센터에서 SQL Server를 사용할 때는 가상 네트워크를 *사이트 간* 또는 *지점 및 사이트 간*으로 구성해야 합니다.

  > [!NOTE]
  > **지점 및 사이트 간** 가상 네트워크를 사용할 경우 SQL Server는 VPN 클라이언트 구성 응용 프로그램을 실행해야 합니다. VPN 클라이언트는 Azure Virtual Network 구성의 **대시보드**에서 사용할 수 있습니다.

    HDInsight와 함께 Azure Virtual Network를 사용하는 방법에 대한 자세한 내용은 [Azure Virtual Network를 사용하여 HDInsight 확장](hdinsight-extend-hadoop-virtual-network.md) 문서를 참조하세요. Azure Virtual Network에 대한 자세한 내용은 [가상 네트워크 개요](../virtual-network/virtual-networks-overview.md) 문서를 참조하세요.

* SQL Server는 SQL 인증을 허용하도록 구성되어야 합니다. 자세한 내용은 [인증 모드 선택](https://msdn.microsoft.com/ms144284.aspx) 문서를 참조하세요.

* SQL Server를 원격 연결을 허용하도록 구성해야 할 수 있습니다. 자세한 내용은 [SQL Server 데이터베이스 엔진에 연결하는 문제를 해결하는 방법](http://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx)(영문)을 참조하십시오.

* **SQL Server Management Studio**, **tsql** 등의 유틸리티를 사용하여 SQL Server에 **sqooptest** 데이터베이스를 만듭니다. Azure CLI를 사용하는 단계는 Azure SQL Database에만 작동합니다.

    다음 Transact-SQL 문을 사용하여 **mobiledata** 테이블을 만듭니다.

    ```sql
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
    ```

* HDInsight에서 SQL Server에 연결하는 경우 SQL Server의 IP 주소를 사용해야 할 수 있습니다. 예:

    ```bash
    sqoop import --connect 'jdbc:sqlserver://10.0.1.1:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasbs:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

## <a name="limitations"></a>제한 사항

* 대량 내보내기 - Linux 기반 HDInsight와 함께 Microsoft SQL Server 또는 Azure SQL 데이터베이스에 데이터를 내보내는 데 사용된 Sqoop 커넥터도 현재 대량 삽입을 지원하지 않습니다.

* 배치 - Linux 기반 HDInsight에서 삽입을 수행할 때 `-batch` 스위치를 사용하는 경우 Sqoop은 삽입 작업을 일괄 처리하는 대신 여러 번의 삽입 작업을 수행합니다.

## <a name="next-steps"></a>다음 단계

이제 Sqoop을 사용하는 방법에 대해 알아봤습니다. 자세한 내용은 다음을 참조하세요.

* [HDInsight와 함께 Oozie 사용][hdinsight-use-oozie]: Oozie 워크플로에서 Sqoop 작업을 사용합니다.
* [HDInsight를 사용하여 비행 지연 데이터 분석][hdinsight-analyze-flight-data]: Hive를 사용하여 비행 지연 데이터를 분석한 후 Sqoop을 사용하여 데이터를 Azure SQL Database로 내보냅니다.
* [HDInsight에 데이터 업로드][hdinsight-upload-data]: HDInsight/Azure Blob Storage에 데이터를 업로드하는 다른 방법을 찾습니다.

[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[sqldatabase-get-started]: ../sql-database-get-started.md
[sqldatabase-create-configue]: ../sql-database-create-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

