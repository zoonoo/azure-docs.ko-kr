<properties 
	pageTitle="Azure 검색에서 점수 매기기 프로필을 사용하는 방법 | Microsoft Azure | 호스트된 클라우드 검색 서비스" 
	description="Microsoft Azure에서 호스팅되는 클라우드 검색 서비스인 Azure 검색을 사용하여 점수 매기기 프로필을 통해 검색 순위를 조정합니다." 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="06/04/2016" 
	ms.author="heidist"/>

# Azure 검색에서 점수 매기기 프로필을 사용하는 방법

점수 매기기 프로필은 검색 결과 목록에서 항목 순위를 지정하는 방식에 영향을 미치는 검색 점수 계산을 사용자 지정하는 Microsoft Azure 검색 기능입니다. 점수 매기기 프로필은 미리 정의된 조건을 충족하는 항목의 순위를 높이는 모델 관련성 방식으로 생각할 수 있습니다. 예를 들어, 응용 프로그램이 온라인 호텔 예약 사이트라고 가정합니다. `location` 필드의 순위를 높이면 시애틀 같은 용어를 포함하는 검색에서는 `location` 필드에 시애틀을 포함하는 항목의 점수가 높아집니다. 점수 매기기 프로필이 두 개 이상 있거나, 응용 프로그램에 대해 기본 점수 매기기로 충분할 경우 프로필이 없을 수도 있습니다.

점수 매기기 프로필을 실험할 수 있도록 점수 매기기 프로필을 사용하여 검색 결과의 순위를 변경하는 샘플 응용 프로그램을 다운로드할 수 있습니다. 샘플은 콘솔 응용 프로그램으로, 실제 응용 프로그램 개발을 위한 매우 현실적인 응용 프로그램은 아니지만 학습 도구로는 유용합니다.

샘플 응용 프로그램에서는 `musicstoreindex`라는 가상 데이터를 사용하여 점수 매기기 동작을 보여 줍니다. 샘플 앱은 간단하므로 손쉽게 점수 매기기 프로필 및 쿼리를 수정하고 프로그램이 실행될 때 순위에 대한 즉각적인 효과를 확인할 수 있습니다.

<a id="sub-1"></a>
## 필수 조건

샘플 응용 프로그램은 Visual Studio 2013을 사용하여 C#으로 작성됩니다. 기존에 사용 중인 Visual Studio가 없다면 무료 [Visual Studio 2013 Express Edition](http://www.visualstudio.com/products/visual-studio-express-vs.aspx)을 사용해 보세요.

자습서를 완료하려면 Azure 구독 및 Azure 검색 서비스가 필요합니다. 서비스 설정에 대한 도움말은 [포털에서 검색 서비스 만들기](search-create-service-portal.md)를 참조하세요.

[AZURE.INCLUDE [이 자습서를 완료하려면 Azure 계정이 있어야 합니다.](../../includes/free-trial-note.md)]

<a id="sub-2"></a>
## 샘플 응용 프로그램 다운로드

