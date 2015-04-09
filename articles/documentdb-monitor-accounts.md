<properties 
	pageTitle="DocumentDB 계정 모니터링 | Azure" 
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
	ms.date="01/13/2015" 
	ms.author="mimig"/>

#DocumentDB 계정 모니터링  

[Azure Preview 포털](https://portal.azure.com/)에서 DocumentDB 계정을 모니터링할 수 있습니다. 각 DocumentDB 계정에 대해 성능 메트릭(예: 요청 및 서버 오류)과 사용량 메트릭(예: 저장소 사용)을 사용할 수 있습니다.

## <a id="metrics"></a>  방법: DocumentDB 계정의 성능 메트릭 보기
1.	[Azure Preview 포털](https://portal.azure.com/)에서 **찾아보기**, **DocumentDB 계정**을 클릭하고 성능 메트릭을 보려는 DocumentDB 계정의 이름을 클릭합니다.
2.	**모니터링** 렌즈에서 기본적으로 다음을 확인할 수 있습니다.
	*	오늘의 총 요청 수
	*	오늘의 초당 평균 요청 수 
	
	![](./media/documentdb-monitor-accounts/madocdb1.png)


3.	**총 요청** 또는 **초당 평균 요청 수** 파트를 클릭하면 자세한 **메트릭** 블레이드가 열립니다.
4.	**메트릭** 블레이드는 선택된 메트릭에 대한 세부 정보를 보여 줍니다.  블레이드 맨 위에는 그래프가 있고, 그 아래에 평균, 최소값, 최대값 등 선택된 메트릭의 집계를 보여 주는 테이블이 있습니다.  또한 메트릭 블레이드는 현재 메트릭 블레이드에 표시되는 메트릭으로 정의 및 필터링된 경고 목록을 보여 줍니다. 이런 방법으로, 경고가 많은 경우 여기에 표시되는 관련성 높은 경고만 볼 수 있습니다.   

	![](./media/documentdb-monitor-accounts/madocdb2.png)


## <a id="custom"></a>DocumentDB 계정의 성능 메트릭 뷰 사용자 지정

1.	특정 파트에 표시되는 메트릭을 사용자 지정하려면 메트릭 차트를 마우스 오른쪽 단추로 클릭한 다음 **차트 편집**을 선택합니다.  
	![](./media/documentdb-monitor-accounts/madocdb3.png)

2.	**차트 편집** 블레이드에서 파트뿐 아니라 시간 간격에 표시되는 메트릭을 수정하는 옵션도 있습니다.  
	![](./media/documentdb-monitor-accounts/madocdb4.png)

3.	파트에 표시되는 메트릭을 변경하려면 사용 가능한 성능 메트릭을 선택/선택 취소한 다음, 블레이드의 아래쪽에 있는 **저장**을 클릭합니다.  
4.	시간 범위를 변경하려면 다른 범위(예: **지난 시간**)를 선택한 다음 블레이드의 아래쪽에 있는 **저장**을 클릭합니다.  

	![](./media/documentdb-monitor-accounts/madocdb5.png) 


## <a id="create"></a>병렬 성능 메트릭 차트 만들기
Azure Preview 포털에서 병렬 메트릭 차트를 만들 수 있습니다.  

1.	먼저, 복제 및 수정할 차트를 마우스 오른쪽 단추로 클릭하고 **사용자 지정**을 선택합니다. 

	![](./media/documentdb-monitor-accounts/madocdb6.png)

2.	메뉴에서 **복제**를 클릭하여 파트를 복사한 후 **사용자 지정 완료**를 클릭합니다. 

	![](./media/documentdb-monitor-accounts/madocdb7.png)  


이제 파트에 표시되는 메트릭 및 시간 범위를 사용자 지정하면서 이 파트를 다른 메트릭 파트로 처리할 수 있습니다.  이렇게 하면 두 가지 다른 메트릭 차트를 동시에 병렬로 볼 수 있습니다.  
	![](./media/documentdb-monitor-accounts/madocdb8.png)  

## <a id="view"></a>DocumentDB 계정의 사용량 메트릭 보기
1.	[Azure Preview 포털](https://portal.azure.com/)에서 **찾아보기**, **DocumentDB 계정**을 클릭하고 사용량 메트릭을 보려는 DocumentDB 계정의 이름을 클릭합니다.
2.	**사용량** 렌즈에서 기본적으로 다음을 확인할 수 있습니다.
	*	예상 비용.
	*	계정 내에서 소비되는 저장소.
	*	계정의 최대 사용 가능한 저장소.
	*	사용자 및 권한 사용량.
	*	CU(용량 단위) 할당.
	*	첨부 파일 사용량.

	![](./media/documentdb-monitor-accounts/madocdb9.png)
 
## <a id="setup"></a>DocumentDB 계정의 성능 메트릭 경고 설정
1.	[Azure Preview 포털](https://portal.azure.com/)에서 **찾아보기**, **DocumentDB 계정**을 클릭하고 성능 메트릭 경고를 설정할 DocumentDB 계정의 이름을 클릭합니다.
2.	**작업** 렌즈에서 **경고 규칙** 파트를 클릭합니다.  
	![](./media/documentdb-monitor-accounts/madocdb10.png)

3.	경고 규칙 블레이드에서 **경고 추가**를 클릭합니다.  
	![](./media/documentdb-monitor-accounts/madocdb11.png)

4.	**경고 규칙 추가** 블레이드에서 다음을 지정합니다.
	*	설정 중인 경고 규칙의 이름
	*	새 경고 규칙에 대한 설명
	*	경고 규칙에 대한 메트릭
	*	경고가 활성화되는 시기를 결정하는 조건, 임계값 및 기간. 예를 들어 서버 오류가 지난 15분 동안 5개보다 많습니다.
	*	경고가 발생할 때 서비스 관리자 및 공동 관리자에게 메일을 보낼지 여부
	*	경고 알림에 대한 추가 메일 주소  
	![](./media/documentdb-monitor-accounts/madocdb12.png)

 
## <a id="next"></a>다음 단계
DocumentDB 용량 및 성능에 대한 자세한 내용은 [DocumentDB 용량 및 성능 관리](documentdb-manage.md)를 참조하세요. 

<!--Anchors-->
[DocumentDB 계정의 성능 메트릭을 보는 방법]: #How-to-view-performance-metrics-for-a-DocumentDB-account
[DocumentDB 계정의 성능 메트릭 뷰 사용자 지정]: #Customize-performance-metric-views-for-a-DocumentDB-account
[병렬 성능 메트릭을 만드는 방법]: #How-to-create-side-by-side-performance-metric-charts
[DocumentDB 계정의 사용량 메트릭을 보는 방법]: #How-to-view-usage-metrics-for-a-DocumentDB-account
[DocumentDB 계정에 대해 성능 메트릭 경로를 설정하는 방법]: #How-to-setup-performance-metric-alerts-for-a-DocumentDB-account
[다음 단계]: #Next-steps

<!--HONumber=49-->