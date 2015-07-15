<properties
   pageTitle="HDInsight의 하이브를 사용하여 JSON 문서 분석 및 처리 | Microsoft Azure"
   description="JSON 문서를 사용하고 HDInsight의 하이브를 사용하여 이 문서를 분석하는 방법에 대해 알아봅니다."
   services="hdinsight"
   documentationCenter=""
   authors="rashimg"
   manager="mwinkle"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="04/14/2015"
   ms.author="rashimg"/>


# 하이브에서 JSON 문서를 처리 및 분석하는 방법에 대해 알아봅니다.

JSON은 웹에서 가장 일반적으로 사용되는 형식 중 하나입니다. 이 자습서에서는 하이브와 관련된 일반적인 질문 중 하나인 하이브에서 JSON 문서를 사용한 후 분석을 실행하는 방법에 대해 알아봅니다.

##JSON 예제

예제를 살펴보겠습니다. 아래에 표시된 JSON 문서가 있다고 가정합니다. 이 자습서의 목적은 이 JSON 문서를 구문 분석한 후 키에서 값 조회, 집계 등 이 문서에서 쿼리를 실행할 수 있도록 하는 것입니다.

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

##JSON 문서 평면화(JSON이 있는 경우에만 필요한 단계)

하이브 연산자를 사용하여 분석을 수행하기 전에 하이브에서 사용할 준비가 완료되도록 JSON 문서를 미리 처리해야 합니다.

다음 섹션에 나열된 모든 옵션은 JSON 문서가 단일 행으로 되어 있다고 가정합니다. 즉, 하이브 연산자에서 사용할 수 있도록 JSON 문서를 문자열로 평면화해야 합니다.

**JSON 문서가 이미 평면화되어 있고 전체 문서가 한 줄에 있으면 이 단계를 건너뛰고 다음 섹션인 JSON 데이터 분석으로 바로 이동할 수 있습니다.**

