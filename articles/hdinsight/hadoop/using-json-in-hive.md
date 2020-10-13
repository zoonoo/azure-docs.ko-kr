---
title: Apache Hive를 사용 하 여 & 프로세스 JSON 분석-Azure HDInsight
description: Azure HDInsight의 Apache Hive를 사용 하 여 JSON 문서를 사용 하 고 분석 하는 방법을 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: 31fc6fe02559c356f072761c024308f158ae4d9c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86085449"
---
# <a name="process-and-analyze-json-documents-by-using-apache-hive-in-azure-hdinsight"></a>Azure HDInsight에서 Apache Hive를 사용하여 JSON 문서 처리 및 분석

Azure HDInsight에서 Apache Hive를 사용하여 JSON(JavaScript Object Notation) 파일을 처리하고 분석하는 방법에 대해 알아봅니다. 이 문서에서는 다음 JSON 문서를 사용 합니다.

```json
{
  "StudentId": "trgfg-5454-fdfdg-4346",
  "Grade": 7,
  "StudentDetails": [
    {
      "FirstName": "Peggy",
      "LastName": "Williams",
      "YearJoined": 2012
    }
  ],
  "StudentClassCollection": [
    {
      "ClassId": "89084343",
      "ClassParticipation": "Satisfied",
      "ClassParticipationRank": "High",
      "Score": 93,
      "PerformedActivity": false
    },
    {
      "ClassId": "78547522",
      "ClassParticipation": "NotSatisfied",
      "ClassParticipationRank": "None",
      "Score": 74,
      "PerformedActivity": false
    },
    {
      "ClassId": "78675563",
      "ClassParticipation": "Satisfied",
      "ClassParticipationRank": "Low",
      "Score": 83,
      "PerformedActivity": true
    }
  ]
}
```

이 파일은 `wasb://processjson@hditutorialdata.blob.core.windows.net/`에 있습니다. HDInsight에서 Azure Blob Storage를 사용하는 방법에 대한 자세한 내용은 [HDInsight에서 Apache Hadoop을 사용하여 HDFS 호환 Azure Blob Storage 사용](../hdinsight-hadoop-use-blob-storage.md)을 참조하세요. 클러스터의 기본 컨테이너에 파일을 복사할 수 있습니다.

이 문서에서는 Apache Hive 콘솔을 사용 합니다. Hive 콘솔을 여는 방법에 대 한 지침은 [HDInsight에서 Apache Hadoop Apache Ambari Hive 보기 사용](apache-hadoop-use-hive-ambari-view.md)을 참조 하세요.

> [!NOTE]  
> Hive 보기는 HDInsight 4.0에서 더 이상 사용할 수 없습니다.

## <a name="flatten-json-documents"></a>JSON 문서 평면화

다음 섹션에 나열 된 메서드를 통해 JSON 문서를 단일 행으로 구성 해야 합니다. 따라서 JSON 문서를 문자열로 평면화해야 합니다. JSON 문서가 이미 평면화되어 있으면 이 단계를 건너뛰고 다음 섹션인 JSON 데이터 분석으로 바로 이동할 수 있습니다. JSON 문서를 평면화하려면 다음 스크립트를 실행합니다.

```sql
DROP TABLE IF EXISTS StudentsRaw;
CREATE EXTERNAL TABLE StudentsRaw (textcol string) STORED AS TEXTFILE LOCATION "wasb://processjson@hditutorialdata.blob.core.windows.net/";

DROP TABLE IF EXISTS StudentsOneLine;
CREATE EXTERNAL TABLE StudentsOneLine
(
  json_body string
)
STORED AS TEXTFILE LOCATION '/json/students';

INSERT OVERWRITE TABLE StudentsOneLine
SELECT CONCAT_WS(' ',COLLECT_LIST(textcol)) AS singlelineJSON
      FROM (SELECT INPUT__FILE__NAME,BLOCK__OFFSET__INSIDE__FILE, textcol FROM StudentsRaw DISTRIBUTE BY INPUT__FILE__NAME SORT BY BLOCK__OFFSET__INSIDE__FILE) x
      GROUP BY INPUT__FILE__NAME;

SELECT * FROM StudentsOneLine
```

원시 JSON 파일은 `wasb://processjson@hditutorialdata.blob.core.windows.net/`에 있습니다. **StudentsRaw** Hive 테이블은 평면화 되지 않은 원시 JSON 문서를 가리킵니다.

