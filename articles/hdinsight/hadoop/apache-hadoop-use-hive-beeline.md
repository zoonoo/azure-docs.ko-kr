---
title: Apache Hive와 Apache Beeline 사용 - Azure HDInsight
description: Beeline 클라이언트를 사용하여 HDInsight에서 Hadoop과 Hive 쿼리를 실행하는 방법을 알아봅니다. Beeline은 JDBC를 통한 HiveServer2 작업을 위한 유틸리티입니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 4f2b192765aab4c7cf18c62988ae2f6080b4b17c
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80436894"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>Apache Hive와 Apache Beeline 클라이언트 사용

[Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell)을 사용하여 HDInsight에서 Apache Hive 쿼리를 실행하는 방법에 대해 알아봅니다.

Beeline은 HDInsight 클러스터의 헤드 노드에 포함된 Hive 클라이언트입니다. Beeline을 로컬로 설치하려면 아래 [Beeline 클라이언트 설치를](#install-beeline-client)참조하십시오. Beeline은 JDBC를 사용하여 HDInsight 클러스터에서 호스팅되는 서비스인 HiveServer2에 연결합니다. 또한 Beeline을 사용하면 인터넷을 통해 HDInsight의 Hive에 원격으로 액세스할 수 있습니다. 다음 예제는 Beeline에서 HDInsight에 연결하는 데 사용되는 가장 일반적인 연결 문자열을 제공합니다.

## <a name="types-of-connections"></a>연결 유형

### <a name="from-an-ssh-session"></a>SSH 세션에서

SSH 세션에서 클러스터 헤드노드에 연결할 때 포트의 `headnodehost` `10001`주소에 연결할 수 있습니다.

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
```

---

### <a name="over-an-azure-virtual-network"></a>Azure 가상 네트워크 이상

클라이언트에서 Azure 가상 네트워크를 통해 HDInsight에 연결할 때 클러스터 헤드 노드의 정규화된 도메인 이름(FQDN)을 제공해야 합니다. 이 연결은 클러스터 노드로 직접 설정되므로 연결은 포트 `10001`을 사용합니다.

```bash
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

클러스터 `<headnode-FQDN>` 헤드노드의 정규화된 도메인 이름으로 바꿉니다. 헤드 노드의 정규화된 도메인 이름을 찾으려면 [Apache Ambari REST API를 사용하여 HDInsight 관리](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes) 문서의 정보를 사용합니다.

---

### <a name="to-hdinsight-enterprise-security-package-esp-cluster-using-kerberos"></a>Kerberos를 사용하여 HDInsight 엔터프라이즈 보안 패키지(ESP) 클러스터로

클라이언트에서 ESP(엔터프라이즈 보안 패키지) 클러스터에 연결할 때 클러스터의 동일한 영역에 있는 컴퓨터에서 Azure Active Directory(AAD) -DS에 가입한 경우 클러스터에 `<AAD-Domain>` `<username>`액세스할 수 있는 권한이 있는 도메인 이름과 도메인 사용자 계정의 이름도 지정해야 합니다.

```bash
kinit <username>
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
```

`<username>`을 클러스터에 액세스할 수 있는 권한이 있는 도메인의 계정 이름으로 바꿉니다. 클러스터가 조인되는 AAD(Azure Active Directory)의 이름으로 바꿉니다. `<AAD-DOMAIN>` 값에 대문자 문자열을 `<AAD-DOMAIN>` 사용 하 여 그렇지 않으면 자격 증명을 찾을 수 없습니다. 필요한 `/etc/krb5.conf` 경우 영역 이름을 확인합니다.

Ambari에서 JDBC URL을 찾으려면 다음을 수행하십시오.

1. 웹 브라우저에서 클러스터 `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary`의 `CLUSTERNAME` 이름으로 이동합니다. HiveServer2가 실행 되고 있는지 확인합니다.

1. 클립보드를 사용하여 HiveServer2 JDBC URL을 복사합니다.

---

### <a name="over-public-or-private-endpoints"></a>공용 또는 비공개 끝점 초과

공용 또는 개인 끝점을 사용하여 클러스터에 연결할 때 클러스터 로그인 계정 `admin`이름(기본값)과 암호를 제공해야 합니다. 예를 들어 클라이언트 시스템에서 Beeline을 사용하여 `clustername.azurehdinsight.net` 주소에 연결합니다. 이 연결은 포트를 `443`통해 이루어지며 TLS/SSL을 사용하여 암호화됩니다.

`clustername`을 HDInsight 클러스터 이름으로 바꿉니다. `admin`을 클러스터의 클러스터 로그인 계정으로 바꿉니다. ESP 클러스터의 경우 전체 UPN(예: user@domain.com)을 사용합니다. `password`를 클러스터 로그인 계정의 암호로 바꿉니다.

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

또는 개인 끝점:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

전용 끝점은 동일한 리전에서 피어온 VNET에서만 액세스할 수 있는 기본 로드 밸러블러를 가리킵니다. 자세한 내용은 [전역 VNet 피어링 및 로드 밸러저에 대한 제약 조건을](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) 참조하십시오. Beeline을 `curl` 사용하기 `-v` 전에 옵션과 함께 명령을 사용하여 공용 또는 개인 끝점의 연결 문제를 해결할 수 있습니다.

---

### <a name="use-beeline-with-apache-spark"></a>Apache Spark와 함께 Beeline 사용

Apache Spark는 자체적으로 HiveServer2를 구현하며, HiveServer2는 종종 Spark Thrift 서버라고 합니다. 이 서비스는 Hive 대신 Spark SQL을 사용하여 쿼리를 해결하고, 쿼리에 따라 더 나은 성능을 제공할 수 있습니다.

#### <a name="through-public-or-private-endpoints"></a>공용 또는 비공개 끝점을 통해

사용되는 연결 문자열은 약간 다릅니다. `httpPath=/hive2` 그것을 포함하는 대신 `httpPath/sparkhive2`. `clustername`을 HDInsight 클러스터 이름으로 바꿉니다. `admin`을 클러스터의 클러스터 로그인 계정으로 바꿉니다. ESP 클러스터의 경우 전체 UPN(예: user@domain.com)을 사용합니다. `password`를 클러스터 로그인 계정의 암호로 바꿉니다.

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

또는 개인 끝점:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

전용 끝점은 동일한 리전에서 피어온 VNET에서만 액세스할 수 있는 기본 로드 밸러블러를 가리킵니다. 자세한 내용은 [전역 VNet 피어링 및 로드 밸러저에 대한 제약 조건을](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) 참조하십시오. Beeline을 `curl` 사용하기 `-v` 전에 옵션과 함께 명령을 사용하여 공용 또는 개인 끝점의 연결 문제를 해결할 수 있습니다.

---

#### <a name="from-cluster-head-or-inside-azure-virtual-network-with-apache-spark"></a>클러스터 헤드 또는 아파치 스파크가 있는 Azure 가상 네트워크 내부에서

클러스터 헤드 노드에서 직접 연결하거나 HDInsight 클러스터와 동일한 Azure Virtual Network 내부의 리소스에서 연결하는 경우 `10001` 포트 대신 `10002` 포트를 Spark Thrift 서버에 사용해야 합니다. 다음 예제에서는 헤드 노드에 직접 연결하는 방법을 보여 주며 있습니다.

```bash
/usr/hdp/current/spark2-client/bin/beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

---

## <a name="prerequisites-for-examples"></a>예에 대한 필수 조건

* HDInsight의 하두프 클러스터. [리눅스에서 HDInsight로 시작하기를](./apache-hadoop-linux-tutorial-get-started.md)참조하십시오.

* 클러스터의 기본 저장소에 대한 [URI 구성표를](../hdinsight-hadoop-linux-information.md#URI-and-scheme) 확인합니다. 예를 들어 `wasb://` Azure 저장소, `abfs://` Azure 데이터 레이크 저장소 `adl://` Gen2 또는 Azure 데이터 레이크 저장소 Gen1의 경우입니다. Azure 저장소에 대해 보안 전송을 `wasbs://`사용하도록 설정하면 URI는 . 자세한 내용은 [보안 전송](../../storage/common/storage-require-secure-transfer.md)을 참조하십시오.

* 옵션 1: SSH 클라이언트입니다. 자세한 내용은 [SSH를 사용하여 HDInsight(Apache Hadoop)에 연결](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요. 이 문서의 대부분의 단계는 SSH 세션에서 클러스터로 Beeline을 사용하고 있다고 가정합니다.

* 옵션 2: 로컬 Beeline 클라이언트입니다.

## <a name="run-a-hive-query"></a>HIVE 쿼리 실행

이 예제는 SSH 연결에서 Beeline 클라이언트를 사용하는 것을 기반으로 합니다.

1. 아래 코드와 함께 클러스터에 대한 SSH 연결을 엽니다. `sshuser`은 클러스터의 SSH 사용자로, `CLUSTERNAME`은 클러스터 이름으로 바꿉니다. 메시지가 표시되면 SSH 사용자 계정의 암호를 입력합니다.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. 다음 명령을 입력하여 열려 있는 SSH 세션에서 Beeline 클라이언트와 HiveServer2에 연결합니다.

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

3. Beeline 명령은 일반적으로 `!` 문자로 시작합니다. 예를 들어 `!help`는 도움말을 표시합니다. 그러나 일부 명령에서는 `!`를 생략할 수 있습니다. 예를 들어 `help`도 작동합니다.

    HiveQL 문을 실행하는 데 사용되는 `!sql`것은 있습니다. 그러나 HiveQL은 너무 일반적으로 사용되어 이전의 `!sql`를 생략할 수 있습니다. 다음 두 문은 동일합니다.

    ```hiveql
    !sql show tables;
    show tables;
    ```

    새 클러스터에서는 **hivesampletable**이라는 테이블 하나만 나열됩니다.

4. 다음 명령을 사용하여 hivesampletable의 스키마를 표시합니다.

    ```hiveql
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

    이 정보는 테이블의 열을 설명합니다.

5. HDInsight 클러스터와 함께 제공된 샘플 데이터를 사용하여 **log4jLogs라는** 테이블을 만들려면 다음 문을 [입력합니다.](../hdinsight-hadoop-linux-information.md#URI-and-scheme)

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs
        WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log'
        GROUP BY t4;
    ```

    이러한 명령문은 다음과 같은 작업을 수행합니다.

    |인수를 제거합니다. |Description |
    |---|---|
    |DROP TABLE|테이블이 있으면 삭제됩니다.|
    |외부 테이블 만들기|Hive에서 **외부** 테이블을 만듭니다. 외부 테이블만 테이블 정의를 Hive에 저장합니다. 데이터는 원래 위치에 그대로 유지됩니다.|
    |행 형식|데이터의 서식이 지정되는 방식입니다. 이 경우, 각 로그의 필드는 공백으로 구분됩니다.|
    |텍스트 파일 위치로 저장|데이터가 저장되는 위치 및 파일 형식입니다.|
    |선택|열 **t4에** 값 **[ERROR]이**포함된 모든 행의 개수를 선택합니다. 이 값을 포함하는 세 개의 행이 있으므로 이 쿼리는 **3** 값을 반환합니다.|
    |INPUT__FILE__NAME '%.log'를 좋아합니다.|Hive는 디렉터리내의 모든 파일에 스키마를 적용하려고 시도합니다. 이 경우 디렉터리에는 스키마와 일치하지 않는 파일이 포함되어 있습니다. 결과에 가비지 데이터가 나타나는 것을 방지하기 위해 이 명령문은 .log로 끝나는 파일의 데이터만 반환해야 함을 Hive에게 알립니다.|

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

6. 비라인 출구:

    ```bash
    !exit
    ```

## <a name="run-a-hiveql-file"></a>HiveQL 파일 실행

이는 이전 예제의 연속입니다. 다음 단계를 사용하여 파일을 만든 다음 Beeline를 사용하여 실행합니다.

1. 다음 명령을 사용하여 **query.hql**이라는 파일을 만듭니다.

    ```bash
    nano query.hql
    ```

1. 다음 텍스트를 파일의 내용으로 사용합니다. 이 쿼리는 **errorLogs**라는 새 '내부' 테이블을 만듭니다.

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    이러한 명령문은 다음과 같은 작업을 수행합니다.

    |인수를 제거합니다. |Description |
    |---|---|
    |테이블 만들기 존재하지 않는 경우|테이블이 아직 존재하지 않으면 테이블이 만들어집니다. **외부** 키워드가 사용되지 않으므로 이 문은 내부 테이블을 만듭니다. 내부 테이블은 Hive 데이터 웨어하우스에 저장되며 Hive에 서 완전히 관리됩니다.|
    |ORC로 저장|데이터를 ORC(Optimized Row Columnar) 형식으로 저장합니다. ORC 형식은 Hive 데이터를 저장하기 위해 고도로 최적화되고 효율적인 형식입니다.|
    |덮어 쓰기 삽입 ... 선택|**[ERROR]** 가 포함된 **log4jLogs** 테이블에서 행을 선택하고 데이터를 **errorLogs** 테이블에 삽입합니다.|

    > [!NOTE]  
    > 외부 테이블과 달리 내부 테이블을 삭제하면 기본 데이터도 삭제됩니다.

1. 파일을 저장하려면 **Ctrl**+**X를**사용하여 **Y를**입력하고 마지막으로 **enter**.

1. 다음을 사용하여 Beeline을 통해 파일을 실행합니다.

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]  
    > `-i` 매개 변수는 Beeline을 시작하고 `query.hql` 파일의 문을 실행합니다. 쿼리가 완료되면 `jdbc:hive2://headnodehost:10001/>` 프롬프트가 표시됩니다. 쿼리 완료 후 Beeline을 종료하는 `-f` 매개 변수를 사용하여 파일을 실행할 수도 있습니다.

1. **errorLogs** 테이블을 만들었는지 확인하려면 다음 문을 사용하여 **errorLogs**에서 모든 행을 반환합니다.

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
        3 rows selected (0.813 seconds)

## <a name="install-beeline-client"></a>Beeline 클라이언트 설치

Beeline은 HDInsight 클러스터의 헤드 노드에 포함되어 있지만 로컬 컴퓨터에 설치할 수 있습니다.  로컬 컴퓨터에 Beeline을 설치 하는 아래 단계는 [리눅스에 대 한 Windows 하위 시스템을](https://docs.microsoft.com/windows/wsl/install-win10)기반으로 합니다.

1. 패키지 목록을 업데이트합니다. bash 셸에 다음 명령을 입력합니다.

    ```bash
    sudo apt-get update
    ```

1. 설치되지 않은 경우 Java를 설치합니다. 명령으로 `which java` 확인할 수 있습니다.

    1. Java 패키지가 설치되어 있지 않은 경우 다음 명령을 입력합니다.

        ```bash
        sudo apt install openjdk-11-jre-headless
        ```

    1. bashrc 파일을 엽니다 (일반적으로 ~/.bashrc에서 발견): `nano ~/.bashrc`.

    1. bashrc 파일을 수정합니다. 파일 끝에 다음 줄을 추가합니다.

        ```bash
        export JAVA_HOME=/usr/lib/jvm/java-1.11.0-openjdk-amd64
        ```

        그런 다음 **Ctrl+X**, **Y를**누릅니다.

1. 하두프와 비라인 아카이브를 다운로드하고 다음 명령을 입력합니다.

    ```bash
    wget https://archive.apache.org/dist/hadoop/core/hadoop-2.7.3/hadoop-2.7.3.tar.gz
    wget https://archive.apache.org/dist/hive/hive-1.2.1/apache-hive-1.2.1-bin.tar.gz
    ```

1. 아카이브의 압축을 풀고 다음 명령을 입력합니다.

    ```bash
    tar -xvzf hadoop-2.7.3.tar.gz
    tar -xvzf apache-hive-1.2.1-bin.tar.gz
    ```

1. 또한 bashrc 파일을 수정합니다. 아카이브의 압축을 풀었던 경로를 식별해야 합니다. [Linux용 Windows 하위 시스템을](https://docs.microsoft.com/windows/wsl/install-win10)사용하고 단계를 정확하게 수행한 경우 `/mnt/c/Users/user/` `user` 경로는 다음과 같습니다.

    1. 파일을 엽니다.`nano ~/.bashrc`

    1. 적절한 경로로 아래 명령을 수정한 다음 bashrc 파일의 끝에 입력합니다.

        ```bash
        export HADOOP_HOME=/path_where_the_archives_were_unpacked/hadoop-2.7.3
        export HIVE_HOME=/path_where_the_archives_were_unpacked/apache-hive-1.2.1-bin
        PATH=$PATH:$HIVE_HOME/bin
        ```

    1. 그런 다음 **Ctrl+X**, **Y를**누릅니다.

1. 닫은 다음 bash 세션을 다시 엽니다.

1. 연결을 테스트합니다. 위의 [공용 또는 개인 끝점 에서](#over-public-or-private-endpoints)연결 형식을 사용합니다.

## <a name="next-steps"></a>다음 단계

* HDInsight의 하이브에 대한 자세한 내용은 [HDInsight에서 아파치 하두프와 아파치 하이브 사용을](hdinsight-use-hive.md) 참조하십시오.

* HDInsight에서 Hadoop으로 작업할 수 있는 다른 방법에 대한 자세한 내용은 [HDInsight의 아파치 하도롭과 함께 MapReduce 사용을](hdinsight-use-mapreduce.md) 참조하십시오.