이 자습서에 설명된 샘플 응용 프로그램을 다운로드하려면 codeplex의 [Azure 검색 점수 매기기 프로필 데모](https://azuresearchscoringprofiles.codeplex.com/)로 이동합니다.

소스 탭에서**다운로드**를 클릭하여 솔루션 zip 파일을 다운로드하세요.

 ![][12]

<a id="sub-3"></a>
## app.config 편집

1. 파일 압축을 푼 후 Visual Studio에서 솔루션을 열어 구성 파일을 편집합니다.
1. 솔루션 탐색기에서 **app.config**를 두 번 클릭합니다. 이 파일은 서비스 끝점 및 요청을 인증하는 데 사용되는 `api-key`를 지정합니다. 클래식 포털에서 이러한 값을 가져올 수 있습니다.
1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
1. Azure 검색에 대한 서비스 대시보드로 이동합니다.
1. **속성** 타일을 클릭하여 서비스 URL을 복사합니다.
1. **키** 타일을 클릭하여 `api-key`를 복사합니다.

URL 및 `api-key`를 app.config에 추가하고 나면 응용 프로그램 설정이 다음과 같이 표시됩니다.

   ![][11]


<a id="sub-4"></a>
## 응용 프로그램 살펴보기

앱을 빌드하고 실행할 준비가 거의 되었지만 수행하기 전에 인덱스를 채우는 데 사용되는 JSON 파일을 살펴보시기 바랍니다.

**Schema.json**에서는 이 데모에서 강조하는 점수 매기기 프로필을 포함하여 인덱스를 정의합니다. 스키마는 점수 매기기 프로필에서 사용할 수 있는 검색할 수 없는 필드(`margin` 등)를 포함하여 인덱스에서 사용되는 모든 필드를 정의합니다. 점수 매기기 프로필 구문은 [Azure 검색 인덱스에 점수 매기기 프로필 추가](http://msdn.microsoft.com/library/azure/dn798928.aspx)에서 설명합니다.

**Data1-3.json**에서는 몇 가지 장르에 걸쳐 246개 앨범 데이터를 제공합니다. 데이터는 검색 작업을 설명하는 데 사용되는 `price` 및 `margin` 같은 가상 필드를 사용한 실제 앨범과 아티스트 정보의 조합입니다. 데이터 파일은 인덱스를 따르고 Azure 검색 서비스에 업로드됩니다. 데이터가 업로드 및 인덱싱되고 나면 데이터에 대해 쿼리를 실행할 수 있습니다.

**Program.cs**는 다음 작업을 수행합니다.

- 콘솔 창을 엽니다.

- 서비스 URL 및 `api-key`를 사용하여 Azure 검색 서비스에 연결합니다.

- 있는 경우 `musicstoreindex`을 삭제합니다.

- schema.json 파일을 사용하여 새 `musicstoreindex`를 만듭니다.

- 데이터 파일을 사용하여 인덱스를 채웁니다.

- 4개의 쿼리를 사용하여 인덱스를 쿼리합니다. 점수 매기기 프로필은 쿼리 매개 변수로 지정됩니다. 모든 쿼리는 같은 용어인 'best'를 검색합니다. 첫 번째 쿼리는 기본 점수 매기기를 보여 줍니다. 나머지 3개 쿼리는 점수 매기기 프로필을 사용합니다.

<a id="sub-5"></a>
## 응용 프로그램 빌드 및 실행

연결 또는 어셈블리 참조 문제를 방지하려면 먼저 제대로 작동하는 데 문제가 없는지 확인하기 위한 응용 프로그램을 빌드하고 실행합니다. 콘솔 응용 프로그램이 백그라운드에서 열려 있는지 확인해야 합니다. 모든 4개의 쿼리는 일시 중지 없이 순서대로 실행됩니다. 대부분 시스템에서 전체 프로그램은 15초 이내로 실행됩니다. 콘솔 응용 프로그램에 "Complete. Press enter to continue”라는 메시지가 표시되면 프로그램이 성공적으로 완료된 것입니다.

쿼리 실행을 비교하려면 콘솔에서 쿼리 결과를 표시 및 복사하고 Excel 파일에 붙여넣을 수 있습니다.

다음 그림에서는 처음 3개 쿼리의 결과를 나란히 보여 줍니다. 모든 쿼리는 많은 앨범 제목에 나타나는 같은 검색어 'best'를 사용합니다.

   ![][10]

첫 번째 쿼리는 기본 점수 매기기를 사용합니다. 검색어는 앨범 제목에만 나타나고 다른 조건이 지정되지 않았으므로 앨범 제목에 'best'가 포함된 항목이 검색 서비스에서 항목을 찾은 순서대로 반환됩니다.

두 번째 쿼리는 점수 매기기 프로필을 사용하지만 프로필은 아무 영향도 주지 않습니다. 결과는 첫 번째 쿼리의 결과와 똑같습니다. 이는 점수 매기기 프로필이 쿼리와 밀접한 관련이 없는 필드('genre')의 순위를 높이기 때문입니다. 문서의 'genre' 필드에 검색어 'best'가 없습니다. 점수 매기기 프로필이 아무 효과가 없으면 결과가 기본 점수 매기기와 같습니다.

세 번째 쿼리는 점수 매기기 프로필이 미친 영향의 첫 번째 증거입니다. 같은 앨범 집합을 사용하고 있으므로 검색어는 계속 'best'이지만, 점수 매기기 프로필이 'rating' 및 'last-updated' 순위를 높이므로 몇몇 항목이 목록에서 더 높이 올라갑니다.

다음 그림은 'margin'에 의해 순위가 높아진 네 번째 및 마지막 쿼리를 보여 줍니다. 'margin' 필드는 검색할 수 없고 검색 결과로 반환될 수 없습니다. 수익이 더 높은 항목이 검색 결과 목록에서 더 높이 표시된다는 점을 설명하도록 도와주는 'margin' 값이 스프레드시트에 수동으로 추가되었습니다.

   ![][9]

이제 점수 매기기 프로필을 실험했으므로 다른 쿼리 구문, 점수 매기기 프로필 또는 풍부한 데이터를 사용하도록 프로그램을 변경해 보겠습니다. 자세한 내용은 다음 섹션의 링크를 참조하세요.

<a id="next-steps"></a>
## 다음 단계

점수 매기기 프로필에 대해 알아봅니다. 자세한 내용은 [Azure 검색 인덱스에 점수 매기기 프로필 추가](http://msdn.microsoft.com/library/azure/dn798928.aspx)를 참조하세요.

검색 구문 및 쿼리 매개 변수에 대해 알아봅니다. 자세한 내용은 [문서 검색(Azure 검색 REST API)](http://msdn.microsoft.com/library/azure/dn798927.aspx)을 참조하세요.

한 걸음 물러나 인덱스 만들기에 대해 자세히 알아볼까요? 기본 사항을 이해하려면 [이 비디오를 보세요](http://channel9.msdn.com/Shows/Cloud+Cover/Cloud-Cover-152-Azure-Search-with-Liam-Cavanagh).

<!--Anchors-->
[Prerequisites]: #sub-1
[Download the sample application]: #sub-2
[Edit app.config]: #sub-3
[Explore the application]: #sub-4
[Build and run the application]: #sub-5
[Next steps]: #next-steps

<!--Image references-->
[12]: ./media/search-get-started-scoring-profiles/AzureSearch_CodeplexDownload.PNG
[11]: ./media/search-get-started-scoring-profiles/AzureSearch_Scoring_AppConfig.PNG
[10]: ./media/search-get-started-scoring-profiles/AzureSearch_XLSX1.PNG
[9]: ./media/search-get-started-scoring-profiles/AzureSearch_XLSX2.PNG

<!---HONumber=AcomDC_0608_2016-->