**StudentsOneLine** Hive 테이블은 HDInsight 기본 파일 시스템에서 데이터를 **/json/students/** 경로에 저장합니다.

**INSERT** 문은 **StudentOneLine** 테이블을 평면화된 JSON 데이터로 채웁니다.

**SELECT** 문은 한 행만 반환합니다.

**SELECT** 문의 출력은 다음과 같습니다.

![JSON 문서를 평면화 하는 HDInsight](./media/using-json-in-hive/hdinsight-flatten-json.png)

## <a name="analyze-json-documents-in-hive"></a>Hive에서 JSON 문서 분석

Hive는 JSON 문서에서 쿼리를 실행할 수 있는 서로 다른 세 가지 메커니즘을 제공하거나 직접 작성할 수 있습니다.

* get_json_object UDF(사용자 정의 함수) 사용
* json_tuple UDF 사용
* 사용자 지정 Serializer/Deserializer(SerDe) 사용
* Python 또는 다른 언어를 사용하여 사용자 고유의 UDF 작성 Hive를 사용하여 사용자 고유의 Python 코드를 실행하는 방법에 대한 자세한 내용은 [Apache Hive와 Apache Pig에서 Python UDF 사용](./python-udf-hdinsight.md)을 참조하세요.

### <a name="use-the-get_json_object-udf"></a>get_json_object UDF 사용

Hive는 런타임 중에 JSON을 쿼리 하는 [get_json_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) 라는 기본 제공 UDF를 제공 합니다. 이 메서드는 두 개의 인수, 즉 테이블 이름과 메서드 이름을 사용 합니다. 메서드 이름에는 평면화 된 JSON 문서와 구문 분석 해야 하는 JSON 필드가 있습니다. 이 UDF의 작동 방식을 확인 하는 예를 살펴보겠습니다.

다음 쿼리는 각 학생의 이름과 성을 반환합니다.

```sql
SELECT
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
FROM StudentsOneLine;
```

콘솔 창에서 이 쿼리를 실행하는 경우 출력은 다음과 같습니다.

![Json 개체 UDF를 가져오는 Apache Hive](./media/using-json-in-hive/hdinsight-get-json-object.png)

get-json_object UDF에는 다음과 같은 제한 사항이 있습니다.

* 쿼리의 각 필드는 쿼리를 다시 구문 분석하는 데 필요하므로 성능에 영향을 줍니다.
* **GET\_JSON_OBJECT()** 는 배열의 문자열 표현을 반환합니다. 이 배열을 Hive 배열로 변환하려면 정규식을 사용하여 대괄호("[" 및 "]")를 바꾼 다음 분할된 호출을 통해 배열을 가져와야 합니다.

이 변환은 Hive wiki에서 **json_tuple**사용을 권장 하는 이유입니다.  

### <a name="use-the-json_tuple-udf"></a>json_tupl UDF 사용

Hive에서 제공 하는 다른 UDF를 [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple)라고 하며,이는 [json _object get_](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object)보다 좋습니다. 이 메서드는 키 집합 및 JSON 문자열을 사용 합니다. 그런 다음 값 튜플을 반환 합니다. 다음 쿼리는 JSON 문서에서 학생 ID와 등급을 반환합니다.

```sql
SELECT q1.StudentId, q1.Grade
FROM StudentsOneLine jt
LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
  AS StudentId, Grade;
```

Hive 콘솔에 표시되는 이 스크립트의 출력은 다음과 같습니다.

![Apache Hive json 쿼리 결과](./media/using-json-in-hive/hdinsight-json-tuple.png)

`json_tuple`UDF는 Hive에서 [측면 보기](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) 구문을 사용 합니다 .이를 통해 json \_ 튜플은 원래 테이블의 각 행에 UDT 함수를 적용 하 여 가상 테이블을 만들 수 있습니다. **LATERAL VIEW**를 반복적으로 사용하기 때문에 복잡한 JSON을 다루기가 너무 어려워집니다. 또한 **JSON_TUPLE** 중첩 된 json를 처리할 수 없습니다.

### <a name="use-a-custom-serde"></a>사용자 지정 SerDe 사용

SerDe는 중첩된 JSON 문서를 구문 분석하기 위한 최상의 선택입니다. 이를 통해 JSON 스키마를 정의한 다음 이 스키마를 사용하여 문서를 구문 분석할 수 있습니다. 자세한 내용은 [Microsoft Azure HDInsight에서 사용자 지정 JSON SerDe를 사용하는 방법](https://web.archive.org/web/20190217104719/https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/)을 참조하세요.

## <a name="summary"></a>요약

선택한 Hive의 JSON 연산자 유형은 사용자의 시나리오에 따라 달라 집니다. 간단한 JSON 문서와 조회할 필드 하나를 사용 하 여 Hive UDF **get_json_object**를 선택 합니다. 조회할 키가 두 개 이상인 경우 **json_tuple**를 사용할 수 있습니다. 중첩 된 문서의 경우 **JSON SerDe**를 사용 합니다.

## <a name="next-steps"></a>다음 단계

관련 문서는 다음을 참조하세요.

* [샘플 Apache log4j 파일 분석을 위해 HDInsight에서 Apache Hadoop과 함께 Apache Hive 및 HiveQL 사용](../hdinsight-use-hive.md)
* [HDInsight에서 대화형 쿼리를 사용 하 여 비행 지연 데이터 분석](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)
