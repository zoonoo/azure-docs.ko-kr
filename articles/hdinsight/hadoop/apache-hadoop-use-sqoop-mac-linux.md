---
title: Apache Hadoop과 Apache Sqoop - Azure HDInsight
description: Apache Sqoop을 사용하여 HDInsight의 Hadoop과 Azure SQL Database 간에 가져오기 및 내보내기를 수행하는 방법을 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/28/2019
ms.openlocfilehash: 21bc903349876a76576fb742840e9899f9d94bcd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74769390"
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-apache-hadoop-on-hdinsight-and-sql-database"></a>Apache Sqoop을 사용하여 HDInsight의 Hadoop과 SQL Database 간에 데이터 가져오기 및 내보내기

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Apache Sqoop을 사용하여 Azure HDInsight의 Hadoop 클러스터와 Azure SQL Database 또는 Microsoft SQL Server 데이터베이스 사이에서 가져오기 및 내보내기를 수행하는 방법을 알아봅니다. 이 문서의 단계에서는 Hadoop 클러스터의 헤드에서 직접 `sqoop` 명령을 사용합니다. SSH를 사용하여 헤드 노드에 연결하고 이 문서의 명령을 실행합니다. 이 문서는 [HDInsight에서 하두롭과 아파치 Sqoop사용의](./hdinsight-use-sqoop.md)연속이다.

## <a name="prerequisites"></a>사전 요구 사항

* [HDInsight에서 하두롭과 아파치 Sqoop사용에서](./hdinsight-use-sqoop.md) [테스트 환경 설정](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) 완료 .

