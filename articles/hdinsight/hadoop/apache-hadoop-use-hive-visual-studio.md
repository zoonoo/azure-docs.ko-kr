---
title: 비주얼 스튜디오를 위한 아파치 하이브 & 데이터 레이크 도구 - Azure HDInsight
description: Azure HDInsight의 Apache Hadoop에서 Data Lake Tools for Visual Studio를 사용하여 Apache Hive 쿼리를 실행하는 방법을 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/27/2019
ms.openlocfilehash: 27ab13481525819eb1435f4c9ac256a21acd21fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74687793"
---
# <a name="run-apache-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>Visual Studio용 Data Lake 도구를 사용하여 Apache Hive 쿼리 실행

Data Lake Tools for Visual Studio를 사용하여 Apache Hive를 쿼리하는 방법을 알아봅니다. Data Lake 도구를 사용하면 Azure HDInsight에서 Apache Hadoop에 대해 Hive 쿼리를 쉽게 만들고, 제출하고, 모니터링할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* HDInsight의 Apache Hadoop 클러스터. 이 항목을 만드는 것에 대한 자세한 내용은 [리소스 관리자 템플릿을 사용하여 Azure HDInsight의 아파치 하두롭 클러스터 만들기를](./apache-hadoop-linux-tutorial-get-started.md)참조하십시오.

