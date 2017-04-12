---
title: "HDInsight(Hadoop)에서 Beeline을 사용하여 Hive 작업| Microsoft 문서"
description: "SSH를 사용하여 HDInsight의 Hadoop 클러스터에 연결하고 Beeline을 사용하여 Hive 쿼리를 대화형으로 제출하는 방법을 알아봅니다. Beeline은 JDBC를 통한 HiveServer2 작업을 위한 유틸리티입니다."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 3adfb1ba-8924-4a13-98db-10a67ab24fca
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/17/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: b015e3d99c72c1840a31bacee5d9655fa9f48da7
ms.lasthandoff: 03/25/2017


---
# <a name="use-hive-with-hadoop-in-hdinsight-with-beeline"></a>Beeline를 사용하여 HDInsight에서 Hadoop과 Hive 사용
[!INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

이 문서에서는 SSH(Secure Shell)를 사용하여 Linux 기반 HDInsight 클러스터에 연결한 다음 [Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) 명령줄 도구를 사용하여 Hive 쿼리를 대화형으로 제출하는 방법을 배웁니다.

> [!NOTE]
> Beeline은 JDBC를 사용하여 Hive에 연결합니다. Hive로 JDBC를 사용하는 자세한 내용은 [Hive JDBC 드라이버를 사용하여 Azure HDInsight에서 Hive에 연결](hdinsight-connect-hive-jdbc-driver.md)을 참조하세요.

## <a id="prereq"></a>필수 조건
이 문서의 단계를 완료하려면 다음이 필요합니다.

* HDInsight 클러스터의 Linux 기반 Hadoop

  > [!IMPORTANT]
  > Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [Windows에서 HDInsight 사용 중단](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)을 참조하세요.

* SSH 클라이언트. Linux, Unix 및 Mac OS에는 SSH 클라이언트가 함께 제공됩니다. Windows 사용자는 [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)와 같은 클라이언트를 다운로드해야 합니다.

## <a id="ssh"></a>SSH를 사용하여 연결
SSH 명령을 사용하여 HDInsight 클러스터의 FQDN(정규화된 도메인 이름)에 연결합니다. FQDN은 지정한 클러스터 이름과 그 뒤에 오는 **.azurehdinsight.net**으로 구성됩니다. 예를 들어 다음 명령은 **myhdinsight**라는 클러스터에 연결합니다.

    ssh admin@myhdinsight-ssh.azurehdinsight.net

**SSH 인증을 위해 인증서 키를 제공한 경우** HDInsight 클러스터를 만들 때 클라이언트 시스템에서 개인 키의 위치를 지정해야 할 수도 있습니다.

    ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**SSH 인증을 위해 암호를 제공한 경우** HDInsight 클러스터를 만들 때 메시지가 표시되면 암호를 제공해야 합니다.

HDInsight에서의 SSH 사용에 대한 자세한 내용은 [HDInsight에서 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

## <a id="beeline"></a>Beeline 명령 사용
1. 연결되면 다음을 사용하여 Beeline을 시작합니다.
   
        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin
   
    Beeline 클라이언트를 시작하고 JDBC url에 연결합니다. 여기에서 HiveServer2가 클러스터의 두 헤드 노드에서 실행되기 때문에 `localhost` 을 사용하고 기본 헤드 노드에서 직접 Beeline를 실행합니다.
   
    명령이 완료되면 `jdbc:hive2://localhost:10001/>` 프롬프트가 표시됩니다.
2. Beeline 명령은 일반적으로 `!` 문자로 시작합니다. 예를 들어 `!help`는 도움말을 표시합니다. 그러나 `!`은 생략될 수 있습니다. 예를 들어 `help`도 작동합니다.
   
    도움말을 보면 `!sql`는 HiveQL 문을 실행하는 데 사용됩니다. 그러나 HiveQL은 너무 일반적으로 사용되어 이전의 `!sql`를 생략할 수 있습니다. 다음 두 문의 결과가 정확하게 동일하며 하이브를 통해 현재 사용 가능한 테이블을 표시합니다.
   
        !sql show tables;
        show tables;
   
    새 클러스터에서 **hivesampletable**라는 하나의 테이블을 나열합니다.
3. 다음을 사용하여 hivesampletable에 스키마를 표시합니다.
   
        describe hivesampletable;
   
    다음의 정보를 반환합니다.
   
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
   
    테이블에 열을 표시합니다. 이 데이터에 대한 일부 쿼리를 수행하는 동안 대신 새 테이블을 만들어서 데이터를 Hive에 로드하고 스키마를 적용하는 방법을 보여줍니다.
4. HDInsight 클러스터로 제공되는 샘플 데이터를 사용하여 **log4jLogs** 라는 새 테이블을 만들기 위해 다음 문을 입력합니다.
   
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
   
    이러한 문은 다음 작업을 수행합니다.
   
   * **DROP TABLE** - 테이블이 이미 있는 경우 테이블과 데이터 파일을 삭제합니다.
   * **CREATE EXTERNAL TABLE** - Hive에서 새 ‘외부’ 테이블을 만듭니다. 외부 테이블만 테이블 정의를 Hive에 저장합니다. 데이터는 원래 위치에 그대로 유지됩니다.
   * **ROW FORMAT** - 데이터의 형식 지정 방식을 Hive에 알립니다. 이 경우, 각 로그의 필드는 공백으로 구분됩니다.
   * **STORED AS TEXTFILE LOCATION** - 데이터가 저장된 위치(example/data 디렉터리) 및 텍스트로 저장되었음을 Hive에 알립니다.
   * **SELECT** - **t4** 열에 **[ERROR]** 값이 포함된 모든 행의 수를 선택합니다. 이 경우 이 값을 포함하는 행이 3개 있으므로 **3** 값이 반환되어야 합니다.
   * **INPUT__FILE__NAME LIKE '%.log'** - .log로 끝나는 파일의 데이터만 반환하도록 Hive에 지시합니다. 일반적으로 hive를 쿼리할 때 동일한 폴더 내에서 동일한 스키마를 사용하는 데이터가 있지만 이 예제 로그 파일은 다른 데이터 형식과 저장됩니다.
     
     > [!NOTE]
     > 자동화된 데이터 업로드 프로세스와 같은 외부 원본이나 또 다른 MapReduce 작업을 통해 기본 데이터를 업데이트해야 하지만 Hive 쿼리에서 항상 최신 데이터를 사용하려고 할 경우 외부 테이블을 사용해야 합니다.
     > 
     > 외부 테이블을 삭제하면 데이터는 삭제되지 **않고** 테이블 정의만 삭제됩니다.
     > 
     > 
     
     이 명령의 출력은 다음과 유사합니다.

     ```
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
     ```
5. Beeline을 종료하려면 `!quit`을 사용합니다.

## <a id="file"></a>HiveQL 파일 실행
또한 Beeline은 HiveQL 문을 포함하는 파일을 실행하는 데 사용할 수 있습니다. 다음 단계를 사용하여 파일을 만든 다음 Beeline를 사용하여 실행합니다.

1. 다음 명령을 사용하여 **query.hql**이라는 새 파일을 만듭니다.
   
        nano query.hql
2. 편집기가 열리면 파일의 내용으로 다음을 사용합니다. 이 쿼리는 **errorLogs**라는 새 '내부' 테이블을 만듭니다.
   
        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
   
    이러한 문은 다음 작업을 수행합니다.
   
   * **CREATE TABLE IF NOT EXISTS** - 테이블이 아직 없는 경우 테이블을 만듭니다. **EXTERNAL** 키워드가 사용되지 않으면 Hive 데이터 웨어하우스에 저장되고 Hive에서 완전히 관리되는 내부 테이블입니다.
   * **STORED AS ORC** - 데이터를 ORC(Optimized Row Columnar) 형식으로 저장합니다. Hive 데이터를 저장하기 위한 고도로 최적화되고 효율적인 형식입니다.
   * **덮어쓰기 삽입... SELECT** - **[ERROR]**가 포함된 **log4jLogs** 테이블에서 행을 선택하고 데이터를 **errorLogs** 테이블에 삽입합니다.
     
     > [!NOTE]
     > 외부 테이블과 달리 내부 테이블을 삭제하면 기본 데이터도 삭제됩니다.
     > 
     > 
3. 파일을 저장하려면 **Ctrl**+**_X**을 사용한 다음 **Y**를 입력하고 마지막으로 **Enter** 키를 누릅니다.
4. Beeline을 사용하여 파일을 실행하려면 다음을 사용합니다. **HOSTNAME**을 헤드 노드에 가져온 이전 이름으로 바꾸고 **PASSWORD**를 관리자 계정의 암호로 바꿉니다.
   
        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin -i query.hql
   
   > [!NOTE]
   > `-i` 매개 변수는 Beeline을 시작하고 query.hql 파일의 문을 실행한 다음 `jdbc:hive2://localhost:10001/>` 프롬프트에서 Beeline에 유지됩니다. `-f` 매개 변수를 사용하여 파일을 실행할 수도 있으며, 이렇게 하면 파일이 처리된 후에 Bash를 반환합니다.
   > 
   > 
5. **errorLogs** 테이블을 만들었는지 확인하려면 다음 문을 사용하여 **errorLogs**에서 모든 행을 반환합니다.
   
        SELECT * from errorLogs;
   
    데이터 중 t4 열에 모두 **[ERROR]** 가 포함된 3개 행이 반환되어야 합니다.
   
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (1.538 seconds)

## <a name="more-about-beeline-connectivity"></a>Beeline 연결에 대한 자세한 정보
이 문서의 단계에서는 `localhost`를 사용하여 클러스터 헤드 노드에서 실행 중인 HiveServer2에 연결합니다. 헤드 노드의 FQDN(정규화된 도메인 이름)이나 호스트 이름을 사용할 수도 있지만 해당 프로세스에는 추가 단계가 필요합니다(호스트 이름 또는 FQDN을 찾는 단계). 헤드 노드의 Beeline을 사용하는 경우 `localhost`를 사용하면 충분합니다.

Beeline이 설치되어 있는 클러스터에 에지 노드가 있는 경우 헤드 노드의 FQDN이나 호스트 이름을 사용하여 연결해야 합니다.

클러스터의 클라이언트 외부에 Beeline이 설치되어 있으면 다음 명령을 사용하여 연결할 수 있습니다. **CLUSTERNAME** 을 HDInsight 클러스터의 이름으로 바꿉니다. **PASSWORD**를 관리자(HTTP 로그인) 계정의 암호로 바꿉니다.

    beeline -u 'jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=hive2' -n admin -p PASSWORD

매개 변수/URI가 헤드 노드에서 직접 실행하거나 클러스터 내 에지 노드에서 실행하는 경우와 다르니 유의하세요. 이것은 인터넷에서 클러스터에 연결하는 경우 443 포트를 통해 트래픽을 라우팅하는 공용 게이트웨이를 사용하기 때문입니다. 또한, 몇 가지 다른 서비스가 443 포트에서 공용 게이트웨이를 통해 노출되기 때문에 URI가 직접 연결하는 경우와 다릅니다. 인터넷에서 연결하는 경우 암호를 제공하여 세션도 인증해야 합니다.

## <a id="summary"></a><a id="nextsteps"></a>다음 단계
여기에서 볼 수 있듯이 Beeline 명령을 사용하면 쉽게 HDInsight 클러스터에서 대화형으로 Hive 쿼리를 실행할 수 있습니다.

HDInsight의 Hive에 대한 일반적인 정보:

* [HDInsight에서 Hadoop과 Hive 사용](hdinsight-use-hive.md)

HDInsight에서 Hadoop으로 작업하는 다른 방법에 관한 정보:

* [HDInsight에서 Hadoop과 Pig 사용](hdinsight-use-pig.md)
* [HDInsight에서 Hadoop과 MapReduce 사용](hdinsight-use-mapreduce.md)

Hive와 함께 Tez를 사용하는 경우 디버깅 정보에 대한 다음 문서를 참조하세요.

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


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