* SSH 클라이언트. 자세한 내용은 [SSH를 사용하여 HDInsight(Apache Hadoop)에 연결](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

* Sqoop에 익숙합니다. 자세한 내용은 [Sqoop 사용자 가이드를](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html)참조하십시오.

## <a name="set-up"></a>설정

1. [ssh 명령을](../hdinsight-hadoop-linux-use-ssh-unix.md) 사용하여 클러스터에 연결합니다. CLUSTERNAME을 클러스터 이름으로 바꿉니다 아래 명령을 편집한 다음 명령을 입력합니다.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 사용 편의성을 위해 변수를 설정합니다. `PASSWORD`을 `MYSQLSERVER`대체하고 `MYDATABASE` 관련 값으로 입력한 다음 아래 명령을 입력합니다.

    ```bash
    export password='PASSWORD'
    export sqlserver="MYSQLSERVER"
    export database="MYDATABASE"


    export serverConnect="jdbc:sqlserver://$sqlserver.database.windows.net:1433;user=sqluser;password=$password"
    export serverDbConnect="jdbc:sqlserver://$sqlserver.database.windows.net:1433;user=sqluser;password=$password;database=$database"
    ```

## <a name="sqoop-export"></a>Sqoop 내보내기

하이브에서 SQL 서버까지.

1. Sqoop이 SQL 데이터베이스를 볼 수 있는지 확인하려면 열려 있는 SSH 연결에서 아래 명령을 입력합니다. 이 명령은 데이터베이스 목록을 반환합니다.

    ```bash
    sqoop list-databases --connect $serverConnect
    ```

1. 다음 명령을 입력하여 지정된 데이터베이스에 대한 테이블 목록을 볼 수 있습니다.

    ```bash
    sqoop list-tables --connect $serverDbConnect
    ```

1. Hive `hivesampletable` 테이블의 데이터를 SQL `mobiledata` Database의 테이블로 내보내려면 열려 있는 SSH 연결에 아래 명령을 입력합니다.

    ```bash
    sqoop export --connect $serverDbConnect \
    -table mobiledata \
    --hcatalog-table hivesampletable
    ```

1. 데이터를 내보냈는지 확인하려면 SSH 연결의 다음 쿼리를 사용하여 내보낸 데이터를 봅니다.

    ```bash
    sqoop eval --connect $serverDbConnect \
    --query "SELECT COUNT(*) from dbo.mobiledata WITH (NOLOCK)"


    sqoop eval --connect $serverDbConnect \
    --query "SELECT TOP(10) * from dbo.mobiledata WITH (NOLOCK)"
    ```

## <a name="sqoop-import"></a>Sqoop 가져오기

SQL 서버에서 Azure 저장소로.

1. SQL Database의 `mobiledata` 테이블에서 HDInsight의 `wasbs:///tutorials/usesqoop/importeddata` 디렉터리로 데이터를 가져오려면 열려 있는 SSH 연결에서 아래 명령을 입력합니다. 데이터의 필드는 탭 문자로 구분되어 있으며 줄은 줄 바꿈 문자로 종료됩니다.

    ```bash
    sqoop import --connect $serverDbConnect \
    --table mobiledata \
    --target-dir 'wasb:///tutorials/usesqoop/importeddata' \
    --fields-terminated-by '\t' \
    --lines-terminated-by '\n' -m 1
    ```

1. 또는 Hive 테이블을 지정할 수도 있습니다.

    ```bash
    sqoop import --connect $serverDbConnect \
    --table mobiledata \
    --target-dir 'wasb:///tutorials/usesqoop/importeddata2' \
    --fields-terminated-by '\t' \
    --lines-terminated-by '\n' \
    --create-hive-table \
    --hive-table mobiledata_imported2 \
    --hive-import -m 1
    ```

1. 가져오기가 완료되면 열려 있는 SSH 연결에 다음 명령을 입력하여 새 디렉터리에서 데이터를 나열합니다.

    ```bash
    hadoop fs -tail /tutorials/usesqoop/importeddata/part-m-00000
    ```

1. [Beeline을](./apache-hadoop-use-hive-beeline.md) 사용하여 테이블이 Hive에서 만들어졌는지 확인합니다.

    1. 연결

        ```bash
        beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
        ```

    1. 각 쿼리를 한 번에 하나씩 실행하고 출력을 검토합니다.

        ```hql
        show tables;
        describe mobiledata_imported2;
        SELECT COUNT(*) FROM mobiledata_imported2;
        SELECT * FROM mobiledata_imported2 LIMIT 10;
        ```

    1. 을 통해 `!exit`비라인을 종료합니다.

## <a name="limitations"></a>제한 사항

* 대량 내보내기 - Linux 기반 HDInsight를 사용하면 데이터를 Microsoft SQL Server 또는 Azure SQL Database로 내보내는 데 사용되는 Sqoop 커넥터는 대량 삽입을 지원하지 않습니다.

* Batch - Linux 기반 HDInsight에서 삽입을 수행할 때 `-batch` 스위치를 사용하는 경우 Sqoop은 삽입 작업을 일괄 처리하는 대신 여러 번의 삽입 작업을 수행합니다.

## <a name="important-considerations"></a>중요 고려 사항

* HDInsight와 SQL Server가 모두 동일한 Azure Virtual Network에 있어야 합니다.

    예제를 보려면 [온-프레미스 네트워크에 HDInsight 연결](./../connect-on-premises-network.md) 문서를 참조하세요.

    HDInsight와 함께 Azure Virtual Network를 사용하는 방법에 대한 자세한 내용은 [Azure Virtual Network를 사용하여 HDInsight 확장](../hdinsight-plan-virtual-network-deployment.md) 문서를 참조하세요. Azure Virtual Network에 대한 자세한 내용은 [가상 네트워크 개요](../../virtual-network/virtual-networks-overview.md) 문서를 참조하세요.

* SQL Server는 SQL 인증을 허용하도록 구성되어야 합니다. 자세한 내용은 [인증 모드 선택](https://msdn.microsoft.com/ms144284.aspx) 문서를 참조하세요.

* SQL Server를 원격 연결을 허용하도록 구성해야 할 수 있습니다. 자세한 내용은 [SQL Server 데이터베이스 엔진에 연결하는 문제를 해결하는 방법](https://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx)(영문)을 참조하십시오.

## <a name="next-steps"></a>다음 단계

이제 Sqoop을 사용하는 방법을 배웠습니다. 자세한 내용은 다음을 참조하세요.

* [HDInsight와 아파치 Oozie를 사용](../hdinsight-use-oozie-linux-mac.md): Oozie 워크플로우에서 Sqoop 작업을 사용합니다.
* [HDInsight를 사용하여 비행 지연 데이터 분석](../interactive-query/interactive-query-tutorial-analyze-flight-data.md): 대화형 쿼리를 사용하여 비행 지연 데이터를 분석한 다음 Sqoop을 사용하여 데이터를 Azure SQL 데이터베이스로 내보냅니다.
* [HDInsight에 데이터 업로드](../hdinsight-upload-data.md): HDInsight/Azure Blob Storage에 데이터를 업로드하는 다른 방법을 찾습니다.
