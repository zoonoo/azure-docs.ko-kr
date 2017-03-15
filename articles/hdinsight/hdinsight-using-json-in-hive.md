---
title: "HDInsight의 하이브를 사용하여 JSON 문서 분석 및 처리 | Microsoft Docs"
description: "JSON 문서를 사용하고 HDInsight의 하이브를 사용하여 이 문서를 분석하는 방법에 대해 알아봅니다."
services: hdinsight
documentationcenter: 
author: rashimg
manager: mwinkle
editor: cgronlun
ms.assetid: e17794e8-faae-4264-9434-67f61ea78f13
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/22/2015
ms.author: rashimg
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: c292772cb21c90bf4373803bfcaa47787c3980b5
ms.lasthandoff: 03/06/2017


---
# <a name="process-and-analyze-json-documents-using-hive-in-hdinsight"></a>HDInsight의 Hive를 사용한 JSON 문서 처리 및 분석
HDInsight에서 Hive를 사용하여 JSON 파일을 분석하고 처리하는 방법에 알아봅니다. 이 자습서에서는 다음 JSON 문서를 사용합니다.

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

이 파일은 wasbs://processjson@hditutorialdata.blob.core.windows.net/에 있습니다. HDInsight에서의 Azure Blob 저장소 사용에 대한 자세한 내용은 [HDInsight에서 Hadoop으로 HDFS 호환 가능한 Azure Blob 저장소 사용](hdinsight-hadoop-use-blob-storage.md)을 참조하세요. 원하는 경우 클러스터의 기본 컨테이너에 파일을 복사할 수 있습니다.

이 자습서에서는 Hive 콘솔을 사용합니다.  Hive 콘솔을 여는 방법은 [원격 데스크톱을 사용하여 HDInsight에서 Hadoop과 Hive 사용](hdinsight-hadoop-use-hive-remote-desktop.md)을 참조하세요.

## <a name="flatten-json-documents"></a>JSON 문서 평면화
다음 섹션에 나열된 메서드에서는 단일 행의 JSON 문서가 필요합니다. 따라서 JSON 문서를 문자열로 평면화해야 합니다. JSON 문서가 이미 평면화되어 있으면 이 단계를 건너뛰고 다음 섹션인 JSON 데이터 분석으로 바로 이동할 수 있습니다.

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

원시 JSON 파일은 **wasbs://processjson@hditutorialdata.blob.core.windows.net/**에 있습니다. *StudentsRaw* Hive 테이블은 평면화되지 않은 JSON 문서를 가리킵니다.

