<properties
	pageTitle="Azure 포털을 사용하여 DocumentDB 계정 모니터링 | Microsoft Azure"
	description="성능 메트릭(예: 요청 및 서버 오류) 및 사용 현황 메트릭(예: 저장소 사용량)에 대해 DocumentDB 계정을 모니터링하는 방법을 알아봅니다."
	services="documentdb"
	documentationCenter=""
	authors="mimig1"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/10/2016"
	ms.author="mimig"/>

# Azure 포털에서 DocumentDB 계정 사용 모니터링

[Microsoft Azure 포털](https://portal.azure.com/)에서 DocumentDB 계정을 모니터링할 수 있습니다. 각 DocumentDB 계정에 대해 성능 메트릭(예: 요청 및 서버 오류)과 사용량 메트릭(예: 저장소 사용)을 사용할 수 있습니다.

## DocumentDB 계정의 성능 메트릭 보기
1.	[Azure 포털](https://portal.azure.com/)에서 **찾아보기**, **DocumentDB 계정**을 클릭하고 성능 메트릭을 보려는 DocumentDB 계정의 이름을 클릭합니다.
2.	**모니터링** 렌즈에서 기본적으로 다음을 확인할 수 있습니다.
	*	오늘의 총 요청 수
	*	계정의 저장소 사용

	테이블에 **사용 가능한 데이터 없음**이 표시되는 경우 [문제 해결](#troubleshooting) 섹션을 참조하세요.

	![오늘의 총 요청과 저장소 사용을 보여 주는 모니터링 렌즈의 스크린샷](./media/documentdb-monitor-accounts/documentdb-total-requests-and-usage.png)


3.	**총 요청** 또는 **저장소** 타일을 클릭하면 자세한 **메트릭** 블레이드가 열립니다.
4.	**메트릭** 블레이드는 선택한 메트릭에 대한 세부 정보를 보여 줍니다. 블레이드 맨 위에는 그래프가 있고, 그 아래에 평균, 최소값, 최대값, 합계 등 선택된 메트릭의 집계 값을 보여 주는 테이블이 있습니다. 또한 메트릭 블레이드는 현재 메트릭 블레이드에 표시되는 메트릭으로 정의 및 필터링된 경고 목록을 보여 줍니다. 이런 방법으로, 경고가 많은 경우 여기에 표시되는 관련성 높은 경고만 볼 수 있습니다.   

	![메트릭 블레이드의 스크린샷](./media/documentdb-monitor-accounts/documentdb-metric-blade.png)


## DocumentDB 계정의 성능 메트릭 뷰 사용자 지정

1.	특정 파트에 표시되는 메트릭을 사용자 지정하려면 메트릭 차트를 마우스 오른쪽 단추로 클릭한 다음 **차트 편집**을 선택합니다. ![차트 편집 단추가 강조 표시된 총 요청 차트의 스크린샷](./media/documentdb-monitor-accounts/madocdb3.png)

2.	**차트 편집** 블레이드에서 파트뿐 아니라 시간 간격에 표시되는 메트릭을 수정하는 옵션도 있습니다. ![차트 편집 블레이드의 스크린샷](./media/documentdb-monitor-accounts/madocdb4.png)

3.	파트에 표시되는 메트릭을 변경하려면 사용 가능한 성능 메트릭을 선택/선택 취소한 다음 블레이드의 아래쪽에 있는 **저장**을 클릭합니다.
4.	시간 범위를 변경하려면 다른 범위(예: **사용자 지정**)를 선택한 다음 블레이드의 아래쪽에 있는 **저장**을 클릭합니다.  

	![사용자 지정 시간 범위를 입력하는 방법을 보여 주는 차트 편집 블레이드 시간 범위 파트의 스크린샷](./media/documentdb-monitor-accounts/madocdb5.png)


## 병렬 성능 메트릭 차트 만들기
Azure 포털에서 병렬 메트릭 차트를 만들 수 있습니다.

1.	먼저, 복제 및 수정할 차트를 마우스 오른쪽 단추로 클릭하고 **사용자 지정**을 선택합니다.

	![사용자 지정 옵션이 강조 표시된 총 요청 차트의 스크린샷](./media/documentdb-monitor-accounts/madocdb6.png)

2.	메뉴에서 **복제**를 클릭하여 파트를 복사한 후 **사용자 지정 완료**를 클릭합니다.

	![복제 및 사용자 지정 완료 옵션이 강조 표시된 총 요청 차트의 스크린샷](./media/documentdb-monitor-accounts/madocdb7.png)


이제 파트에 표시되는 메트릭 및 시간 범위를 사용자 지정하면서 이 파트를 다른 메트릭 파트로 처리할 수 있습니다. 이렇게 하면 두 가지 다른 메트릭 차트를 동시에 병렬로 볼 수 있습니다. ![총 요청 차트 및 지난 1시간의 새로운 총 요청 차트의 스크린샷](./media/documentdb-monitor-accounts/madocdb8.png)

## DocumentDB 계정의 성능 메트릭 경고 설정
1.	[Azure 포털](https://portal.azure.com/)에서 **모두 찾아보기**, **DocumentDB 계정**을 클릭하고 성능 메트릭 경고를 설정할 DocumentDB 계정의 이름을 클릭합니다.

2.	**모든 설정** 블레이드가 열려 있지 않은 경우 왼쪽 상단의 **설정** 명령을 클릭하여 블레이드를 엽니다. ![데이터베이스 계정 블레이드의 설정 명령 스크린샷](./media/documentdb-monitor-accounts/madocdb10.png)

3.	**모든 설정** 블레이드에서 **경고 규칙**을 클릭하여 경고 규칙 블레이드를 엽니다. ![경고 규칙 부분이 선택된 스크린샷](./media/documentdb-monitor-accounts/madocdb10.5.png)

4.	경고 규칙 블레이드에서 **경고 추가**를 클릭합니다. ![경고 추가 단추가 강조 표시된 경고 규칙 블레이드의 스크린샷](./media/documentdb-monitor-accounts/madocdb11.png)

5.	**경고 규칙 추가** 블레이드에서 다음을 지정합니다.
	*	설정 중인 경고 규칙의 이름
	*	새 경고 규칙에 대한 설명
	*	경고 규칙에 대한 메트릭
	*	경고가 활성화되는 시기를 결정하는 조건, 임계값 및 기간. 예를 들어 서버 오류가 지난 15분 동안 5개보다 많습니다.
	*	경고가 발생할 때 서비스 관리자 및 공동 관리자에게 메일을 보낼지 여부
	*	경고 알림에 대한 추가 메일 주소 ![경고 규칙 추가 블레이드의 스크린샷](./media/documentdb-monitor-accounts/madocdb12.png)

## DocumentDB를 모니터링하는 다른 방법
포털에서 제공되는 계정 수준 메트릭(예: 계정 저장소 사용 및 총 요청)은 DocumentDB API를 통해 사용할 수 없습니다. 그러나 컬렉션 수준에서 사용 데이터를 검색할 수 있습니다. 컬렉션 수준 데이터를 검색하려면 다음을 수행합니다.

- REST API를 사용하려면 [컬렉션에 대해 GET을 수행](https://msdn.microsoft.com/library/mt489073.aspx)합니다. 컬렉션에 대한 할당량 및 사용량 정보는 응답의 x-ms-resource-quota 및 x-ms-resource-usage 헤더에서 반환됩니다.
- .NET SDK를 사용하려면 [DocumentClient.ReadDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync.aspx) 메서드를 사용합니다. 이 메서드는 **CollectionSizeUsage**, **DatabaseUsage**, **DocumentUsage** 등의 여러 사용량 속성이 포함된 [ResourceResponse](https://msdn.microsoft.com/library/dn799209.aspx)를 반환합니다.

## 문제 해결
최근에 데이터를 요청하거나 데이터베이스에 데이터를 추가한 경우 모니터링 타일에 **사용 가능한 데이터 없음**이 표시되면 최근 사용을 반영하도록 이 타일을 편집할 수 있습니다.

### 현재 데이터를 새로 고치도록 타일 편집
1.	특정 파트에 표시되는 메트릭을 사용자 지정하려면 메트릭 차트를 마우스 오른쪽 단추로 클릭한 다음 **차트 편집**을 선택합니다. ![사용 가능한 데이터가 없음을 나타내는 총 요청 타일의 스크린샷](./media/documentdb-monitor-accounts/documentdb-no-available-data.png)

2.	**차트 편집** 블레이드의 **시간 범위** 섹션에서 **지난 시간**, **저장**을 차례로 클릭합니다. ![지난 시간이 선택된 차트 편집 블레이드의 스크린샷](./media/documentdb-monitor-accounts/documentdb-no-available-data-past-hour.png)


3.	이제 현재 데이터 및 사용 현황이 표시되도록 타일이 새로 고쳐집니다. ![업데이트된 총 요청 지난 시간 타일의 스크린샷](./media/documentdb-monitor-accounts/documentdb-no-available-data-fixed.png)

## 다음 단계
DocumentDB에 대한 자세한 내용은 [DocumentDB 용량 관리](documentdb-manage.md)를 참조하세요.

<!---HONumber=AcomDC_0316_2016-->