* [비주얼 스튜디오](https://visualstudio.microsoft.com/vs/). 이 문서의 단계는 Visual Studio 2019를 사용합니다.

* Visual Studio용 HDInsight 도구 또는 Visual Studio용 Azure Data Lake 도구 도구 설치 및 구성에 대한 자세한 내용은 [Visual Studio용 데이터 레이크 도구 설치를](apache-hadoop-visual-studio-tools-get-started.md#install-data-lake-tools-for-visual-studio)참조하십시오.

## <a name="run-apache-hive-queries-using-the-visual-studio"></a> Visual Studio를 사용하여 Apache Hive 쿼리 실행

Hive 쿼리를 만들고 실행하기 위한 두 가지 옵션이 있습니다.

* 임시 쿼리를 만듭니다.
* 하이브 응용 프로그램을 만듭니다.

### <a name="create-an-ad-hoc-hive-query"></a>임시 하이브 쿼리 만들기

임시 쿼리는 **일괄 처리** 또는 **대화형** 모드에서 실행할 수 있습니다.

1. Visual Studio를 실행하고 **코드 없이 계속을** **선택합니다.**

2. **서버 탐색기에서** **Azure를**마우스 오른쪽 단추로 클릭하고 **Microsoft Azure 구독에 연결...,** 로그인 프로세스를 완료합니다.

3. **HDInsight를**확장한 다음 쿼리를 실행할 클러스터를 마우스 오른쪽 단추로 클릭한 다음 **Hive 쿼리 작성을**선택합니다.

4. 다음 하이브 쿼리를 입력합니다.

    ```hql
    SELECT * FROM hivesampletable;
    ```

5. **실행**을 선택합니다. 실행 모드는 기본적으로 **대화형**입니다.

    ![대화형 하이브 쿼리 실행, 비주얼 스튜디오](./media/apache-hadoop-use-hive-visual-studio/vs-execute-hive-query.png)

6. **일괄 처리** 모드에서 동일한 쿼리를 실행하려면 드롭다운 목록을 **대화형에서** **Batch로**전환합니다. 실행 단추가 **실행에서** **제출로**변경됩니다.

    ![일괄 처리 하이브 쿼리 제출, 비주얼 스튜디오](./media/apache-hadoop-use-hive-visual-studio/visual-studio-batch-query.png)

    Hive 편집기는 IntelliSense를 지원합니다. Data Lake Tools for Visual Studio는 Hive 스크립트를 편집할 때 원격 메타데이터 로드를 지원합니다. 예를 들어 `SELECT * FROM`입력하는 경우 IntelliSense는 제안된 모든 테이블 이름을 나열합니다. 테이블 이름이 지정되면 IntelliSense에서 열 이름을 나열합니다. 이 도구는 대부분의 Hive DML 문, 하위 쿼리 및 기본 제공 UDF를 지원합니다. IntelliSense는 HDInsight 도구 모음에서 선택한 클러스터의 메타데이터만 제안합니다.

7. 쿼리 도구 모음(쿼리 탭 아래및 쿼리 텍스트 위의 영역)에서 **제출을**선택하거나 **제출** 옆에 있는 풀다운 화살표를 선택하고 풀다운 목록에서 **고급을** 선택합니다. 후자의 옵션을 선택하면

8. 고급 제출 옵션을 선택한 경우 **스크립트 제출** 대화 상자에서 **작업 이름,** **인수,** **추가 구성**및 **상태 디렉터리를** 구성합니다. 그런 다음 **제출**을 선택합니다.

    ![스크립트 대화 상자 제출, HDInsight 하두프 하이브 쿼리](./media/apache-hadoop-use-hive-visual-studio/vs-tools-submit-jobs-advanced.png)

### <a name="create-a-hive-application"></a>Hive 애플리케이션 만들기

Hive 응용 프로그램을 만들어 Hive 쿼리를 실행하려면 다음 단계를 따르십시오.

1. 오픈 **비주얼 스튜디오**.

2. **시작** 창에서 **새 프로젝트 만들기**를 선택합니다.

3. 새 **프로젝트 만들기** 창에서 템플릿 검색 상자에서 *Hive를* **입력합니다.** 그런 다음 **하이브 응용 프로그램을** 선택하고 **다음을**선택합니다.

4. 새 **프로젝트 구성** 창에서 **프로젝트 이름을**입력하고 새 프로젝트의 **위치를** 선택 또는 작성한 다음 **을**선택합니다.

5. 이 프로젝트에서 만든 **Script.hql** 파일을 열고 아래 HiveQL 문을 붙여 넣습니다.

    ```hql
    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND  INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
    ```

    이러한 명령문은 다음과 같은 작업을 수행합니다.

    * `DROP TABLE`: 테이블이 있는 경우 테이블을 삭제합니다.

    * `CREATE EXTERNAL TABLE`: Hive에서 새 '외부' 테이블을 만듭니다. 외부 테이블만 테이블 정의를 Hive에 저장합니다. (데이터는 원래 위치에 남아 있습니다.)

        > [!NOTE]  
        > MapReduce 작업 또는 Azure 서비스와 같은 외부 원본에서 기본 데이터를 업데이트할 것으로 예상되는 경우 외부 테이블을 사용해야 합니다.
        >
        > 외부 테이블을 삭제하면 데이터는 삭제되지 **않고** 테이블 정의만 삭제됩니다.

    * `ROW FORMAT`: 데이터의 형식 지정 방식을 Hive에 알립니다. 이 경우, 각 로그의 필드는 공백으로 구분됩니다.

    * `STORED AS TEXTFILE LOCATION`: 데이터가 *예제/데이터* 디렉토리에 저장되고 텍스트로 저장됨을 Hive에 알려줍니다.

    * `SELECT`: 열에 `t4` 값이 `[ERROR]`포함된 모든 행의 개수를 선택합니다. 이 문은 세 `3`행에 이 값이 포함되어 있으므로 의 값을 반환합니다.

    * `INPUT__FILE__NAME LIKE '%.log'`: Hive에 .log로 끝나는 파일의 데이터만 반환하도록 지시합니다. 이 절은 데이터를 포함하는 *sample.log* 파일로 검색을 제한합니다.

6. 임시 쿼리 도구 모음과 모양이 비슷한 쿼리 파일 모음에서 이 쿼리에 사용할 HDInsight 클러스터를 선택합니다. 그런 다음 **대화형을 일괄 처리(필요한** 경우)로 변경하고 **Interactive** **제출을** 선택하여 문을 Hive 작업으로 실행합니다.

   **Hive 작업 요약** 이 표시되고 실행 중인 작업 정보가 표시됩니다. **작업 상태**가 **완료**로 변경될 때까지 **새로 고침** 링크를 사용하여 작업 정보를 새로 고칩니다.

   ![하이브 작업 요약 완료, 하이브 응용 프로그램, 비주얼 스튜디오](./media/apache-hadoop-use-hive-visual-studio/hdinsight-job-summary.png)

7. 이 작업의 출력을 보려면 **작업 출력을** 선택합니다. 이 쿼리로 반환된 값으로 `[ERROR] 3`이 표시되어야 합니다.

### <a name="additional-example"></a>추가 예제

다음 예제에서는 이전 `log4jLogs` 절차에서 만든 테이블에 의존 [합니다.](#create-a-hive-application)

1. **서버 탐색기에서**클러스터를 마우스 오른쪽 단추로 클릭하고 **하이브 쿼리 쓰기를**선택합니다.

2. 다음 하이브 쿼리를 입력합니다.

    ```hql
    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    이러한 명령문은 다음과 같은 작업을 수행합니다.

    * `CREATE TABLE IF NOT EXISTS`: 테이블이 아직 없는 경우 테이블을 만듭니다. 키워드가 `EXTERNAL` 사용되지 않으므로 이 문은 내부 테이블을 만듭니다. 내부 테이블은 Hive 데이터 웨어하우스에 저장되며 Hive에 의해 관리됩니다.

        > [!NOTE]  
        > `EXTERNAL` 테이블과 달리 내부 테이블을 삭제하면 기본 데이터도 삭제됩니다.

    * `STORED AS ORC`: 최적화된 행 열(ORC) 형식으로 데이터를 *저장합니다.* ORC는 Hive 데이터를 저장하기 위한 고도로 최적화되고 효율적인 형식입니다.

    * `INSERT OVERWRITE ... SELECT`: `[ERROR]`가 포함된 `log4jLogs` 테이블에서 행을 선택하고 데이터를 `errorLogs` 테이블에 삽입합니다.

3. 필요한 경우 **대화형을** **일괄 처리로** 변경한 다음 **제출을**선택합니다.

4. 작업이 테이블을 만들었으며 서버 **탐색기로** 이동하여 **Azure** > **HDInsight**를 확장합니다. HDInsight 클러스터를 확장한 다음 Hive 데이터베이스**기본값을** **확장합니다.** >  **errorLogs** 및 **log4jLogs** 테이블이 나열됩니다.

## <a name="next-steps"></a>다음 단계

여기에서 볼 수 있듯이 Visual Studio용 HDInsight 도구는 HDInsight에서 Hive 쿼리를 수행하는 쉬운 방법을 제공합니다.

* HDInsight의 하이브에 대한 일반적인 정보는 [Azure HDInsight에서 아파치 하이브및 HiveQL이란 무엇입니까?](hdinsight-use-hive.md)

* HDInsight에서 Hadoop으로 작업할 수 있는 다른 방법에 대한 자세한 내용은 [HDInsight의 아파치 하두프에서 MapReduce 사용을](hdinsight-use-mapreduce.md) 참조하십시오.

* Visual Studio용 HDInsight 도구에 대한 자세한 내용은[Visual Studio용 데이터 레이크 도구 사용을 참조하여 Azure HDInsight에 연결하고 아파치 하이브 쿼리를 실행합니다.](apache-hadoop-visual-studio-tools-get-started.md)
