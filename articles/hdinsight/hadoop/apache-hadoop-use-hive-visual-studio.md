---
title: Visual Studio용 Data Lake(Apache Hadoop) 도구로 Apache Hive 사용 - Azure HDInsight
description: Azure HDInsight의 Apache Hadoop에서 Data Lake Tools for Visual Studio를 사용하여 Apache Hive 쿼리를 실행하는 방법을 알아봅니다.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: hrasheed
ms.openlocfilehash: 7480dafe435e555bfba81ebd9242bb5724c0bf3f
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65861591"
---
# <a name="run-apache-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>Visual Studio용 Data Lake 도구를 사용하여 Apache Hive 쿼리 실행

Data Lake Tools for Visual Studio를 사용하여 Apache Hive를 쿼리하는 방법을 알아봅니다. Data Lake 도구를 사용하면 Azure HDInsight에서 Apache Hadoop에 대해 Hive 쿼리를 쉽게 만들고, 제출하고, 모니터링할 수 있습니다.

## <a id="prereq"></a>필수 조건

* HDInsight에서 Apache Hadoop 클러스터를 합니다. 참조 [Linux에서 HDInsight 시작](./apache-hadoop-linux-tutorial-get-started.md)합니다.

* Visual Studio(다음 버전 중 하나)

    * Visual Studio 2015, 2017 (모든 버전)
    * Visual Studio 2013 Community/Professional/Premium/Ultimate 업데이트 4

* Visual Studio용 HDInsight 도구 또는 Visual Studio용 Azure Data Lake 도구 도구 설치 및 구성에 대한 내용은 [HDInsight용 Visual Studio Hadoop 도구 사용 시작](apache-hadoop-visual-studio-tools-get-started.md) 을 참조하세요.

## <a id="run"></a> Visual Studio를 사용하여 Apache Hive 쿼리 실행

Hive 쿼리를 만들고 실행하기 위한 두 가지 옵션이 있습니다.

* 임시 쿼리 만들기
* Hive 애플리케이션 만들기

### <a name="ad-hoc"></a>임시

임시 쿼리를 실행할 수 있습니다 **일괄 처리** 하거나 **대화형** 모드입니다.

1. 오픈 **Visual Studio**합니다.

2. **서버 탐색기**, 이동할 **Azure** > **HDInsight**합니다.

3. 확장 **HDInsight**, 쿼리를 실행 하 고 클릭 하려는 클러스터를 마우스 오른쪽 단추로 클릭 하 고 **Hive 쿼리 작성**합니다.

4. 다음 hive 쿼리를 입력 합니다.

    ```hql
    SELECT * FROM hivesampletable;
    ```

5. **실행**을 선택합니다. 실행 모드는 기본값으로 보면 **대화형**합니다.

    ![대화형 Hive 쿼리 실행 스크린샷](./media/apache-hadoop-use-hive-visual-studio/vs-execute-hive-query.png)

6. 동일한 쿼리를 실행 하려면 **일괄 처리** 모드, 토글 드롭다운 목록에서 **대화형** 하 **일괄 처리**합니다. 실행 단추에서 변경 되 면 **Execute** 하 **제출**합니다.

    ![Hive 쿼리 제출 스크린샷](./media/apache-hadoop-use-hive-visual-studio/vs-batch-query.png)

    Hive 편집기는 IntelliSense를 지원합니다. Data Lake Tools for Visual Studio는 Hive 스크립트를 편집할 때 원격 메타데이터 로드를 지원합니다. 예를 들어 입력 `SELECT * FROM`, IntelliSense 제안 된 테이블 이름을 모두 나열 합니다. 테이블 이름이 지정되면 IntelliSense에서 열 이름을 나열합니다. 이 도구는 대부분의 Hive DML 문, 하위 쿼리 및 기본 제공 UDF를 지원합니다. IntelliSense는 HDInsight 도구 모음에서 선택한 클러스터의 메타데이터만 제안합니다.

    ![HDInsight Visual Studio Tools- IntelliSense 예 1의 스크린샷](./media/apache-hadoop-use-hive-visual-studio/vs-intellisense-table-name.png "U-SQL IntelliSense")
   
    ![HDInsight Visual Studio Tools - IntelliSense 예 2의 스크린샷](./media/apache-hadoop-use-hive-visual-studio/vs-intellisense-column-name.png "U-SQL IntelliSense")

7. **제출** 또는 **제출(고급)** 을 선택합니다.

   고급 제출 옵션을 선택하는 경우 스크립트에 대한 **작업 이름**, **인수**, **추가 구성** 및 **상태 디렉터리**를 구성합니다.

    ![HDInsight Hadoop Hive 쿼리의 스크린샷](./media/apache-hadoop-use-hive-visual-studio/hdinsight.visual.studio.tools.submit.jobs.advanced.png "쿼리 제출")

### <a name="hive-application"></a>Hive 응용 프로그램

1. 오픈 **Visual Studio**합니다.

2. 메뉴 모음에서로 이동 **파일** > **새로 만들기** > **프로젝트**합니다.

3. **새 프로젝트** 창에서 이동할 **템플릿** > **Azure Data Lake** > **HIVE (HDInsight)**  >  **응용 프로그램을 hive**합니다. 

4. 이 프로젝트에 대 한 이름을 입력 하 고 선택한 **확인**합니다.

