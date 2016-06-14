<properties 
	pageTitle="DocumentDB의 요청 단위 | Microsoft Azure" 
	description="DocumentDB의 요청 단위 요구 사항을 이해, 지정 및 예측하는 방법에 알아봅니다." 
	services="documentdb" 
	authors="stephbaron" 
	manager="jhubbard" 
	editor="mimig" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/31/2016" 
	ms.author="stbaro"/>

#DocumentDB의 요청 단위
지금 사용 가능: DocumentDB [요청 단위 계산기](https://www.documentdb.com/capacityplanner) 자세한 내용을 보려면 [처리량 요구 예측](documentdb-request-units.md#estimating-throughput-needs)을 참조하세요.

![처리량 계산기][5]

##소개
이 문서에서는 [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/)의 요청 단위의 개요를 제공합니다.

이 문서를 읽은 다음에는 다음과 같은 질문에 답할 수 있습니다.

-	요청 단위 및 요청 요금이 무엇인가요?
-	컬렉션에 대해 요청 단위 용량을 지정하려면 어떻게 해야 하나요?
-	내 응용 프로그램에 필요한 요청 단위를 어떻게 추정할 수 있나요?
-	컬렉션의 요청 단위 용량을 초과하면 어떻게 되나요?


##요청 단위 및 요청 요금
DocumentDB는 응용 프로그램의 처리량 수요를 충족하도록 리소스를 *예약*하여 신속하고 예측 가능한 성능을 제공합니다. 시간이 지나면 응용 프로그램 로드 및 액세스 패턴이 변하는데, DocumentDB를 사용하면 응용 프로그램에 제공되는 예약된 처리량을 간편하게 늘리거나 줄일 수 있습니다.

DocumentDB에서는 예약된 처리량이 초당 처리하는 요청 단위로 지정됩니다. 요청 단위란 응용 프로그램에 보장되는 초당 요청 단위의 양을 *예약*할 수 있는 처리량 통화라고 생각하시면 됩니다. 문서 작성, 쿼리 수행, 문서 업데이트 등 DocumentDB의 각 작업에서는 CPU, 메모리 및 IOPS를 사용합니다. 즉, 각 작업이 *요청 요금*을 발생시키고, 요청 요금은 *요청 단위*로 표시됩니다. 응용 프로그램의 처리량 요구 사항과 함께 요청 단위 요금에 영향을 주는 요소를 이해하면 응용 프로그램을 최대한 경제적으로 실행할 수 있습니다.

##요청 단위 용량 지정
DocumentDB 컬렉션을 만들 때 컬렉션에 대해 예약하려는 초당 요청 단위 수(RU)를 지정합니다. 컬렉션이 생성되면 지정된 RU 할당 전체가 해당 컬렉션에 사용되도록 예약됩니다. 컬렉션마다 격리된 전용 처리량 특징이 보장됩니다.

DocumentDB는 예약 모델에서 작동한다는 점에 주의해야 합니다. 즉, 능동적으로 *사용된* 처리량이 얼마가 됐든, 해당 컬렉션에 대해 *예약된* 처리량에 따라 요금이 청구됩니다. 하지만 응용 프로그램의 부하, 데이터 및 사용 패턴이 변하면 그에 따라 DocumentDB SDK를 통해 또는 [Azure 포털](https://portal.azure.com)을 사용하여 예약된 RU 양을 간단하게 늘리거나 줄일 수 있습니다. 처리량을 늘리고 줄이는 방법에 대한 자세한 내용은 [DocumentDB 성능 수준](documentdb-performance-levels.md)을 참조하세요.

##요청 단위 고려 사항
DocumentDB 컬렉션에 대해 예약할 요청 단위 수를 예상할 때 다음 변수를 고려해야 합니다.

- **문서 크기**. 문서 크기가 증가할수록 데이터를 읽거나 쓰는 데 사용되는 단위도 증가합니다.
- **문서 속성 수**. 모든 속성의 기본 인덱싱을 가정할 경우 속성 수가 증가할수록 문서를 쓰는 데 사용되는 단위가 증가합니다.
- **데이터 일관성**. 강력 또는 제한된 부실 데이터 일관성 수준을 사용하는 경우 문서를 읽는 데 추가 단위가 사용됩니다.
- **인덱싱된 속성**. 각 컬렉션의 인덱스 정책에 따라 기본적으로 인덱싱되는 속성이 결정됩니다. 인덱싱되는 속성 수를 제한하거나 지연 인덱싱을 사용하면 요청 단위 사용을 줄일 수 있습니다.
- **문서 인덱싱**. 기본적으로 각 문서가 자동으로 인덱싱되며, 일부 문서를 인덱싱하지 않도록 선택하면 더 적은 요청 단위가 사용됩니다.
- **쿼리 패턴**. 쿼리의 복잡성은 작업에 사용되는 요청 단위의 양에 영향을 줍니다. 조건자의 수, 조건자의 특성, 프로젝션, UDF 수 및 원본 데이터 집합의 크기는 모두 쿼리 작업의 비용에 영향을 줍니다.
- **스크립트 사용량**. 쿼리와 마찬가지로, 저장된 프로시저 및 트리거는 수행하는 작업의 복잡성에 따라 요청 단위를 사용합니다. 응용 프로그램을 개발하면서 요청 요금 헤더를 검사하면 각 작업이 요청 단위 용량을 어떻게 사용하는지 파악하는 데 도움이 됩니다.

##필요한 처리량 예측
요청 단위는 요청 처리 비용의 정규화된 측정값입니다. 단일 요청 단위는 10개의 고유한 속성 값(시스템 속성 제외)으로 구성된 1KB JSON 문서 하나를 읽는 데(self 링크 또는 ID를 통해) 필요한 처리 용량을 나타냅니다. 동일한 문서를 생성(삽입), 대체 또는 삭제하는 요청은 서비스에서 추가 처리를 사용하므로 더 많은 요청 단위가 사용됩니다.

> [AZURE.NOTE] 1KB 문서에 대한 한 개 요청 단위의 기초는 문서의 self 링크 또는 ID에 의한 간단한 GET에 해당합니다.

###요청 단위 계산기를 사용합니다.
고객이 해당 처리량 예측을 미세 조정하도록 도우려면 다음을 포함한 일반적인 작업에 대한 요청 단위 요구 사항을 예측하는 웹 기반 [요청 단위 계산기](https://www.documentdb.com/capacityplanner)를 사용합니다.

- 문서 만들기(쓰기)
- 문서 읽기
- 문서 삭제

도구를 사용하는 것은 간단합니다.

1. 하나 이상의 대표 JSON 문서를 업로드합니다.

	![요청 단위 계산기에 문서 업로드][2]

2. 문서 수를 입력하여 필요한 작업을 만들고 읽고 삭제합니다(초 단위 별로).

	![요청 단위 계산기에 처리량 요구 입력][3]

3. 결과를 계산하고 검토하도록 클릭합니다.

	![요청 단위 계산기 결과][4]

>[AZURE.NOTE]인덱싱된 속성과 크기 및 수가 많이 다른 문서 유형이 있는 경우 일반 문서의 각 *유형*을 도구에 업로드한 다음 결과를 계산합니다.

###DocumentDB 요청 요금 응답 헤더를 사용합니다.
DocumentDB 서비스의 모든 응답은 요청에 사용된 요청 단위가 포함된 사용자 지정 헤더(x-ms-request-charge)를 포함하고 있습니다. 이 헤더는 DocumentDB SDK를 통해 액세스할 수도 있습니다. .NET SDK에서 RequestCharge는 ResourceResponse 개체의 속성입니다. 쿼리의 경우 Azure 포털의 DocumentDB 쿼리 탐색기는 실행된 쿼리에 대한 요청 요금 정보를 제공합니다.

![쿼리 탐색기에서 RU 요금을 검사하는 중][1]

이 점을 염두에 두고, 응용 프로그램에 필요한 예약된 처리량을 예측하는 한 가지 방법은 응용 프로그램에서 사용하는 대표적인 문서에 대해 실행되는 일반 작업과 연결된 요청 단위 요금을 기록한 다음, 예상되는 초당 수행 작업 수를 추정하는 것입니다. 일반 쿼리 및 DocumentDB 스크립트 사용량도 측정하여 포함해야 합니다.

>[AZURE.NOTE]인덱싱된 속성과 크기 및 수가 많이 다른 문서 유형이 있는 경우에는 일반 문서의 각 *유형*과 연결된 적용 가능한 작업 요청 단위 요금을 기록합니다.

예를 들면 다음과 같습니다.

1. 일반 문서를 만드는(삽입하는) 요청 단위 요금을 기록합니다. 
2. 일반 문서를 읽는 요청 단위 요금을 기록합니다.
3. 일반 문서를 업데이트하는 요청 단위 요금을 기록합니다.
3. 일반적이고 공통적인 문서 쿼리의 요청 단위 요금을 기록합니다.
4. 응용 프로그램에서 활용하는 모든 사용자 지정 스크립트(저장된 프로시저, 트리거, 사용자 정의 함수)의 요청 단위 요금을 기록합니다.
5. 예상되는 초당 작업 수를 고려하여 필요한 요청 단위를 계산합니다.

##요청 단위 추정 예제
다음과 같은 ~1KB 문서를 가정하겠습니다.

	{
	 "id": "08259",
  	"description": "Cereals ready-to-eat, KELLOGG, KELLOGG'S CRISPIX",
  	"tags": [
    	{
      	"name": "cereals ready-to-eat"
    	},
    	{
      	"name": "kellogg"
    	},
    	{
      	"name": "kellogg's crispix"
    	}
	],
  	"version": 1,
  	"commonName": "Includes USDA Commodity B855",
  	"manufacturerName": "Kellogg, Co.",
  	"isFromSurvey": false,
  	"foodGroup": "Breakfast Cereals",
  	"nutrients": [
    	{
      	"id": "262",
      	"description": "Caffeine",
      	"nutritionValue": 0,
      	"units": "mg"
    	},
    	{
      	"id": "307",
      	"description": "Sodium, Na",
      	"nutritionValue": 611,
      	"units": "mg"
    	},
    	{
      	"id": "309",
      	"description": "Zinc, Zn",
      	"nutritionValue": 5.2,
      	"units": "mg"
    	}
  	],
  	"servings": [
    	{
      	"amount": 1,
      	"description": "cup (1 NLEA serving)",
      	"weightInGrams": 29
    	}
  	]
	}

>[AZURE.NOTE]문서가 DocumentDB에서 축소되기 때문에 위에서 시스템이 계산한 문서 크기가 1KB보다 약간 작습니다.


다음 테이블은 이 문서의 일반 작업에 대한 대략적인 요청 단위 요금을 보여 줍니다(대략적인 요청 단위 요금은 계정 일관성 수준이 "세션"으로 설정되어 있고 모든 문서가 자동으로 인덱싱되는 것으로 가정).

작업|요청 단위 요금 
---|---
문서 만들기|~15 RU 
문서 읽기|~1 RU
ID로 문서 쿼리|~2.5 RU

이 테이블은 응용 프로그램에 사용되는 일반 쿼리의 대략적인 요청 단위 요금도 보여 줍니다.

쿼리|요청 단위 요금|반환된 문서의 수
---|---|--- 
ID로 음식 선택|~2.5 RU|1 
제조업체로 음식 선택|~7 RU|7
음식 그룹으로 선택하고 무게로 주문|~70 RU|100
음식 그룹의 상위 10개 음식 선택|~10 RU|10

>[AZURE.NOTE]RU 요금은 반환되는 문서 수에 따라 다릅니다.

이 정보를 통해, 예상되는 초당 작업 및 쿼리 수에 따라 이 응용 프로그램에 필요한 RU를 예측할 수 있습니다.

작업/쿼리|예상되는 초당 수|필요한 RU 
---|---|--- 
문서 만들기|10|150 
문서 읽기|100|100 
제조업체로 음식 선택|25|175 
음식 그룹으로 선택|10|700 
상위 10개 선택|15|총 150|155|1275

이 예에서는 필요한 평균 처리량이 1,275 RU/s로 예상됩니다. 가장 가까운 100자리 숫자로 반올림하면 이 응용 프로그램의 컬렉션에 1,300 RU/s를 프로비전하면 됩니다.

##예약된 처리량 제한 초과
요청 단위 소비는 초당 비율로 평가된다고 했습니다. 컬렉션에서 프로비전된 요청 단위 속도를 초과하는 응용 프로그램의 경우 비율이 예약된 수준 이하로 떨어질 때까지 해당 컬렉션에 대한 요청이 제한됩니다. 스로틀이 발생하면 서버에서 RequestRateTooLarge(HTTP 상태 코드 429)를 사용하여 선제적으로 요청을 종료하고, 사용자가 요청을 다시 시도할 수 있을 때까지 기다려야 하는 시간을 밀리초 단위로 표시하는 x-ms-retry-after-ms 헤더를 반환합니다.

	HTTP Status 429
	Status Line: RequestRateTooLarge
	x-ms-retry-after-ms :100

.NET 클라이언트 SDK 및 LINQ 쿼리를 사용하는 경우에는 거의 대부분 이 예외를 처리할 필요가 없습니다. .NET 클라이언트 SDK 최신 버전이 이 응답을 암시적으로 catch하고, 서버에서 지정한 retry-after 헤더를 준수하고, 요청을 다시 시도하기 때문입니다. 동시에 여러 클라이언트가 계정에 액세스하지만 않으면 다음 재시도가 성공할 것입니다.

여러 클라이언트가 누적적으로 요청 속도를 초과하여 작동하는 경우에는 기본 재시도 동작으로 충분하지 않을 수 있으며, 클라이언트가 응용 프로그램에 상태 코드 429와 함께 DocumentClientException을 throw합니다. 이 경우 응용 프로그램의 오류 처리 루틴에서 재시도 동작 및 논리를 처리하는 방법 또는 컬렉션에 대해 예약된 처리량을 늘리는 방법을 고려해 볼 수 있습니다.

##다음 단계

Azure DocumentDB의 예약된 처리량에 대해 자세히 알아보려면 다음 리소스를 참조하세요.
 
- [DocumentDB 가격 책정](https://azure.microsoft.com/pricing/details/documentdb/)
- [DocumentDB 용량 관리](documentdb-manage.md) 
- [DocumentDB에서 데이터 모델링](documentdb-modeling-data.md)
- [DocumentDB 성능 수준](documentdb-partition-data.md)

DocumentDB에 대해 자세히 알아보려면 Azure DocumentDB [설명서](https://azure.microsoft.com/documentation/services/documentdb/)를 참조하세요.

DocumentDB를 사용하여 규모 및 성능 테스트를 시작하려면 [Azure DocumentDB를 사용한 성능 및 규모 테스트](documentdb-performance-testing.md)를 참조하세요.


[1]: ./media/documentdb-request-units/queryexplorer.png
[2]: ./media/documentdb-request-units/RUEstimatorUpload.png
[3]: ./media/documentdb-request-units/RUEstimatorDocuments.png
[4]: ./media/documentdb-request-units/RUEstimatorResults.png
[5]: ./media/documentdb-request-units/RUCalculator2.png

<!---HONumber=AcomDC_0601_2016-->