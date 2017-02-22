---
title: "HDInsight(Hadoop)에서 Ambari 보기를 사용하는 Hive 작업| Microsoft 문서"
description: "웹 브라우저에서 Hive 뷰를 사용하여 Hive 쿼리를 제출하는 방법을 알아봅니다. Hive 뷰는 Linux 기반 HDInsight 클러스터와 함께 제공되는 Ambari 웹 UI의 일부입니다."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 1abe9104-f4b2-41b9-9161-abbc43de8294
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/08/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 5ec4b964066687b506686709c3dc5ed5b402fbaf
ms.openlocfilehash: a846d5a70451ed3082b90d87b90bef0eb6da5993


---
# <a name="use-the-hive-view-with-hadoop-in-hdinsight"></a>HDInsight에서 Hadoop과 Hive 사용

[!INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Ambari는 Linux 기반 HDInsight 클러스터와 함께 제공되는 관리 및 모니터링 유틸리티입니다. Ambari를 통해 제공되는 기능 중 하나는 Hive 쿼리를 실행하는 데 사용할 수 있는 웹 UI입니다. 이는 **Hive 보기**이며, HDInsight 클러스터에서 제공하는 Ambari 보기의 일부입니다.

> [!NOTE]
> Ambari에는 이 문서에 설명되지 않은 여러 기능이 있습니다. 자세한 내용은 [Ambari 웹 UI를 사용하여 HDInsight 클러스터 관리](hdinsight-hadoop-manage-ambari.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

* Linux 기반 HDInsight 클러스터입니다. 클러스터를 만드는 방법에 대한 정보는 [Linux 기반 HDInsight 시작](hdinsight-hadoop-linux-tutorial-get-started.md)을 참조하세요.

> [!IMPORTANT]
> 이 문서의 단계에는 Linux를 사용하는 HDInsight 클러스터가 필요합니다. Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [Windows에서 HDInsight 사용 중단](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)을 참조하세요.

## <a name="open-the-hive-view"></a>Hive 뷰를 엽니다.

Azure Portal에서 HDInsight 클러스터를 선택한 다음 **빠른 링크** 섹션에서 **Ambari 보기**를 선택하면 Ambari 보기를 볼 수 있습니다.

![빠른 링크 섹션](./media/hdinsight-hadoop-use-hive-ambari-view/quicklinks.png)

웹 브라우저에 https://CLUSTERNAME.azurehdinsight.net을 입력하여 Ambari로 직접 이동할 수도 있습니다. **CLUSTERNAME** 을 HDInsight 클러스터의 이름으로 바꿉니다. **관리자** 링크 옆에 있는 페이지 메뉴에서 사각형 집합을 선택하여 사용 가능한 보기의 목록을 표시합니다. **Hive 보기**를 선택합니다.

![Ambari 뷰 선택](./media/hdinsight-hadoop-use-hive-ambari-view/selecthiveview.png).

> [!NOTE]
> Ambari에 액세스할 때 사이트에 인증하라는 메시지가 표시됩니다. 클러스터를 만들 때 사용한 관리자(기본값 `admin`), 계정 이름 및 암호를 입력합니다.

다음과 유사한 결과가 표시됩니다.

![쿼리 편집기 섹션이 포함된 Hive 뷰 페이지 이미지](./media/hdinsight-hadoop-use-hive-ambari-view/hiveview.png)

## <a name="view-tables"></a>테이블 보기
페이지의 **데이터베이스 탐색기**섹션에서 **데이터베이스** 탭의 **기본** 항목을 선택합니다. 기본 데이터베이스에 테이블의 목록이 나타납니다. 새 HDInsight 클러스터의 경우 단 하나의 테이블, 즉 **hivesampletable**이 있어야 합니다.

![확장된 기본 데이터베이스를 사용한 데이터베이스 탐색기](./media/hdinsight-hadoop-use-hive-ambari-view/databaseexplorer.png)

새 테이블이 이 문서의 단계를 통해 추가되면 데이터베이스 탐색기의 오른쪽 위 모서리에서 새로 고침 아이콘을 사용하여 목록을 새로 고칠 수 있습니다.

## <a name="a-namehivequeryaquery-editor"></a><a name="hivequery"></a>쿼리 편집기

Hive 보기에서 다음 단계를 사용하여 Hive 쿼리를 실행합니다.

1. 페이지의 **쿼리 편집기** 섹션에서 다음 HiveQL 문을 워크시트에 붙여넣습니다.
   
    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;
    ```
   
    이러한 문은 다음 작업을 수행합니다.
   
   * **DROP TABLE** - 테이블이 이미 있는 경우 테이블과 데이터 파일을 삭제합니다.

   * **CREATE EXTERNAL TABLE** - Hive에서 새 “외부” 테이블을 만듭니다. 
   외부 테이블은 테이블 정의만 Hive에 저장합니다. 데이터는 원래 위치에 그대로 유지됩니다.

   * **ROW FORMAT** - 데이터의 형식 지정 방식을 Hive에 알립니다. 이 경우, 각 로그의 필드는 공백으로 구분됩니다.

   * **STORED AS TEXTFILE LOCATION** - 데이터가 저장된 위치(example/data 디렉터리) 및 텍스트로 저장되었음을 Hive에 알립니다.

   * **SELECT** - t4 열에 ERROR 값이 포함된 모든 행의 수를 선택합니다.
     
     > [!NOTE]
     > 외부 원본에서 기본 데이터를 업데이트하길 원하는 경우에는 외부 테이블을 사용해야 합니다. 예: 자동화된 데이터 업로드 프로세스나 또 다른 MapReduce 작업 사용 외부 테이블을 삭제하면 데이터는 삭제되지 *않고* 테이블 정의만 삭제됩니다.

2. 쿼리를 시작하려면 [쿼리 편집기] 아래쪽의 **실행** 단추를 사용합니다. 주황색으로 바뀌고 텍스트가 **실행 중지**로 변경됩니다. 쿼리 편집기 아래에 **쿼리 프로세스 결과** 섹션이 나타나고 작업에 대한 정보가 표시될 것입니다.
   
   > [!IMPORTANT]
   > 일부 브라우저에서는 로그 또는 결과 정보가 올바르게 새로 고침되지 않을 수 있습니다. 작업을 실행했는데 로그가 업데이트되거나 결과가 반환되지 않고 작업이 계속 실행되는 것 같으면 Mozilla FireFox 또는 Google Chrome를 사용해 보세요.
 
3. 쿼리가 완료되면 **쿼리 프로세스 결과** 섹션에 작업 결과가 표시됩니다. 쿼리가 완료되면 **실행 중지** 단추도 녹색 **실행** 단추로 다시 바뀝니다. **결과** 탭에는 다음 정보가 표시됩니다.
   
        sev       cnt
        [ERROR]   3
   
    **로그** 탭을 사용하면 작업에서 생성된 로깅 정보를 볼 수 있습니다.
   
   > [!TIP]
   > **프로세스 결과 쿼리** 섹션 맨 위 왼쪽에서 **결과 저장** 드롭다운 대화 상자를 사용하여 결과를 다운로드하거나 저장할 수 있습니다.

4. 이 쿼리의 처음 네 줄을 선택한 다음 **실행**을 선택합니다. 작업이 완료되어도 결과가 없습니다. 쿼리의 일부를 선택했을 때 **실행** 단추를 사용하면 선택한 문만 실행됩니다. 이 예에서는 테이블에서 행을 검색하는 최종 문이 선택한 네 줄에 포함되지 않았습니다. 해당 줄만 선택하고 **실행**단추를 사용하면 예상된 결과가 표시됩니다.

5.새 워크시트를 추가하려면 **쿼리 편집기** 아래쪽의 **새 워크시트** 단추를 사용합니다. 새 워크시트에 다음 HiveQL 문을 입력합니다.
   
    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';
    ```
   
    These statements perform the following actions:
   
   * **CREATE TABLE IF NOT EXISTS** - 테이블이 아직 없는 경우 테이블을 만듭니다. **EXTERNAL** 키워드가 사용되지 않으므로 내부 테이블이 생성됩니다. 내부 테이블은 Hive 데이터 웨어하우스에 저장되며 Hive에서 전적으로 관리됩니다. 외부 테이블과 달리 내부 테이블을 삭제하면 기본 데이터도 삭제됩니다.

   * **STORED AS ORC** - 데이터를 ORC(Optimized Row Columnar) 형식으로 저장합니다. Hive 데이터를 저장하기 위한 고도로 최적화되고 효율적인 형식입니다.

   * **덮어쓰기 삽입... SELECT** - [ERROR]가 포함된 **log4jLogs** 테이블에서 행을 선택한 다음 **errorLogs** 테이블에 데이터를 삽입합니다.
     
     **실행** 단추를 사용하여 해당 쿼리를 실행합니다. 쿼리가&0;개의 행을 반환할 때 **결과** 탭에는 어떠한 정보도 포함되지 않습니다. 쿼리가 완료되면 상태는 **SUCCEEDED**로 표시됩니다.

### <a name="hive-settings"></a>Hive 설정

편집기 오른쪽의 **설정** 아이콘을 선택합니다.

![Hive 보기의 설정 아이콘](./media/hdinsight-hadoop-use-hive-ambari-view/hive-view-settings-icon.png)

설정은 Tez(기본값)에서 MapReduce로 Hive에 대한 실행 엔진을 변경하는 등 다양한 Hive 설정을 변경하는 데 사용할 수 있습니다.

### <a name="visualization"></a>시각화

편집기 오른쪽의 __시각화__ 아이콘을 선택합니다.

![Hive 보기에 대한 시각화 아이콘](./media/hdinsight-hadoop-use-hive-ambari-view/hive-view-visualization-icon.png)

쿼리에서 반환된 데이터의 시각화를 만들 수 있는 시각화 인터페이스가 열립니다. 다음은 HDInsight에 포함된 `hivesampletable`에서 데이터를 사용하는 시각화 예제입니다.

![예제 시각화](./media/hdinsight-hadoop-use-hive-ambari-view/hive-view-visualization.png)

### <a name="visual-explain"></a>시각적 개체 설명

편집기 오른쪽의 **시각적 개체 설명** 아이콘을 선택합니다.

![Hive 보기에 대한 시각적 설명 아이콘](./media/hdinsight-hadoop-use-hive-ambari-view/hive-view-visual-explain-icon.png)

이 아이콘은 쿼리의 **시각적 설명** 보기이며, 복잡한 쿼리 흐름을 이해하는 데 도움이 됩니다. [쿼리 편집기]의 **설명** 단추를 사용하면 이 보기에 해당하는 텍스트 명령을 볼 수 있습니다.

![시각적 설명 이미지](./media/hdinsight-hadoop-use-hive-ambari-view/visualexplain.png)

### <a name="tez"></a>Tez

편집기 오른쪽의 **Tez** 아이콘을 선택합니다.

![Hive 보기에 대한 Tez 아이콘](./media/hdinsight-hadoop-use-hive-ambari-view/hive-view-tez-icon.png)

사용 가능한 경우 이 쿼리에 대해 Tez에서 사용된 방향성 비순환 그래프(DAG)가 표시됩니다. 이전에 실행한 쿼리에 대한 DAG를 보거나 Tez 프로세스를 디버그하려면 [Tez 뷰](hdinsight-debug-ambari-tez-view.md) 를 대신 사용합니다.

### <a name="notifications"></a>알림

편집기 오른쪽에의 **알림** 아이콘을 선택합니다.

![Hive 보기에 대한 알림 아이콘](./media/hdinsight-hadoop-use-hive-ambari-view/hive-view-notifications-icon.png)

알림은 쿼리를 실행할 때 생성되는 메시지입니다. 예를 들어 쿼리를 제출하거나 오류가 발생할 때 알림을 받게 됩니다.

## <a name="saved-queries"></a>저장된 쿼리

1. 쿼리 편집기에서 워크시트를 만들고 다음 쿼리를 입력합니다.
   
    ```hiveql
    SELECT * from errorLogs;
    ```
   
    작동하는지 확인하려면 쿼리를 실행합니다. 결과는 다음 예제와 유사합니다.
   
        errorlogs.t1     errorlogs.t2     errorlogs.t3     errorlogs.t4     errorlogs.t5     errorlogs.t6     errorlogs.t7
        2012-02-03     18:35:34     SampleClass0     [ERROR]     incorrect     id     
        2012-02-03     18:55:54     SampleClass1     [ERROR]     incorrect     id     
        2012-02-03     19:25:27     SampleClass4     [ERROR]     incorrect     id

2. 편집기 아래쪽의 **다른 이름으로 저장** 단추를 사용합니다. 이 쿼리의 이름으로 **Errorlogs**를 입력하고 **확인**을 선택합니다. 워크시트 이름이 **Errorlogs**로 바뀝니다.

3. Hive 보기 페이지의 위쪽에서 **저장된 쿼리** 탭을 선택합니다. 이제 **Errorlogs**가 저장된 쿼리로 나열됩니다. 이 목록에서 제거될 때까지 유지됩니다. 이름을 선택하면 쿼리 편집기에서 해당 쿼리가 열립니다.

## <a name="query-history"></a>쿼리 기록

Hive 보기 위쪽의 **기록** 단추를 사용하면 이전에 실행한 쿼리를 볼 수 있습니다. 이제 사용하여 이전에 실행한 쿼리 중 일부를 선택합니다. 쿼리를 선택하면 쿼리 편집기에서 열립니다.

## <a name="user-defined-functions-udf"></a>UDF(사용자 정의 함수)

Hive는 **사용자 정의 함수(UDF)**를 통해 확장 될 수도 있습니다. UDF를 사용하면 HiveQL에서 쉽게 모델링할 수 있는 기능 또는 논리를 구현할 수 있습니다.

Hive 보기 위쪽의 UDF 탭에서는 **쿼리 편집기**를 통해 사용할 수 있는 UDF 집합을 선언하고 사용할 수 있습니다.

UDF를 Hive 보기에 추가하면 **쿼리 편집기** 아래쪽에 **udf 삽입** 단추가 표시됩니다. 이 단추를 선택하면 Hive 뷰에서 정의된 UDF의 드롭다운 목록을 표시합니다. UDF를 선택하면 쿼리에 HiveQL 문을 추가하여 UDF를 사용하도록 설정합니다.

예를 들어 다음 속성으로 UDF를 정의하는 경우:

* 리소스 이름: myudfs

* 리소스 경로: /myudfs.jar

* UDF 이름: myawesomeudf

* UDF 클래스 이름: com.myudfs.Awesome

**udf 삽입** 단추를 사용하면 해당 리소스에 정의된 각 UDF에 대한 또 다른 드롭다운 목록과 함께 **myudfs**라는 항목이 표시됩니다. 이 경우 **myawesomeudf**입니다. 이 항목을 선택하면 쿼리의 시작 부분에 다음을 추가합니다.

```hiveql
add jar /myudfs.jar;
create temporary function myawesomeudf as 'com.myudfs.Awesome';
```

그런 다음 쿼리에서 UDF를 사용할 수 있습니다. 예: `SELECT myawesomeudf(name) FROM people;`.

HDInsight에서 Hive를 통해 UDF를 사용하는 방법에 대한 자세한 내용은 다음을 참조하세요.

* [HDInsight에서 Hive 및 Pig와 함께 Python 사용](hdinsight-python.md)
* [HDInsight에 사용자 지정 하이브 UDF를 추가 하는 방법](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

## <a name="a-idnextstepsanext-steps"></a><a id="nextsteps"></a>다음 단계
HDInsight의 Hive에 대한 일반적인 정보:

* [HDInsight에서 Hadoop과 Hive 사용](hdinsight-use-hive.md)

HDInsight에서 Hadoop으로 작업하는 다른 방법에 관한 정보:

* [HDInsight에서 Hadoop과 Pig 사용](hdinsight-use-pig.md)
* [HDInsight에서 Hadoop과 MapReduce 사용](hdinsight-use-mapreduce.md)




<!--HONumber=Feb17_HO2-->