여러 행에 걸쳐 있는 평면화되지 않은 JSON 문서가 Azure Blob 저장소의 기본 컨테이너 아래 */json/input/* 폴더에 있다고 가정해 보겠습니다. 아래 코드는 평면화되지 않은 원래 JSON 문서를 가리키는 jsonexample 테이블을 만듭니다.

    drop table jsonexample;
    CREATE EXTERNAL TABLE jsonexample (textcol string) stored as textfile location '/json/input/';

평면화된 JSON 문서를 가리키고 Azure Blob 저장소의 기본 컨테이너 아래 */json/temp/* 폴더에 저장되는 one_line_json이라는 새 테이블을 만듭니다.

    drop table if exists one_line_json;
    create external table one_line_json
    (
      json_body string
    )
    stored as textfile location '/json/temp';

마지막으로 이 테이블을 평면화된 JSON 데이터로 채웁니다.

    insert overwrite table one_line_json
    select concat_ws(' ',collect_list(textcol)) as singlelineJSON 
      from (select INPUT__FILE__NAME,BLOCK__OFFSET__INSIDE__FILE,textcol from jsonexample distribute by INPUT__FILE__NAME sort by BLOCK__OFFSET__INSIDE__FILE ) 
      x group by INPUT__FILE__NAME;

이제 새로 만든 one_line_json 테이블을 보면 JSON 문서가 단일 줄에 포함되어 있는 것을 볼 수 있습니다.

    select * from one_line_json;

이 쿼리의 출력은 다음과 같습니다.

![JSON 문서 평면화][image-hdi-hivejson-flatten]

##하이브에서 JSON 문서를 분석하기 위한 옵션

이제 JSON 문서가 단일 열의 테이블에 있으므로 하이브를 사용하여 이 데이터에 대한 쿼리를 실행할 준비가 완료되었습니다. 하이브는 JSON 문서에서 쿼리를 실행할 수 있는 다음 세 가지 메커니즘을 제공합니다.

1.	UDF(사용자 정의 함수) 사용: get_json_object
2.	UDF 사용: json_tuple
3.	사용자 지정 SerDe 사용

각 메커니즘에 대해 자세히 알아보겠습니다.

##get_json_object UDF
하이브는 런타임에 JSON 쿼리를 수행할 수 있는 [get_json_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object)라는 기본 제공 UDF를 제공합니다. 이 메서드에는 두 개의 인수, 즉 평면화된 JSON 문서와 구문 분석해야 하는 JSON 필드가 있는 테이블 이름 및 메서드 이름이 사용됩니다. 이 UDF의 작동 방식에 대한 예를 살펴보겠습니다.

학생의 이름 및 성 가져오기

    select get_json_object(one_line_json.json_body, '$.StudentDetails.FirstName'), get_json_object(one_line_json.json_body, '$.StudentDetails.LastName') from one_line_json;

이 쿼리를 콘솔 창에서 실행할 경우의 출력은 다음과 같습니다.

![get_json_object UDF][image-hdi-hivejson-getjsonobject]

이 UDF에는 몇 가지 제한 사항이 있습니다.


- 주요 제한 사항 중 하나는 이 UDF는 쿼리의 각 필드에서 쿼리 구문을 다시 분석해야 하며 이는 성능에 영향을 주기 때문에 성능이 떨어진다는 점입니다.
- 둘째로, get_json_object()는 배열의 문자열 표현을 반환합니다. 따라서 이를 하이브 배열로 변환하려면 정규식을 사용하여 대괄호 ‘[‘ 및 ‘]’을 바꾼 다음 분할 호출하여 배열을 가져와야 합니다.


그렇기 때문에 하이브 Wiki에서는 json_tuple(나중에 설명)을 사용하는 것을 권장합니다.


##json_tuple UDF

하이브에서 제공하는 다른 UDF는 [get_json_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object)보다 성능이 뛰어난 [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple)입니다. 이 메서드는 키 집합 및 JSON 문자열을 사용하며, 하나의 함수를 통해 값의 튜플을 반환합니다. 이 UDF의 작동 방식에 대한 예를 살펴보겠습니다.

JSON 문서에서 학생 ID 및 성적을 가져와 보겠습니다.

    select q1.StudentId, q1.Grade 
      from one_line_json jt
      LATERAL VIEW json_tuple(jt.json_body, 'StudentId', 'Grade') q1
        as StudentId, Grade;

하이브에서 [lateral view](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) 구문을 사용합니다. 이 구문을 사용하면 json_tuple이 원래 테이블의 각 행에 UDT 함수를 적용하여 가상 테이블을 만들 수 있습니다.

하이브 콘솔에 표시되는 이 스크립트의 출력을 살펴보겠습니다.

![json_tuple UDF][image-hdi-hivejson-jsontuple]

이 UDF의 주요 단점 중 하나는 LATERAL VIEW의 반복된 사용으로 인해 복잡한 JSON을 다루기 어렵다는 점입니다. 실제로 이 UDF는 중첩된 JSON을 처리할 수 없습니다.

##사용자 지정 SerDe 사용

중첩된 JSON 문서의 구문 분석에는 SerDe를 사용하는 것이 **가장 좋습니다**. JSON 스키마를 정의하여 쿼리를 매우 쉽게 실행할 수 있기 때문입니다.

[rcongiu](https://github.com/rcongiu)에서 개발한 가장 일반적인 SerDe 중 하나를 사용하는 방법에 대해 알아보겠습니다.

1단계: [Java SE Development Kit 7u55 JDK 1.7.0_55](http://www.oracle.com/technetwork/java/javase/downloads/java-archive-downloads-javase7-521261.html#jdk-7u55-oth-JPR)가 설치되어 있는지 확인합니다(참고: JDK 1.8이 설치된 경우에는 이 SerDe를 사용할 수 없음).


- HDInsight의 Windows 배포를 사용하려면 x64 Windows 버전의 JDK를 선택합니다.
- 설치가 완료되면 제어판 --> 환경 변수 추가로 이동하여 C:\Program Files\Java\jdk1.7.0_55 또는 JDK가 설치된 위치를 가리키는 새 JAVA_HOME 환경 변수를 추가합니다. 다음 스크린샷은 환경 변수를 설정하는 방법을 보여 줍니다.

![JDK에 대한 올바른 구성 값 설정][image-hdi-hivejson-jdk]

2단계: [이](http://mirror.olnevhost.net/pub/apache/maven/maven-3/3.3.1/binaries/apache-maven-3.3.1-bin.zip) 링크로 이동하여 Maven 3.3.1을 다운로드합니다. 이진 파일을 저장할 곳에 보관 파일의 압축을 풉니다. 이 예제에서는 C:\Program Files\Maven에 압축을 풉니다. 제어판-->사용자 계정의 환경 변수에 대한 시스템 변수 편집으로 이동하여 bin 폴더를 경로에 추가합니다. 아래의 스크린샷은 이 작업을 수행하는 방법을 보여 줍니다.

![Maven 설치][image-hdi-hivejson-maven]

3단계: [Hive-JSON-SerDe](https://github.com/sheetaldolas/Hive-JSON-Serde/tree/master) github 사이트에서 프로젝트를 복제합니다. 아래 스크린샷에 표시된 것처럼 "Zip 다운로드" 단추를 클릭하면 됩니다.

![프로젝트 복제][image-hdi-hivejson-serde]

4단계: 이 패키지를 다운로드한 폴더로 이동하여 "mvn package"를 입력합니다. 클러스터에 복사할 수 있는 필수 jar 파일이 만들어집니다.

5단계: 패키지를 다운로드한 루트 폴더 아래의 대상 폴더로 이동합니다. 클러스터의 헤드 노드에 json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar 파일을 업로드합니다. 일반적으로 C:\apps\dist\hive-0.13.0.2.1.11.0-2316\bin과 유사한 하이브 이진 파일 폴더에 둡니다.
 
6단계: 하이브 프롬프트에서 “add jar /path/to/json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar”을 입력합니다. 이 예제에서는 jar이 C:\apps\dist\hive-0.13.x\bin 폴더에 있으므로 아래 표시된 이름으로 jar을 직접 추가할 수 있습니다.

    add jar json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar;

![프로젝트에 JAR 추가][image-hdi-hivejson-addjar]

이제 SerDe를 사용하여 JSON 문서를 쿼리할 준비가 완료되었습니다.

먼저 JSON 문서의 스키마가 있는 테이블을 만듭니다. 맵, 배열, 구조체 등 훨씬 다양한 유형을 처리할 수 있습니다. 하이브가 아래의 스키마에 따라 JSON 문서를 읽을 수 있는 json_table이라는 테이블을 만들 수 있도록 하이브 콘솔에서 아래 코드를 입력합니다.

    drop table json_table;
    create external table json_table (
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
    ) row format serde 'org.openx.data.jsonserde.JsonSerDe'
    location '/json/temp';

스키마를 정의했으므로 이제 하이브를 사용하여 JSON 문서를 쿼리하는 방법을 살펴볼 수 있는 몇 가지 예제를 실행해 보겠습니다.

a) 학생의 이름 및 성 나열

    select StudentDetails.FirstName, StudentDetails.LastName from json_table;

하이브 콘솔의 결과는 다음과 같습니다.

![SerDe 쿼리 1][image-hdi-hivejson-serde_query1]

b) 이 쿼리에서 JSON 문서의 성적 합계 계산

    select sum(scores)
    from json_table jt
      lateral view explode(jt.StudentClassCollection.Score) collection as scores;
       
위 쿼리에서는 성적의 합계를 구할 수 있도록 [lateral view explode](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) UDF를 사용하여 성적 배열을 확장합니다.

하이브 콘솔의 출력은 다음과 같습니다.

![SerDe 쿼리 2][image-hdi-hivejson-serde_query2]

c) 지정된 학생의 성적이 80점을 넘는 과목 찾기 select jt.StudentClassCollection.ClassId from json_table jt lateral view explode(jt.StudentClassCollection.Score) collection as score where score > 80;
      
위 쿼리는 문자열을 반환하는 get_json_object와 달리 하이브 배열을 반환합니다.

![SerDe 쿼리 3][image-hdi-hivejson-serde_query3]

잘못된 형식의 JSON을 중지하려면 이 SerDe의 [Wiki 페이지](https://github.com/sheetaldolas/Hive-JSON-Serde/tree/master)에 설명된 대로 아래 코드를 입력하면 됩니다.

    ALTER TABLE json_table SET SERDEPROPERTIES ( "ignore.malformed.json" = "true");


##기타 옵션
아래 나열된 옵션과 다른 옵션으로, Python 또는 시나리오에 특정한 다른 언어를 사용하여 사용자 지정 코드를 작성할 수도 있습니다. Python 스크립트가 준비된 경우 [이 문서][hdinsight-python]를 참조하여 하이브와 함께 사용자 고유의 Python 코드를 실행할 수 있습니다.

##요약
결론적으로 선택하는 하이브의 JSON 연산자 유형은 시나리오에 따라 달라집니다. JSON 문서가 간단하고 조회할 필드가 하나뿐인 경우에는 하이브 UDF get_json_object를 사용할 수 있습니다. 조회가 키가 두 개 이상인 경우에는 json_tuple을 사용할 수 있습니다. 중첩된 문서가 있는 경우에는 JSON SerDe를 사용해야 합니다.

HDInsight에서는 광범위한 사용자 작업 없이도 하이브에서 기본적으로 더 많은 형식을 손쉽게 사용할 수 있도록 하기 위해 노력하고 있습니다. 공유할 내용이 있으면 더 많은 세부 정로 이 자습서를 업데이트하겠습니다.

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
 

<!---HONumber=62-->