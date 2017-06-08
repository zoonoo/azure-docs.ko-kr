---
title: "Apache Hive와 함께 Beeline 사용 - Azure HDInsight | Microsoft Docs"
description: "Beeline 클라이언트를 사용하여 HDInsight에서 Hadoop과 Hive 쿼리를 실행하는 방법을 알아봅니다. Beeline은 JDBC를 통한 HiveServer2 작업을 위한 유틸리티입니다."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
keywords: beeline hive,hive beeline
ms.assetid: 3adfb1ba-8924-4a13-98db-10a67ab24fca
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/05/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: e75bc8b74f965a0d4509b6967f1cdc7fa32eec56
ms.contentlocale: ko-kr
ms.lasthandoff: 05/18/2017


---
# <a name="use-the-beeline-client-with-apache-hive"></a>Apache Hive와 함께 Beeline 클라이언트 사용

[Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell)을 사용하여 HDInsight에서 Hive 쿼리를 실행하는 방법에 대해 알아봅니다.

Beeline은 HDInsight 클러스터의 헤드 노드에 포함된 Hive 클라이언트입니다. Beeline은 JDBC를 사용하여 HDInsight 클러스터에서 호스팅되는 서비스인 HiveServer2에 연결합니다. 또한 Beeline을 사용하면 인터넷을 통해 HDInsight의 Hive에 원격으로 액세스할 수 있습니다. 다음 표에서는 Beeline과 함께 사용할 연결 문자열을 제공합니다.

| Beeline을 실행하는 위치 | 매개 변수 |
| --- | --- | --- |
| 헤드 노드 또는 에지 노드에 대한 SSH 연결 | `-u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -n admin` |
| 클러스터 외부 | `-u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p password` |

> [!NOTE]
> `admin`을 클러스터의 클러스터 로그인 계정으로 바꿉니다.
>
> `password`를 클러스터 로그인 계정의 암호로 바꿉니다.
>
> `clustername`을 HDInsight 클러스터 이름으로 바꿉니다.

## <a id="prereq"></a>필수 조건