*StudentsOneLine* Hive 테이블은 HDInsight 기본 파일 시스템에서 데이터를 */json/students/* 경로에 저장합니다.

INSERT 문은 StudentOneLine 테이블을 평면화된 JSON 데이터로 채웁니다.

SELECT 문은 1행만 반환해야 합니다.

SELECT 문의 출력은 다음과 같습니다.

![JSON 문서 평면화][image-hdi-hivejson-flatten]

## <a name="analyze-json-documents-in-hive"></a>Hive에서 JSON 문서 분석
하이브는 JSON 문서에서 쿼리를 실행할 수 있는 다음 세 가지 메커니즘을 제공합니다.

* GET\_JSON\_OBJECT UDF(사용자 정의 함수) 사용
* JSON_TUPLE UDF 사용
* 사용자 지정 SerDe 사용
* Python 또는 기타 언어를 사용하여 자체 UDF를 작성합니다. Hive에서 Python 실행에 대한 자세한 내용은 [이 문서][hdinsight-python]를 참조하세요.

### <a name="use-the-getjsonobject-udf"></a>GET\_JSON_OBJECT UDF 사용
Hive는 런타임에 JSON 쿼리를 수행할 수 있는 [get json object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) 라는 기본 제공 UDF를 제공합니다. 이 메서드에는 두 개의 인수, 즉 평면화된 JSON 문서와 구문 분석해야 하는 JSON 필드가 있는 테이블 이름 및 메서드 이름이 사용됩니다. 이 UDF의 작동 방식에 대한 예를 살펴보겠습니다.

학생의 이름 및 성 가져오기

    SELECT
      GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
      GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
    FROM StudentsOneLine;

이 쿼리를 콘솔 창에서 실행할 경우의 출력은 다음과 같습니다.

![get_json_object UDF][image-hdi-hivejson-getjsonobject]

get-json_object UDF에는 몇 가지 제한 사항이 있습니다.

* 쿼리의 각 필드에서는 쿼리를 다시 구문 분석해야 하므로 성능에 영향을 미칩니다.
* GET\_JSON_OBJECT()는 배열의 문자열 표현을 반환합니다. 이를 Hive 배열로 변환하려면 정규식을 사용하여 대괄호 ‘[‘ 및  ‘]’을 바꾼 다음 분할 호출하여 배열을 가져와야 합니다.

그렇기 때문에 Hive Wiki에서는 json_tuple을 사용하는 것을 권장합니다.  

### <a name="use-the-jsontuple-udf"></a>JSON_TUPLE UDF 사용
Hive에서 제공하는 다른 UDF는 [get_ json _object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object)보다 성능이 뛰어난 [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple)입니다. 이 메서드는 키 집합 및 JSON 문자열을 사용하며, 하나의 함수를 통해 값의 튜플을 반환합니다. 다음 쿼리는 JSON 문서에서 학생 ID와 등급을 반환합니다.

    SELECT q1.StudentId, q1.Grade
      FROM StudentsOneLine jt
      LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
        AS StudentId, Grade;

Hive 콘솔에 표시되는 이 스크립트의 출력은 다음과 같습니다.

![json_tuple UDF][image-hdi-hivejson-jsontuple]

JSON\_TUPLE은 Hive에서 [lateral view](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) 구문을 사용합니다. 이 구문을 사용하면 json\_tuple이 원래 테이블의 각 행에 UDT 함수를 적용하여 가상 테이블을 만들 수 있습니다.  LATERAL VIEW를 반복 사용하면 복잡한 JSON이 다루기 어렵게 됩니다. 또한 JSON_TUPLE은 중첩된 JSON을 처리할 수 없습니다.

### <a name="use-custom-serde"></a>사용자 지정 SerDe 사용
SerDe는 중첩된 JSON 문서에 가장 적합한 구문 분석으로, JSON 스키마를 정의하고 이 스키마를 사용하여 문서를 구문 분석할 수 있습니다. 이 자습서에서는 [rcongiu](https://github.com/rcongiu)에서 개발한 가장 일반적인 SerDe 중 하나를 사용하게 됩니다.

**사용자 지정 SerDe를 사용하려면**

1. [Java SE Development Kit 7u55 JDK 1.7.0_55](http://www.oracle.com/technetwork/java/javase/downloads/java-archive-downloads-javase7-521261.html#jdk-7u55-oth-JPR)를 설치합니다. HDInsight의 Windows 배포를 사용하려면 Windows X64 버전 JDK를 선택합니다.
   
   > [!WARNING]
   > JDK 1.8은 이 SerDe에서 작동하지 않습니다.
   > 
   > 
   
    설치 완료 후 새 사용자 환경 변수를 추가합니다.
   
   1. Windows 화면에서 **고급 시스템 설정 보기** 를 엽니다.
   2. **환경 변수**를 클릭합니다.  
   3. 새 **JAVA_HOME** 환경 변수를 추가합니다. 이 변수는 **C:\Program Files\Java\jdk1.7.0_55** 또는 JDK 설치 위치를 가리킵니다.
      
      ![JDK에 대한 올바른 구성 값 설정][image-hdi-hivejson-jdk]
2. [Maven 3.3.1](http://mirror.olnevhost.net/pub/apache/maven/maven-3/3.3.1/binaries/apache-maven-3.3.1-bin.zip)
   
    제어판-->사용자 계정의 환경 변수에 대한 시스템 변수 편집으로 이동하여 bin 폴더를 경로에 추가합니다. 아래의 스크린샷은 이 작업을 수행하는 방법을 보여 줍니다.
   
    ![Maven 설치][image-hdi-hivejson-maven]
3. [Hive-JSON-SerDe](https://github.com/sheetaldolas/Hive-JSON-Serde/tree/master) github 사이트에서 프로젝트를 복제합니다. 아래 스크린샷에 표시된 것처럼 "Zip 다운로드" 단추를 클릭하면 됩니다.
   
    ![프로젝트 복제][image-hdi-hivejson-serde]

4: 이 패키지를 다운로드한 폴더로 이동하여 "mvn package"를 입력합니다. 클러스터에 복사할 수 있는 필수 jar 파일이 만들어집니다.

5: 패키지를 다운로드한 루트 폴더 아래의 대상 폴더로 이동합니다. 클러스터의 헤드 노드에 json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar 파일을 업로드합니다. 일반적으로 C:\apps\dist\hive-0.13.0.2.1.11.0-2316\bin과 유사한 하이브 이진 파일 폴더에 둡니다.

6: Hive 프롬프트에서 “add jar /path/to/json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar”을 입력합니다. 이 예제에서는 jar이 C:\apps\dist\hive-0.13.x\bin 폴더에 있으므로 아래 표시된 이름으로 jar을 직접 추가할 수 있습니다.

    add jar json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar;

   ![프로젝트에 JAR 추가][image-hdi-hivejson-addjar]

이제 SerDe를 사용하여 JSON 문서를 쿼리할 준비가 완료되었습니다.

다음 문은 정의된 스키마가 있는 테이블을 만듭니다.

    DROP TABLE json_table;
    CREATE EXTERNAL TABLE json_table (
      StudentId string,
      Grade int,
      StudentDetails array<struct<
          FirstName:string,
          LastName:string,
          YearJoined:int
          >
      >,
      StudentClassCollection array<struct<
          ClassId:string,
          ClassParticipation:string,
          ClassParticipationRank:string,
          Score:int,
          PerformedActivity:boolean
          >
      >
    ) ROW FORMAT SERDE 'org.openx.data.jsonserde.JsonSerDe'
    LOCATION '/json/students';

학생의 이름 및 성을 나열하려면

    SELECT StudentDetails.FirstName, StudentDetails.LastName FROM json_table;

하이브 콘솔의 결과는 다음과 같습니다.

![SerDe 쿼리 1][image-hdi-hivejson-serde_query1]

JSON 문서의 성적 합계를 계산하려면

    SELECT SUM(scores)
    FROM json_table jt
      lateral view explode(jt.StudentClassCollection.Score) collection as scores;

위 쿼리에서는 성적의 합계를 구할 수 있도록 [lateral view explode](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) UDF를 사용하여 성적 배열을 확장합니다.

하이브 콘솔의 출력은 다음과 같습니다.

![SerDe 쿼리 2][image-hdi-hivejson-serde_query2]

To find which subjects a given student has scored more than 80 points SELECT  
      jt.StudentClassCollection.ClassId FROM json_table jt lateral view explode(jt.StudentClassCollection.Score) collection as score  where score > 80;

위 쿼리는 문자열을 반환하는 get\_json\_object와 달리 하이브 배열을 반환합니다.

![SerDe 쿼리 3][image-hdi-hivejson-serde_query3]

잘못된 형식의 JSON을 중지하려면 이 SerDe의 [Wiki 페이지](https://github.com/sheetaldolas/Hive-JSON-Serde/tree/master) 에 설명된 대로 아래 코드를 입력하면 됩니다.  

    ALTER TABLE json_table SET SERDEPROPERTIES ( "ignore.malformed.json" = "true");




## <a name="summary"></a>요약
결론적으로 선택하는 하이브의 JSON 연산자 유형은 시나리오에 따라 달라집니다. JSON 문서가 간단하고 조회할 필드가 하나뿐인 경우에는 하이브 UDF get\_json\_object를 사용할 수 있습니다. 조회가 키가 두 개 이상인 경우에는 json_tuple을 사용할 수 있습니다. 중첩된 문서가 있는 경우에는 JSON SerDe를 사용해야 합니다.

다른 관련된 문서는 다음을 참조하세요.

* [샘플 Apache log4j 파일 분석을 위해 HDInsight에서 Hadoop과 함께 Hive 및 HiveQL 사용](hdinsight-use-hive.md)
* [HDInsight의 Hive를 사용하여 비행 지연 데이터 분석](hdinsight-analyze-flight-delay-data.md)
* [HDInsight에서 Hive를 사용하여 Twitter 데이터 분석](hdinsight-analyze-twitter-data.md)
* [DocumentDB 및 HDInsight를 사용해서 Hadoop 실행](../documentdb/documentdb-run-hadoop-with-hdinsight.md)

[hdinsight-python]: hdinsight-python.md

[image-hdi-hivejson-flatten]: ./media/hdinsight-using-json-in-hive/flatten.png
[image-hdi-hivejson-getjsonobject]: ./media/hdinsight-using-json-in-hive/getjsonobject.png
[image-hdi-hivejson-jsontuple]: ./media/hdinsight-using-json-in-hive/jsontuple.png
[image-hdi-hivejson-jdk]: ./media/hdinsight-using-json-in-hive/jdk.png
[image-hdi-hivejson-maven]: ./media/hdinsight-using-json-in-hive/maven.png
[image-hdi-hivejson-serde]: ./media/hdinsight-using-json-in-hive/serde.png
[image-hdi-hivejson-addjar]: ./media/hdinsight-using-json-in-hive/addjar.png
[image-hdi-hivejson-serde_query1]: ./media/hdinsight-using-json-in-hive/serde_query1.png
[image-hdi-hivejson-serde_query2]: ./media/hdinsight-using-json-in-hive/serde_query2.png
[image-hdi-hivejson-serde_query3]: ./media/hdinsight-using-json-in-hive/serde_query3.png
[image-hdi-hivejson-serde_result]: ./media/hdinsight-using-json-in-hive/serde_result.png

