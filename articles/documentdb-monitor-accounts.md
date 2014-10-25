<properties title="Monitor a DocumentDB Account" pageTitle="Monitor a DocumentDB account | Azure" description="Learn how to monitor your DocumentDB account for performance metrics (such as requests and server errors) and usage metrics (such as storage consumption)." metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, monitor, accounts" services="documentdb" solutions="data-management" documentationCenter=""  authors="bradsev" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="brradsev"></tags>

# DocumentDB 계정 모니터링

[Azure 미리 보기 포털][Azure 미리 보기 포털]에서 DocumentDB 계정을 모니터링할 수 있습니다. 각 DocumentDB 계정에 대해 성능 메트릭(예: 요청 및 서버 오류)과 사용량 메트릭(예: 저장소 사용)을 사용할 수 있습니다.

## 이 문서에서는 다음을 수행합니다.

-   [방법: DocumentDB 계정의 성능 메트릭 보기][방법: DocumentDB 계정의 성능 메트릭 보기]
-   [방법: DocumentDB 계정의 성능 메트릭 뷰 사용자 지정][방법: DocumentDB 계정의 성능 메트릭 뷰 사용자 지정]
-   [방법: 병렬 성능 메트릭 차트 만들기][방법: 병렬 성능 메트릭 차트 만들기]
-   [방법: DocumentDB 계정의 사용량 메트릭 보기][방법: DocumentDB 계정의 사용량 메트릭 보기]
-   [방법: DocumentDB 계정의 성능 메트릭 경고 설정][방법: DocumentDB 계정의 성능 메트릭 경고 설정]
-   [다음 단계][다음 단계]

## <span id="metrics"></span></a>방법: DocumentDB 계정의 성능 메트릭 보기

1.  [Azure 미리 보기 포털][Azure 미리 보기 포털]에서, **찾아보기**, **DocumentDB 계정**, 성능 메트릭을 볼 DocumentDB 계정의 이름을 차례로 클릭합니다.
2.  **모니터링** 렌즈에서 기본적으로 다음을 확인할 수 있습니다.

    -   오늘의 총 요청 수
    -   오늘의 초당 평균 요청 수

    ![][]

3.  **총 요청 또는 초당 평균 요청 수** 파트를 클릭하면 자세한 **메트릭** 블레이드가 열립니다.
4.  메트릭 블레이드는 선택된 메트릭에 대한 세부 정보를 보여 줍니다. 블레이드 맨 위에는 그래프가 있고, 그 아래에 평균, 최소값, 최대값 등 선택된 메트릭의 집계를 보여 주는 테이블이 있습니다. 또한 메트릭 블레이드는 현재 메트릭 블레이드에 표시되는 메트릭으로 정의 및 필터링된 경고 목록을 보여 줍니다. 이런 방법으로, 경고가 많은 경우 여기에 표시되는 관련성 높은 경고만 볼 수 있습니다.

    ![][1]

## <span id="custom"></span></a>방법: DocumentDB 계정의 성능 메트릭 뷰 사용자 지정

1.  특정 파트에 표시되는 메트릭을 사용자 지정하려면 메트릭 차트를 마우스 오른쪽 단추로 클릭한 다음 **차트 편집**을 선택합니다.
    ![][2]

2.  **차트 편집** 블레이드에서 파트뿐 아니라 시간 간격에 표시되는 메트릭을 수정하는 옵션도 있습니다.
    ![][3]

3.  파트에 표시되는 메트릭을 변경하려면 사용 가능한 성능 메트릭을 선택/선택 취소한 다음, 블레이드의 아래쪽에 있는 **저장**을 클릭합니다.
4.  시간 범위를 변경하려면 다른 범위(예: **지난 시간**)를 선택한 다음, 블레이드의 아래쪽에 있는 **저장**을 클릭합니다.
    ![][4]

5.  사용자 지정 시간 범위를 통해 지난 2주간에 걸친 기간을 임의로 선택할 수 있습니다.
6.  **저장**을 클릭하면 DocumentDB 계정 블레이드에서 나갈 때까지 변경 내용이 유지됩니다. 나중에 돌아오면 원래 메트릭 및 시간 범위가 표시됩니다.

## <span id="create"></span></a>방법: 병렬 차트 만들기

Azure 미리 보기 포털에서 병렬 메트릭 차트를 만들 수 있습니다.