5. 이 프로젝트에서 만든 **Script.hql** 파일을 열고 아래 HiveQL 문을 붙여 넣습니다.

    ```hiveql
    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND  INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
    ```

    이러한 문은 다음 작업을 수행합니다.

   * `DROP TABLE`: 테이블이 있는 경우 이 문에서 삭제합니다.

   * `CREATE EXTERNAL TABLE`: Hive에서 새 ‘외부’ 테이블을 만듭니다. 외부 테이블은 Hive에 테이블 정의만 저장하고, 데이터는 원래 위치에 남아 있습니다.

     > [!NOTE]  
     > 외부 원본에서 기본 데이터를 업데이트하길 원하는 경우에는 외부 테이블을 사용해야 합니다. MapReduce 작업 또는 Azure 서비스를 예로 들 수 있습니다.
     >
     > 외부 테이블을 삭제하면 데이터는 삭제되지 **않고** 테이블 정의만 삭제됩니다.

   * `ROW FORMAT`: 데이터의 형식 지정 방식을 Hive에 알립니다. 이 경우, 각 로그의 필드는 공백으로 구분됩니다.

   * `STORED AS TEXTFILE LOCATION`: 데이터가 example/data 디렉터리에 텍스트로 저장되었음을 Hive에 알립니다.

   * `SELECT`: `t4` 열에 `[ERROR]` 값이 포함된 모든 행의 수를 선택합니다. 이 문에서는 이 값을 포함하는 행이 3개 있으므로 `3` 값이 반환되어야 합니다.

   * `INPUT__FILE__NAME LIKE '%.log'` - .log로 끝나는 파일의 데이터만 반환하도록 Hive에 지시합니다. 이 절은 데이터가 포함된 sample.log 파일로 검색을 제한합니다.

6. 도구 모음에서 쿼리에 사용할 **HDInsight 클러스터**를 선택합니다. **제출**을 선택하여 Hive 작업으로 문을 실행합니다.

   ![제출 표시줄](./media/apache-hadoop-use-hive-visual-studio/toolbar.png)

7. **Hive 작업 요약** 이 표시되고 실행 중인 작업 정보가 표시됩니다. **작업 상태**가 **완료**로 변경될 때까지 **새로 고침** 링크를 사용하여 작업 정보를 새로 고칩니다.

   ![완료된 작업을 표시하는 작업 요약](./media/apache-hadoop-use-hive-visual-studio/jobsummary.png)

8. 이 작업의 출력을 보려면 **작업 출력** 링크를 사용합니다. 이 쿼리로 반환된 값으로 `[ERROR] 3`이 표시되어야 합니다.

### <a name="additional-example"></a>추가 예제

이 예에서는 `log4jLogs` 이전 단계에서 만든 테이블입니다.

1. **서버 탐색기**, 클러스터를 마우스 오른쪽 단추로 **Hive 쿼리 작성**합니다.

2. 다음 hive 쿼리를 입력 합니다.

    ```hql
    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    이러한 문은 다음 작업을 수행합니다.

    * `CREATE TABLE IF NOT EXISTS`: 테이블이 아직 없는 경우 테이블을 만듭니다. `EXTERNAL` 키워드가 사용되지 않으므로 이 문으로 내부 테이블이 생성됩니다. 내부 테이블은 Hive 데이터 웨어하우스에 저장되며 Hive에 의해 관리됩니다.
    
    > [!NOTE]  
    > `EXTERNAL` 테이블과 달리 내부 테이블을 삭제하면 기본 데이터도 삭제됩니다.

    * `STORED AS ORC`: 데이터를 ORC(Optimized Row Columnar) 형식으로 저장합니다. ORC는 Hive 데이터를 저장하기 위한 고도로 최적화되고 효율적인 형식입니다.
    
    * `INSERT OVERWRITE ... SELECT`: `[ERROR]`가 포함된 `log4jLogs` 테이블에서 행을 선택하고 데이터를 `errorLogs` 테이블에 삽입합니다.

3. 쿼리를 실행할 **일괄 처리** 모드입니다.

4. 작업에서 테이블이 만들어졌는지 확인하려면 **서버 탐색기**를 사용하여 **Azure** > **HDInsight** > HDInsight 클러스터 > **Hive 데이터베이스** > **기본값**을 확장합니다. **errorLogs** 및 **log4jLogs** 테이블이 나열됩니다.

## <a id="nextsteps"></a>다음 단계

여기에서 볼 수 있듯이 Visual Studio용 HDInsight 도구는 HDInsight에서 Hive 쿼리를 수행하는 쉬운 방법을 제공합니다.

HDInsight의 Hive에 대한 일반적인 정보:

* [HDInsight에서 Apache Hadoop과 함께 Apache Hive 사용](hdinsight-use-hive.md)

HDInsight에서 Hadoop으로 작업하는 다른 방법에 관한 정보:

* [HDInsight에서 Apache Hadoop과 함께 Apache Pig 사용](hdinsight-use-pig.md)

* [HDInsight에서 Apache Hadoop과 MapReduce 사용](hdinsight-use-mapreduce.md)

Visual Studio용 HDInsight 도구에 대한 자세한 내용은 다음을 참조하세요.

* [Visual Studio용 HDInsight 도구 시작](apache-hadoop-visual-studio-tools-get-started.md)