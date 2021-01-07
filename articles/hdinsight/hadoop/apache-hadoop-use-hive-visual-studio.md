---
title: Visual Studio 용 Apache Hive & Data Lake 도구-Azure HDInsight
description: Azure HDInsight의 Apache Hadoop에서 Data Lake Tools for Visual Studio를 사용하여 Apache Hive 쿼리를 실행하는 방법을 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/27/2019
ms.openlocfilehash: c8645ae9cb901b9fc95f00665d73e223a24fda63
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86076456"
---
# <a name="run-apache-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>Visual Studio용 Data Lake 도구를 사용하여 Apache Hive 쿼리 실행

Data Lake Tools for Visual Studio를 사용하여 Apache Hive를 쿼리하는 방법을 알아봅니다. Data Lake 도구를 사용하면 Azure HDInsight에서 Apache Hadoop에 대해 Hive 쿼리를 쉽게 만들고, 제출하고, 모니터링할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* HDInsight의 Apache Hadoop 클러스터. 이 항목을 만드는 방법에 대 한 자세한 내용은 [리소스 관리자 템플릿을 사용 하 여 Azure HDInsight에서 Apache Hadoop 클러스터 만들기](./apache-hadoop-linux-tutorial-get-started.md)를 참조 하세요.

* [Visual Studio](https://visualstudio.microsoft.com/vs/) 이 문서의 단계는 Visual Studio 2019을 사용 합니다.

* Visual Studio용 HDInsight 도구 또는 Visual Studio용 Azure Data Lake 도구 도구를 설치 하 고 구성 하는 방법에 대 한 자세한 내용은 [Install Data Lake tools For Visual Studio](apache-hadoop-visual-studio-tools-get-started.md#install-data-lake-tools-for-visual-studio)를 참조 하세요.

## <a name="run-apache-hive-queries-using-the-visual-studio"></a> Visual Studio를 사용하여 Apache Hive 쿼리 실행

Hive 쿼리를 만들고 실행하기 위한 두 가지 옵션이 있습니다.

* 임시 쿼리를 만듭니다.
* Hive 응용 프로그램을 만듭니다.

### <a name="create-an-ad-hoc-hive-query"></a>임시 Hive 쿼리 만들기

임시 쿼리를 **일괄 처리** 또는 **대화형** 모드로 실행할 수 있습니다.

1. **Visual Studio** 를 시작 하 고 **코드를 제외**하 고 계속을 선택 합니다.

2. **서버 탐색기**에서 **Azure**를 마우스 오른쪽 단추로 클릭 하 고 **Microsoft Azure 구독에 연결**...을 선택 하 여 로그인 프로세스를 완료 합니다.

3. **HDInsight**를 확장 하 고 쿼리를 실행 하려는 클러스터를 마우스 오른쪽 단추로 클릭 한 다음 **Hive 쿼리 작성**을 선택 합니다.

4. 다음 hive 쿼리를 입력 합니다.

    ```hql
    SELECT * FROM hivesampletable;
    ```

5. **실행**을 선택합니다. 실행 모드는 기본적으로 **Interactive**로 설정 됩니다.

    ![대화형 Hive 쿼리 실행, Visual Studio](./media/apache-hadoop-use-hive-visual-studio/vs-execute-hive-query.png)

6. **일괄 처리** 모드에서 동일한 쿼리를 실행 하려면 드롭다운 목록을 **대화형** 에서 **일괄 처리**로 전환 합니다. 실행 단추가 **Execute** 에서 **Submit**로 변경 됩니다.

    ![일괄 처리 Hive 쿼리, Visual Studio 제출](./media/apache-hadoop-use-hive-visual-studio/visual-studio-batch-query.png)

    Hive 편집기는 IntelliSense를 지원합니다. Data Lake Tools for Visual Studio는 Hive 스크립트를 편집할 때 원격 메타데이터 로드를 지원합니다. 예를 들어를 입력 하면 `SELECT * FROM` IntelliSense에서 제안 된 테이블 이름을 모두 나열 합니다. 테이블 이름이 지정되면 IntelliSense에서 열 이름을 나열합니다. 이 도구는 대부분의 Hive DML 문, 하위 쿼리 및 기본 제공 UDF를 지원합니다. IntelliSense는 HDInsight 도구 모음에서 선택한 클러스터의 메타데이터만 제안합니다.

7. 쿼리 도구 모음 (쿼리 탭 및 쿼리 텍스트 위의 영역)에서 **제출**을 선택 하거나 **전송** 옆의 풀 다운 화살표를 선택 하 고 풀 다운 목록에서 **고급** 을 선택 합니다. 후자 옵션을 선택 하는 경우

8. 고급 제출 옵션을 선택한 경우 **스크립트 제출** 대화 상자에서 **작업 이름**, **인수**, **추가 구성**및 **상태 디렉터리** 를 구성 합니다. 그런 다음 **제출**을 선택합니다.

    ![스크립트 제출 대화 상자, HDInsight Hadoop Hive 쿼리](./media/apache-hadoop-use-hive-visual-studio/vs-tools-submit-jobs-advanced.png)

### <a name="create-a-hive-application"></a>Hive 애플리케이션 만들기

Hive 응용 프로그램을 만들어 Hive 쿼리를 실행 하려면 다음 단계를 수행 합니다.

1. **Visual Studio**를 엽니다.

2. **시작** 창에서 **새 프로젝트 만들기**를 선택합니다.

3. **새 프로젝트 만들기** 창의 **템플릿 검색** 상자에 *Hive*를 입력 합니다. 그런 다음 **Hive 응용 프로그램** 을 선택 하 고 **다음**을 선택 합니다.

4. **새 프로젝트 구성** 창에서 **프로젝트 이름을**입력 하 고 새 프로젝트의 **위치** 를 선택한 다음 **만들기**를 선택 합니다.

5. 이 프로젝트에서 만든 **Script.hql** 파일을 열고 아래 HiveQL 문을 붙여 넣습니다.

    ```hql
    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND  INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
    ```

    이러한 문은 다음 작업을 수행 합니다.

    * `DROP TABLE`: 테이블이 있으면 삭제 합니다.

    * `CREATE EXTERNAL TABLE`: Hive에서 새 '외부' 테이블을 만듭니다. 외부 테이블만 테이블 정의를 Hive에 저장합니다. 데이터는 원래 위치에 남아 있습니다.

        > [!NOTE]  
        > MapReduce 작업 또는 Azure 서비스와 같은 외부 원본에서 기본 데이터를 업데이트 해야 하는 경우 외부 테이블을 사용 해야 합니다.
        >
        > 외부 테이블을 삭제하면 데이터는 삭제되지 **않고** 테이블 정의만 삭제됩니다.

    * `ROW FORMAT`: 데이터의 형식 지정 방식을 Hive에 알립니다. 이 경우, 각 로그의 필드는 공백으로 구분됩니다.

    * `STORED AS TEXTFILE LOCATION`: 데이터를 *example/data* 디렉터리에 저장 하 고 텍스트로 저장 되었음을 Hive에 알립니다.

    * `SELECT`: 열에 값이 포함 된 모든 행의 수를 선택 `t4` `[ERROR]` 합니다. 이 문은 `3` 세 개의 행이이 값을 포함 하기 때문에 값을 반환 합니다.

    * `INPUT__FILE__NAME LIKE '%.log'`:는 .log로 끝나는 파일의 데이터만 반환 하도록 Hive에 지시 합니다. 이 절은 데이터를 포함 하는 *샘플 .log* 파일로 검색을 제한 합니다.

6. 임시 쿼리 도구 모음과 비슷한 모양의 쿼리 파일 도구 모음에서이 쿼리에 사용할 HDInsight 클러스터를 선택 합니다. 그런 다음 **대화형** 을 **일괄 처리** (필요한 경우)로 변경 하 고 **제출** 을 선택 하 여 문을 Hive 작업으로 실행 합니다.

   **Hive 작업 요약** 이 표시되고 실행 중인 작업 정보가 표시됩니다. **작업 상태**가 **완료**로 변경될 때까지 **새로 고침** 링크를 사용하여 작업 정보를 새로 고칩니다.

   ![완료 된 Hive 작업 요약, Hive 응용 프로그램, Visual Studio](./media/apache-hadoop-use-hive-visual-studio/hdinsight-job-summary.png)

7. **작업 출력** 을 선택 하 여이 작업의 출력을 볼 수 있습니다. 이 쿼리로 반환된 값으로 `[ERROR] 3`이 표시되어야 합니다.

### <a name="additional-example"></a>추가 예제

다음 예에서는 `log4jLogs` 이전 절차에서 만든 테이블을 사용 하 여 [Hive 응용 프로그램을 만듭니다](#create-a-hive-application).

1. **서버 탐색기**에서 클러스터를 마우스 오른쪽 단추로 클릭 하 고 **Hive 쿼리 작성**을 선택 합니다.

2. 다음 hive 쿼리를 입력 합니다.

    ```hql
    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    이러한 문은 다음 작업을 수행 합니다.

    * `CREATE TABLE IF NOT EXISTS`: 테이블이 아직 없는 경우 테이블을 만듭니다. 키워드는 `EXTERNAL` 사용 되지 않으므로이 문은 내부 테이블을 만듭니다. 내부 테이블은 Hive 데이터 웨어하우스에 저장되며 Hive에 의해 관리됩니다.

        > [!NOTE]  
        > `EXTERNAL` 테이블과 달리 내부 테이블을 삭제하면 기본 데이터도 삭제됩니다.

    * `STORED AS ORC`: *최적화 된 행 칼럼* 형식 (ORC) 형식으로 데이터를 저장 합니다. ORC는 Hive 데이터를 저장하기 위한 고도로 최적화되고 효율적인 형식입니다.

    * `INSERT OVERWRITE ... SELECT`: `[ERROR]`가 포함된 `log4jLogs` 테이블에서 행을 선택하고 데이터를 `errorLogs` 테이블에 삽입합니다.

3. 필요한 경우 **대화형** 을 **일괄 처리** 로 변경 하 고 **제출**을 선택 합니다.

4. 작업이 테이블을 만들었는지 확인 하려면 **서버 탐색기** 으로 이동 하 여 **Azure**  >  **HDInsight**를 확장 합니다. HDInsight 클러스터를 확장 한 다음 **Hive 데이터베이스**  >  **기본값**을 확장 합니다. **errorLogs** 및 **log4jLogs** 테이블이 나열됩니다.

## <a name="next-steps"></a>다음 단계

여기에서 볼 수 있듯이 Visual Studio용 HDInsight 도구는 HDInsight에서 Hive 쿼리를 수행하는 쉬운 방법을 제공합니다.

* HDInsight의 Hive에 대 한 일반적인 정보는 [Azure hdinsight의 Apache Hive 및 HiveQL?](hdinsight-use-hive.md) 을 참조 하세요.

* HDInsight에서 Hadoop으로 작업 하는 다른 방법에 대 한 자세한 내용은 [hdinsight의 Apache Hadoop에서 MapReduce 사용](hdinsight-use-mapreduce.md) 을 참조 하세요.

* Visual Studio 용 HDInsight 도구에 대 한 자세한 내용은[Visual studio 용 Data Lake 도구를 사용 하 여 Azure HDInsight에 연결 및 Apache Hive 쿼리 실행을](apache-hadoop-visual-studio-tools-get-started.md) 참조 하세요.
