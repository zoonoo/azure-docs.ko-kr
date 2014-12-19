<properties title="Search Service: workflow for developers" pageTitle="검색 서비스: 개발자를 위한 워크플로" description="Search Service: workflow for developers" metaKeywords="" services="" solutions="" documentationCenter="" authors="Heidist" manager="mblythe" videoId="" scriptId="" />

<tags ms.service="azure-search" ms.devlang="" ms.workload="search" ms.topic="article"  ms.tgt_pltfrm="" ms.date="09/23/2014" ms.author="heidist" />

# Azure 검색: 개발 워크플로

[WACOM.INCLUDE [This article uses the Azure Preview portal](../includes/preview-portal-note.md)]

이 문서에서는 검색 서비스와 해당 인덱스를 만들고 유지 관리하기 위한 로드맵과 몇 가지 모범 사례를 제공합니다. 

서비스를 이미 프로비전했다고 가정합니다. 아직 하지 않았다면 자세한 내용은 [Azure 검색 시작](../search-get-started/)을 참조하세요.

+ [1단계: 인덱스 만들기](#sub-1)
+ [2단계: 문서 추가](#sub-2)
+ [3단계: 인덱스 쿼리](#sub-3)
+ [4단계: 인덱스 및 문서 업데이트 또는 삭제](#sub-4)
+ [저장소 설계 고려 사항](#sub-5)


<h2 id="sub-1">1단계: 인덱스 만들기</h2>

쿼리는 검색 데이터 및 특성을 포함하는 검색 인덱스를 대상으로 합니다. 이와 같이 서비스를 프로비전한 후 첫 번째 단계는 JSON 형식으로 인덱스 스키마를 정의하고 HTTPS PUT 요청을 실행하여 서비스에서 인덱스 만드는 것입니다. 

인덱스는 응용 프로그램 코드를 통해 생성됩니다. 사용자 인터페이스에서 인덱스를 정의하도록 도와주는 기본 제공 도구나 편집기는 없습니다. 인덱스를 생성하는 다양한 방법을 보여 주는 예제에는 Program.cs 파일에서 스키마를 지정하는 [Azure 검색을 사용하여 첫 번째 검색 솔루션 만들기](../search-create-first-solution/)와 독립 실행형 JSON 스키마 파일로 인덱스를 제공하는 [Azure 검색에서 점수 매기기 프로필 시작](../search-get-started-scoring-profiles)이 포함됩니다. 인덱스를 만드는 방법에 대한 자세한 내용은 MSDN에서 [인덱스 만들기(Azure 검색 API)](http://msdn.microsoft.com/ko-kr/library/dn798941.aspx)를 참조하세요.

<h2 id="sub-2">2단계: 문서 추가</h2>

검색 인덱스를 만든 후 문서를 JSON 형식으로 POST하여 인덱스에 추가할 수 있습니다. 각 문서에 고유 키와 검색 가능 및 검색 불가능 데이터가 포함된 필드 컬렉션이 있어야 합니다. 문서 데이터는 각 필드에 대한 키/값 쌍의 집합으로 표시됩니다.

처리량을 향상시키기 위해 문서를 일괄 처리로 추가하는 것이 좋습니다. 평균 문서 크기를 약 1-2KB로 가정할 경우 최대 1,000개까지 문서를 일괄 처리할 수 있습니다.

POST 요청에 대한 전체 상태 코드가 있습니다. 상태 코드는 HTTP 200(성공) 또는 성공한 문서와 실패한 문서가 함께 있을 경우 HTTP 207(다중 상태)입니다. POST 요청에 대한 상태 코드 외에도 Azure 검색은 각 문서의 상태 필드를 유지 관리합니다. 일괄 업로드를 감안할 경우 각 문서에 대한 삽입이 성공 또는 실패했는지를 나타내는 문서별 상태를 가져올 방법이 필요합니다. 상태 필드에서 해당 정보를 제공합니다. 문서를 로드하지 못한 경우 false로 설정됩니다.

부하가 크면 일부 업로드 실패가 발생하는 것이 일반적입니다. 이 경우 전체 상태 코드는 부분 성공을 나타내는 207이며, 인덱싱에 실패한 문서의 'status' 속성이 false로 설정됩니다.

> [WACOM.NOTE] 서비스가 문서를 받으면 인덱싱을 위해 대기되며, 검색 결과에 즉시 포함되지 않을 수도 있습니다. 부하가 크지 않은 경우 일반적으로 몇 초 내에 문서가 인덱싱됩니다.


<h2 id="sub-3">3단계: 인덱스 쿼리</h2>

문서가 인덱싱된 후 검색 쿼리를 실행할 수 있습니다. OData 또는 간단한 쿼리 구문을 사용하여 한 번에 하나씩 인덱스를 쿼리할 수 있습니다.

+	[Azure 검색의 OData 식 구문](http://msdn.microsoft.com/ko-kr/library/dn798921.aspx)
+	[Azure 검색의 간단한 쿼리 구문](http://msdn.microsoft.com/ko-kr/library/dn798920.aspx)

<h2 id="sub-4">4단계: 인덱스 및 문서 업데이트 또는 삭제</h2>

선택적으로 검색 인덱스의 스키마를 변경하고, 인덱스 내에서 문서를 업데이트 또는 삭제하고, 인덱스를 삭제할 수 있습니다.

인덱스를 업데이트할 때 여러 작업(삽입, 병합, 삭제)을 동일한 일괄 처리로 결합하여 여러 왕복 요구를 제거할 수 있습니다. 현재 Azure 검색은 부분 업데이트(HTTP PATCH)를 지원하지 않으므로 인덱스를 업데이트해야 하는 경우 인덱스 정의를 다시 전송해야 합니다.

<h2 id="sub-5">저장소 설계 고려 사항</h2>

Azure 검색은 검색 작업에 사용되는 인덱스 및 문서에 대해 내부 저장소를 사용합니다. 텍스트 분석 및 인덱스 구문 분석은 모든 검색 가능 필드 및 관련 특성을 사용할 수 있는지에 따라 달라집니다.

문서의 모든 필드가 검색 가능한 것은 아닙니다. 예를 들어 응용 프로그램이 음악 또는 동영상의 온라인 카탈로그인 경우 Azure BLOB 서비스 또는 다른 저장소 형식에 이진 파일을 저장하는 것이 좋습니다. 이진 파일 자체는 검색 가능하지 않으므로 Azure 검색 저장소에 저장할 필요가 없습니다. 이미지, 비디오 및 오디오 파일을 다른 서비스 또는 위치에 저장해야 하더라도 파일 위치에 대한 URL을 참조하는 필드를 포함해야 합니다. 이러한 방식으로 검색 결과의 일부로 외부 데이터를 반환할 수 있습니다. 

인덱스 또는 문서를 만드는 방법에 대한 자세한 내용은 [Azure 검색 REST API](http://msdn.microsoft.com/ko-kr/library/dn798935.aspx)를 참조하세요.


<!--Anchors-->
[1단계: 인덱스 만들기]: #sub-1
[2단계: 문서 추가]: #sub-2
[3단계: 인덱스 쿼리]: #sub-3
[4단계: 인덱스 및 문서 업데이트 또는 삭제]: #sub-4
[문서 저장소 선택]: #sub-5


<!--Image references-->

<!--Link references-->
[Azure 검색 시작]: ../search-get-started/
[Microsoft Azure에서 검색 서비스 관리]: ../search-manage/
[Azure 검색을 사용하는 첫 검색 솔루션 만들기]: ../search-create-first-solution/