* HDInsight 클러스터의 Linux 기반 Hadoop

  > [!IMPORTANT]
  > Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [Windows에서 HDInsight 사용 중지](hdinsight-component-versioning.md#hdi-version-33-nearing-retirement-date)를 참조하세요.

* SSH 클라이언트 또는 로컬 Beeline 클라이언트입니다. 이 문서에 나온 대부분의 단계는 SSH 세션에서 클러스터까지 Beeline을 사용한다고 가정합니다. 클러스터 외부에서 Beeline을 실행하는 것에 대한 내용은 [Beeline를 원격으로 사용](#remote) 섹션을 참조하세요.

    SSH를 사용하는 방법에 대한 자세한 내용은 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

## <a id="beeline"></a>Beeline 사용

1. Beeline을 시작할 때 HDInsight 클러스터에서 HiveServer2에 대한 연결 문자열을 제공해야 합니다. 클러스터 로그인에 대한 계정 이름을 제공해야 합니다(일반적으로 `admin`). 클러스터 외부에서 명령을 실행하는 경우 클러스터 로그인 암호도 제공해야 합니다. 다음 테이블을 사용하여 연결 문자열 형식 및 사용할 매개 변수를 찾습니다.

    | Beeline을 실행하는 위치 | 매개 변수 |
    | --- | --- | --- |
    | 헤드 노드 또는 에지 노드에 대한 SSH 연결 | `-u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -n admin` |
    | 클러스터 외부 | `-u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p password` |

    예를 들어 다음 명령은 SSH 세션에서 클러스터까지 Beeline을 시작하는 데 사용할 수 있습니다.

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -n admin
    ```

    이 명령은 Beeline 클라이언트를 시작하고 클러스터 헤드 노드의 HiveServer2에 연결합니다. `-n` 매개 변수는 클러스터 로그인 계정을 제공하는 데 사용됩니다. 기본 로그인은 `admin`입니다. 클러스터를 만드는 중에 다른 이름을 사용한 경우 `admin` 대신 이 이름을 사용합니다.

    명령이 완료되면 `jdbc:hive2://headnodehost:10001/>` 프롬프트가 표시됩니다.

2. Beeline 명령은 일반적으로 `!` 문자로 시작합니다. 예를 들어 `!help`는 도움말을 표시합니다. 그러나 일부 명령에서는 `!`를 생략할 수 있습니다. 예를 들어 `help`도 작동합니다.

    HiveQL 문을 실행하는 데 사용되는 `!sql`이 있습니다. 그러나 HiveQL은 너무 일반적으로 사용되어 이전의 `!sql`를 생략할 수 있습니다. 다음 두 문은 동일합니다.

    ```hiveql
    !sql show tables;
    show tables;
    ```

    새 클러스터에서는 **hivesampletable**이라는 테이블 하나만 나열됩니다.

3. 다음 명령을 사용하여 hivesampletable의 스키마를 표시합니다.

    ```bash
    describe hivesampletable;
    ```

    이 명령은 다음 정보를 반환합니다.

        +-----------------------+------------+----------+--+
        |       col_name        | data_type  | comment  |
        +-----------------------+------------+----------+--+
        | clientid              | string     |          |
        | querytime             | string     |          |
        | market                | string     |          |
        | deviceplatform        | string     |          |
        | devicemake            | string     |          |
        | devicemodel           | string     |          |
        | state                 | string     |          |
        | country               | string     |          |
        | querydwelltime        | double     |          |
        | sessionid             | bigint     |          |
        | sessionpagevieworder  | bigint     |          |
        +-----------------------+------------+----------+--+

    이 정보는 테이블의 열을 설명합니다. 이 데이터에 대한 일부 쿼리를 수행하는 동안 대신 새 테이블을 만들어서 데이터를 Hive에 로드하고 스키마를 적용하는 방법을 보여줍니다.

4. 다음 문을 입력하여 HDInsight 클러스터와 함께 제공되는 샘플 데이터로 **log4jLogs**라는 새 테이블을 만듭니다.

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
    ```

    이러한 문은 다음 작업을 수행합니다.

    * `DROP TABLE` - 테이블이 있으면 삭제됩니다.

    * `CREATE EXTERNAL TABLE` - Hive에서 **외부** 테이블을 만듭니다. 외부 테이블만 테이블 정의를 Hive에 저장합니다. 데이터는 원래 위치에 그대로 유지됩니다.

    * `ROW FORMAT` - 데이터의 형식을 지정하는 방식입니다. 이 경우, 각 로그의 필드는 공백으로 구분됩니다.

    * `STORED AS TEXTFILE LOCATION` - 데이터를 저장하는 위치 및 파일 형식입니다.

    * `SELECT` - 열 **t4**에 값 **[ERROR]**가 포함된 모든 행의 수를 선택합니다. 이 값을 포함하는 세 개의 행이 있으므로 이 쿼리는 **3** 값을 반환합니다.

    * `INPUT__FILE__NAME LIKE '%.log'` - Hive는 디렉터리의 모든 파일에 스키마를 적용하려고 합니다. 이 경우 디렉터리에 스키마와 일치하지 않는 파일이 포함되어 있습니다. 결과에 가비지 데이터가 나타나는 것을 방지하기 위해 이 문은 .log로 끝나는 파일의 데이터만 반환해야 함을 Hive에게 알립니다.

  > [!NOTE]
  > 외부 원본에서 기본 데이터를 업데이트하길 원하는 경우에는 외부 테이블을 사용해야 합니다. 예를 들어 자동화된 데이터 업로드 프로세스 또는 MapReduce 작업이 있습니다.
  >
  > 외부 테이블을 삭제하면 데이터는 삭제되지 **않고** 테이블 정의만 삭제됩니다.

    이 명령의 출력은 다음 텍스트와 비슷합니다.

        INFO  : Tez session hasn't been created yet. Opening session
        INFO  :

        INFO  : Status: Running (Executing on YARN cluster with App id application_1443698635933_0001)

        INFO  : Map 1: -/-      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0(+1)/1
        INFO  : Map 1: 1/1      Reducer 2: 1/1
        +----------+--------+--+
        |   sev    | count  |
        +----------+--------+--+
        | [ERROR]  | 3      |
        +----------+--------+--+
        1 row selected (47.351 seconds)

5. Beeline을 종료하려면 `!exit`을 사용합니다.

## <a id="file"></a>Beeline을 사용하여 HiveQL 파일 실행

다음 단계를 사용하여 파일을 만든 다음 Beeline를 사용하여 실행합니다.

1. 다음 명령을 사용하여 **query.hql**이라는 파일을 만듭니다.

    ```bash
    nano query.hql
    ```

2. 다음 텍스트를 파일의 내용으로 사용합니다. 이 쿼리는 **errorLogs**라는 새 '내부' 테이블을 만듭니다.

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    이러한 문은 다음 작업을 수행합니다.

    * **CREATE TABLE IF NOT EXISTS** - 테이블이 아직 없으면 테이블을 만듭니다. **EXTERNAL** 키워드가 사용되지 않으므로 이 문은 내부 테이블을 만듭니다. 내부 테이블은 Hive 데이터 웨어하우스에 저장되며 Hive에 서 완전히 관리됩니다.
    * **STORED AS ORC** - 데이터를 ORC(Optimized Row Columnar) 형식으로 저장합니다. ORC 형식은 Hive 데이터를 저장하기 위해 고도로 최적화되고 효율적인 형식입니다.
    * **덮어쓰기 삽입... SELECT** - **[ERROR]**가 포함된 **log4jLogs** 테이블에서 행을 선택하고 데이터를 **errorLogs** 테이블에 삽입합니다.

    > [!NOTE]
    > 외부 테이블과 달리 내부 테이블을 삭제하면 기본 데이터도 삭제됩니다.

3. 파일을 저장하려면 **Ctrl**+**_X**을 사용한 다음 **Y**를 입력하고 마지막으로 **Enter** 키를 누릅니다.

4. Beeline을 사용하여 파일을 실행하려면 다음을 사용합니다. **HOSTNAME**을 헤드 노드에 가져온 이전 이름으로 바꾸고 **PASSWORD**를 관리자 계정의 암호로 바꿉니다.

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -n admin -i query.hql
    ```

    > [!NOTE]
    > `-i` 매개 변수는 Beeline을 시작하고 query.hql 파일의 문을 실행합니다. 쿼리가 완료되면 `jdbc:hive2://headnodehost:10001/>` 프롬프트가 표시됩니다. 쿼리 완료 후 Beeline을 종료하는 `-f` 매개 변수를 사용하여 파일을 실행할 수도 있습니다.

5. **errorLogs** 테이블을 만들었는지 확인하려면 다음 문을 사용하여 **errorLogs**에서 모든 행을 반환합니다.

    ```hiveql
    SELECT * from errorLogs;
    ```

    데이터 중 t4 열에 모두 **[ERROR]** 가 포함된 3개 행이 반환되어야 합니다.

        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (1.538 seconds)

## <a id="remote"></a>Beeline을 원격으로 사용

Beeline을 로컬로 설치했거나 [sutoiku/beeline](https://hub.docker.com/r/sutoiku/beeline/)과 같은 Docker 이미지를 통해 Beeline을 사용하는 경우 다음 매개 변수를 사용해야 합니다.

* __연결 문자열__: `-u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2'`

* __클러스터 로그인 이름__: `-n admin`

* __클러스터 로그인 암호__ `-p 'password'`

연결 문자열의 `clustername`을 HDInsight 클러스터 이름으로 바꿉니다.

`admin`을 클러스터 로그인 이름으로 바꾸고, `password`를 클러스터 로그인 암호로 바꿉니다.

## <a id="summary"></a><a id="nextsteps"></a>다음 단계

HDInsight의 Hive에 대한 보다 일반적인 내용은 다음 문서를 참조하세요.

* [HDInsight에서 Hadoop과 Hive 사용](hdinsight-use-hive.md)

HDInsight에서 Hadoop을 사용하여 작업할 수 있는 다른 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [HDInsight에서 Hadoop과 Pig 사용](hdinsight-use-pig.md)
* [HDInsight에서 Hadoop과 MapReduce 사용](hdinsight-use-mapreduce.md)

Hive와 함께 Tez를 사용하는 경우 다음 문서를 참조하세요.

* [Windows 기반 HDInsight 클러스터에서 Tez UI 사용](hdinsight-debug-tez-ui.md)
* [Linux 기반 HDInsight에서 Ambari Tez 보기 사용](hdinsight-debug-ambari-tez-view.md)

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md

[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html


[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