1.  먼저, 시작할 차트를 마우스 오른쪽 단추로 클릭하고 **사용자 지정**을 선택합니다.
    ![][5]

2.  메뉴에서 **클론**을 클릭하여 해당 파트를 복사합니다.

    ![][6]

3.  마지막으로, 화면 맨 위의 도구 모음에서 **완료**를 클릭합니다. 이제 파트에 표시되는 메트릭 및 시간 범위를 사용자 지정하면서 이 파트를 다른 메트릭 파트로 처리할 수 있습니다. 그렇게 하면 두 가지 다른 메트릭 차트를 동시에 병렬로 볼 수 있습니다.
    ![][7]

> Azure 미리 보기 포털에서 떠나면 차트 시간 범위 및 선택한 메트릭이 파트의 기본값으로 다시 설정됩니다.

## <span id="view"></span></a>방법: DocumentDB 계정의 사용량 메트릭 보기

1.  [Azure 미리 보기 포털][Azure 미리 보기 포털]에서, **찾아보기**, **DocumentDB 계정**, 사용량 메트릭을 볼 DocumentDB 계정의 이름을 차례로 클릭합니다.
2.  **사용량** 렌즈에서 기본적으로 다음을 확인할 수 있습니다.

    -   계정에서 사용된 저장소
    -   계정의 사용 가능한 최대 저장소
    -   첨부 파일 사용량
    -   사용자 및 사용 권한 사용
    -   용량 단위 할당
    -   계정의 첨부 파일 사용량
        ![][8]

## <span id="setup"></span></a>방법: DocumentDB 계정의 성능 메트릭 경고 설정

1.  [Azure 미리 보기 포털][Azure 미리 보기 포털]에서, **찾아보기**, **DocumentDB 계정**, 성능 메트릭 경고를 설정할 DocumentDB 계정의 이름을 차례로 클릭합니다.
2.  **작업** 렌즈에서 **경고 규칙** 파트를 클릭합니다.
    ![][9]

3.  경고 규칙 블레이드에서 **경고 추가**를 클릭합니다.
    ![][10]

4.  **경고 규칙 추가** 블레이드에서 다음을 지정합니다.
	*	설정 중인 경고 규칙의 이름
	*	새 경고 규칙에 대한 설명
	*	경고 규칙에 대한 메트릭
	*	경고가 활성화되는 시기를 결정하는 조건, 임계값 및 기간. 예를 들어 서버 오류가 지난 15분 동안 5개보다 많습니다.
	*	경고가 발생할 때 서비스 관리자 및 공동 관리자에게 메일을 보낼지 여부
	*	경고 알림에 대한 추가 메일 주소  
	![][11]


## <span id="next"></span></a>다음 단계

-   DocumentDB에 대해 자세히 알아보려면 [azure.com][azure.com]에서 Azure DocumentDB 설명서를 참조하세요.

<!--Anchors-->

  [Azure 미리 보기 포털]: https://portal.azure.com/
  [방법: DocumentDB 계정의 성능 메트릭 보기]: #metrics
  [방법: DocumentDB 계정의 성능 메트릭 뷰 사용자 지정]: #custom
  [방법: 병렬 성능 메트릭 차트 만들기]: #create
  [방법: DocumentDB 계정의 사용량 메트릭 보기]: #view
  [방법: DocumentDB 계정의 성능 메트릭 경고 설정]: #setup
  [다음 단계]: #next
  []: http://i.imgur.com/y7pigTT.png
  [1]: http://i.imgur.com/6rBNPBL.png
  [2]: http://i.imgur.com/tRZEHk1.png
  [3]: http://i.imgur.com/G4UTi5U.png
  [4]: ./media/documentdb-monitor-accounts/madocdb5.png
  [5]: http://i.imgur.com/vLXWftF.png
  [6]: ./media/documentdb-monitor-accounts/madocdb7.png
  [7]: ./media/documentdb-monitor-accounts/madocdb8.png
  [8]: http://i.imgur.com/sL5inOu.png
  [9]: ./media/documentdb-monitor-accounts/madocdb10.png
  [10]: ./media/documentdb-monitor-accounts/madocdb11.png
  [11]: http://i.imgur.com/Inra4Po.png
  [azure.com]: http://go.microsoft.com/fwlink/p/?LinkID=402319
