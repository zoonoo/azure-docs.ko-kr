<properties 
	pageTitle="데이터 집합 만들기" 
	description="Azure Data Factory 데이터 집합을 이해하고 만드는 방법을 알아봅니다." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/12/2015" 
	ms.author="spelluru"/>

# 데이터 집합

## 설명
데이터 집합은 데이터를 논리적으로 설명합니다. 설명되는 데이터는 단순한 바이트, CSV 파일과 같은 반구조적 데이터에서 관계형 테이블 또는 모델에 이르기까지 다양할 수 있습니다. 데이터에 액세스하는 메커니즘(주소, 프로토콜, 인증 체계)를 연결된 서비스에서 정의하고 데이터 집합 정의에서 참조합니다.

## 구문

	{
	    "name": "<name of dataset>",
	    "properties":
	    {
	       "structure": [ ],
	       "type": "<type of dataset>",
			"external": <boolean flag to indicate external data>,
	        "typeProperties":
	        {
	        },
	        "availability":
	        {
	
	        },
	       "policy": 
	        {      
	
	        }
	    }
	}

**구문 세부 정보**

| 속성 | 설명 | 필수 | 기본값 |
| -------- | ----------- | -------- | ------- |
| name | 데이터 집합의 이름 | 예 | 해당 없음 |
| structure | <p>데이터 집합의 스키마</p><p>자세한 내용은 [데이터 집합 구조](#Structure) 섹션을 참조하세요</p> | 번호 | 해당 없음 |
| type | 데이터 집합의 형식 | 예 | 해당 없음 |
| typeProperties | <p>선택된 형식에 해당하는 속성</p><p>지원되는 형식 및 해당 속성에 대한 자세한 내용은 [데이터 집합 형식](#Type) 섹션을 참조하세요.</p> | 예 | 해당 없음 |
| external | 데이터 집합이 데이터 팩터리 파이프라인에 의해 명시적으로 생성되는지를 지정하는 부울 플래그입니다 | 아니요 | false | 
| availability | <p>데이터 집합 생성에 대한 처리 창 또는 조각화 모델을 정의합니다. </p><p>자세한 내용은 [Dataset 가용성](#Availability) 항목을 참조하세요</p><p>모델을 조각화하는 데이터 집합에 대한 자세한 내용은 [일정 예약 및 실행](data-factory-scheduling-and-execution.md) 문서를 참조하세요</p> | 예 | 해당 없음
| policy | 데이터 집합 조각이 충족해야 하는 기준 또는 조건을 정의합니다. <p>자세한 내용은 [데이터 집합 정책](#Policy) 항목을 참조하세요</p> | 아니요 | 해당 없음 |

### 예

아래는 **Azure SQL 데이터베이스**에서 **MyTable**이라는 테이블을 나타내는 데이터 집합의 예입니다. Azure SQL 데이터베이스 연결 문자열을 데이터 집합에서 참조된 **AzureSqlLinkedService**에서 정의합니다. 이 데이터 집합은 매일 조각화됩니다.

	{
	    "name": "DatasetSample",
	    "properties": {
	        "type": "AzureSqlTable",
	        "linkedServiceName": "AzureSqlLinkedService",
	        "typeProperties": 
	        {
	            "tableName": "MyTable"
	        },
	        "availability": 
	        {
	            "frequency": "Day",
	            "interval": 1
	        }
	    }
	}

## <a name="Structure"></a>데이터 집합 구조

**구조** 섹션은 데이터 집합의 스키마를 확정합니다. 열의 컬렉션 및 이러한 열의 형식을 포함합니다. 각 열에는 다음 속성이 포함됩니다.

속성 | 설명 | 필수 | 기본값  
-------- | ----------- | -------- | -------
name | 열의 이름 | 아니요 | 해당 없음 
type | 열의 데이터 형식 | 아니요 | 해당 없음 

### 예

다음 예에서는 데이터 집합에 3개의 열(licetimestamp, projectname 및 pageviews)이 있습니다.

	structure:  
	[ 
	    { "name": "slicetimestamp", "type": "String"},
	    { "name": "projectname", "type": "String"},
	    { "name": "pageviews", "type": "Decimal"}
	]

## <a name="Type"></a> 데이터 집합 형식

지원되는 데이터 원본 및 데이터 집합 형식을 정렬합니다. 데이터 집합의 유형 및 구성에 대한 내용은 [데이터 이동을 활동](data-factory-data-movement-activities.md) 문서에서 커넥터 항목을 참조하세요.

## <a name="Availability"></a> 데이터 집합 가용성

데이터 집합의 가능성 섹션은 데이터 집합 생성에 대한 처리 창 또는 조각화 모델을 정의합니다. 데이터 집합 조각화 및 종속성 모델에 대한 자세한 내용은 [예약 및 실행](data-factory-scheduling-and-execution.md) 문서를 참조하세요.

| 속성 | 설명 | 필수 | 기본값 |
| -------- | ----------- | -------- | ------- |
| frequency | 데이터 집합 조각 생성을 위한 시간 단위를 지정합니다.<p>**지원되는 빈도**: 분, 시, 일, 주, 월</p> | 예 | 해당 없음 |
| interval | 빈도 승수를 지정합니다.<p>"빈도 x 간격"은 조각을 생성하는 빈도를 결정합니다.</p><p>데이터 집합을 시간 단위로 조각화해야 하는 경우 **빈도**를 **시간**으로, **간격**을 **1**로 지정합니다.</p><p>**참고:** 빈도를 분으로 지정하면 15 이상으로 간격을 설정하는 것이 좋습니다</p> | 예 | 해당 없음 |
| style | 간격의 시작/끝에 조각을 생성해야 하는지를 지정합니다.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><p>빈도를 월에 설정하고 스타일을 EndOfInterval로 설정하는 경우 조각을 월의 마지막 날에 생성합니다. 스타일을 StartOfInterval을 설정하는 경우 조각을 달의 첫 번째 날에 생성합니다.</p><p>빈도를 일에 설정하고 스타일을 EndOfInterval로 설정하는 경우 조각을 일의 마지막 시간에 생성합니다.</p>빈도를 시간에 설정하고 스타일을 EndOfInterval로 설정하는 경우 조각을 시간의 끝에 생성합니다. 예를 들어 오후 1-2시 기간에 대한 조각은 오후 2시에 생성됩니다.</p> | 아니요 | EndOfInterval |
| anchorDateTime | 스케줄러에서 사용하는 시간에 절대 위치를 정의하여 데이터 집합 조각 경계를 계산합니다. <p>**참고:** AnchorDateTime에 빈도보다 더 세분화된 날짜 부분이 있는 경우 더 세분화된 부분은 무시됩니다. 예를 들어 **간격**이 **매시간**(빈도: 시간 및 간격:1회)이고 **AnchorDateTime**이 **분 및 초**를 포함하는 경우 AnchorDateTime의 **분 및 초** 부분은 무시됩니다.</p>| 아니요 | 01/01/0001 |
| offset | 모든 데이터 집합 조각의 시작 및 끝이 이동에 의한 Timespan입니다. <p>**참고:** anchorDateTime 및 offset이 모두 지정되면 결과적으로 이동이 결합됩니다.</p> | 아니요 | 해당 없음 |

### anchorDateTime examples

**예제:** 2007-04-19T08:00:00에 시작하는 23시간 데이터 집합 조각

	"availability":	
	{	
		"frequency": "Hour",		
		"interval": "23",	
		"anchorDateTime":"2007-04-19T08:00:00"	
	}


### offset example

기본 자정 대신 오전 6시에 시작하는 일별 조각

	"availability":
	{
		"frequency": "Daily",
		"interval": "1",
		"offset": "06:00:00"
	}

이 경우 SliceStart는 6시간 이동하고 오전 6시가 됩니다.

12개월(빈도 = 월; 간격 = 12) 예약의 경우, 윤년이든 아니든 연도에 따라 오프셋: 60.00:00:00은 매년 3월 2일 또는 3일을 의미합니다.(스타일 = StartOfInterval인 경우 년의 시작부터 60일)



## <a name="Policy"></a>데이터 집합 정책

데이터 집합의 정책 섹션은 데이터 집합 조각이 충족해야 하는 기준 또는 조건을 정의합니다.

### 정책 유효성 검사

| 정책 이름 | 설명 | 에 적용 | 필수 | 기본값 |
| ----------- | ----------- | ---------- | -------- | ------- |
| minimumSizeMB | Azure blob에서 데이터가 최소 크기 요구 사항을 충족하는지 확인합니다.(메가바이트로) | Azure Blob | 아니요 | 해당 없음 |
|minimumRows | Azure SQL 데이터베이스 또는 Azure 테이블에서 데이터가 최소 행 수를 포함하는지 확인합니다. | <ul><li>Azure SQL 데이터베이스</li><li>Azure 테이블</li></ul> | 아니요 | 해당 없음

#### 예

**minimumSizeMB:**

	"policy":
	
	{
	    "validation":
	    {
	        "minimumSizeMB": 10.0
	    }
	}

**minimumRows**

	"policy":
	{
		"validation":
		{
			"minimumRows": 100
		}
	}

### ExternalData 정책

외부 데이터 집합은 데이터 팩토리에서 실행 중인 파이프라인에 의해 생성되지 않습니다. 데이터 집합이 외부로 표시된 경우 ExternalData 정책을 정의하여 데이터 집합 조각 가용성의 동작에 영향을 미칠 수 있습니다.

| 이름 | 설명 | 필수 | 기본값 |
| ---- | ----------- | -------- | -------------- |
| dataDelay | <p>지정된 조각에 대한 외부 데이터의 가용성 확인을 지연하는 시간입니다. 예를 들어 데이터를 매시간 사용할 수 있어야 하는 경우 외부 데이터를 실제로 사용할 수 있고 해당 조각이 준비되었는지를 보는 확인은 dataDelay에 의해 지연될 수 있습니다.</p><p>현재 시간에만 적용됩니다. 예를 들어 지금이 오후 1시이고 이 값이 10분이라면 유효성 검사는 오후 1시 10분에 시작합니다.</p><p>이 설정은 과거의 조각에 영향을 주지 않습니다.(조각 종료 시간을 가진 조각 + dataDelay < 지금) 지연 없이 처리됩니다.</p> <p>23:59시간보다 큰 시간은 일.시간:분:초 형식을 사용하여 지정해야 합니다. 예를 들어 24시간을 지정하려면 24:00:00을 사용하는 대신 1.00:00:00을 사용합니다. 24:00:00을 사용하면 24일(24.00:00:00)로 처리됩니다. 1일 4시간의 경우 1:04:00:00을 지정합니다. </p>| 아니요 | 0 |
| retryInterval | 오류 발생과 다음 다시 시도 사이의 대기 시간입니다. 현재 시간에 적용됩니다. 이전 시도가 실패한 경우 마지막 시도 후에 이 정도를 대기합니다. <p>지금이 오후 1시인 경우 첫 번째 시도를 시작합니다. 첫 번째 유효성 검사를 완료하는 기간이 1분이며 작업이 실패한 경우 다음 재시도는 1시 + 1분(기간) + 1분(다시 시도 간격) = 오후 1시 2분입니다. </p><p>과거 조각의 경우 지연 시간이 없습니다. 재시도는 즉시 발생합니다.</p> | 아니요 | 00:01:00 (1분) | 
| retryTimeout | 각 다시 시도에 대한 제한 시간입니다.<p>10분으로 설정한 경우 유효성 검사를 10분 내에 완료해야 합니다. 유효성 검사를 수행하는 데 10분 보다 오래 걸리는 경우 다시 시도는 제한 시간을 초과합니다.</p><p>유효성 검사에 대한 모든 시도의 시간이 초과되면 조각에 TimedOut이 표시됩니다.</p> | 아니요 | 00:10:00 (10분) |
| maximumRetry | 외부 데이터의 가용성을 검사한 횟수입니다. 허용되는 최대값은 10입니다. | 아니요 | 3 | 

#### 추가 예제

매달 특정 날짜 및 시간(매달 셋째 날 오전 8시라고 가정)에 파이프라인을 실행해야 하는 경우 **오프셋** 태그를 사용하여 실행할 날짜 및 시간을 설정할 수 있습니다.

	{
	  "name": "MyDataset",
	  "properties": {
	    "type": "AzureSqlTable",
	    "linkedServiceName": "AzureSqlLinkedService",
	    "typeProperties": {
	      "tableName": "MyTable"
	    },
	    "availability": {
	      "frequency": "Month",
	      "interval": 1,
	      "offset": "3.08:10:00",
	      "style": "StartOfInterval"
	    }
	  }
	}

## 피드백 보내기
이 문서에 대한 의견을 보내주시면 감사하겠습니다. 몇 분 정도 시간을 할애해서 [메일](mailto:adfdocfeedback@microsoft.com?subject=data-factory-create-datasets.md)을 통해 의견을 보내주세요.





  

<!---HONumber=Oct15_HO3-->