<properties pageTitle="Azure 검색 "자주 함께 구매됨" 추천 | Microsoft Azure | Apache Mahout | Azure 기계 학습" description="Apache Mahout 또는 Azure 기계 학습을 사용하는 Azure 검색 응용 프로그램에 자주 함께 구매됨, 신규 제품 또는 함께 구매함 등의 추천을 추가하기 위한 샘플 코드" services="검색" documentationCenter="" authors="liamca" manager="pablocas" editor="" tags="기계 학습"/>

<tags
   ms.service="search"
   ms.devlang="dotnet"
   ms.workload="search"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.date="11/12/2015"
   ms.author="liamca"/>

# Azure 검색 "자주 함께 구매됨" 추천

온라인으로 쇼핑을 하는 경우 아마도 추가로 고려하고 있는 상품이나 서비스를 나타내는 '자주 함께 구매됨', '이 항목을 구매한 고객이 함께 구매함' 또는 '신규 제품' 목록을 보았을 것입니다. 일반적으로 이 데이터는 많은 고객의 구매 기록과 각자에게 고유한 소비자 선호도 데이터를 기반으로 온라인 업체에 의해 컴파일됩니다. 검색을 통해 관련된 소비자 지향 추천을 제공하는 기능은 상업용 응용 프로그램은 물론 다중 미디어 콘텐츠 또는 정보를 제공하는 사이트에 추가되어 강력한 효과를 발휘합니다.

Azure 검색 응용 프로그램에서 데이터의 패턴 및 관계 식별에 사용하는 데이터 및 서비스를 가져오는 일부 추가 코드를 작성하여 추천을 구현한 다음 사용자와 관련된 추천으로 변환할 수 있습니다.

[이 샘플은 방법을 설명하기](https://github.com/liamca/azure-search-recommendations) 위해 추천 엔진에 대한 [Apache Mahout]()을 사용합니다.

이 문서의 나머지 부분에서는 영화 대여에 대한 샘플 데이터를 사용하여 '자주 함께 구매됨' 추천을 추가하는 샘플 코드를 살펴보겠습니다.

![1](./media/search-fbt-recommendations/product_recommendations.png)

## 추천이란?

*추천*은 항목을 추천하고 변환을 개선하는 데 사용하는 기존의 사용자 활동(예: 웹 로그) 검색을 기반으로 카탈로그의 더 많은 항목을 표시하는 기술입니다.

추천 엔진은 과거 고객 활동을 사용하거나 디지털 저장소에서 직접 데이터를 수집하여 학습됩니다. 이 샘플에서는 Apache Mahout을 사용하여 추천 데이터를 컴파일합니다.

일반적인 추천은 다음과 같습니다. - 자주 함께 구매됨: 이 항목을 구매한 고객이 함께 구매함 - 고객을 위한 항목 추천: 비슷한 고객이 함께 구매함

## Azure 검색 인덱스 만들기

- AzureSearchMovieRecommendations.sln 솔루션을 열고 ImportAzureSearchIndexData를 기본 프로젝트로 설정합니다.  
- ImportAzureSearchIndexData 내에서 Program.cs를 열고 SearchServiceName 및 SearchApiKey를 Azure 검색 서비스를 가리키도록 변경합니다.
- http://grouplens.org/datasets/hetrec-2011/에서 hetrec2011-movielens-2k.zip을 다운로드하고 \\ImportAzureSearchIndexData\\data에 파일 Movies.dat 및 user\_ratedmovies.dat를 복사합니다.
- 인덱스를 만들고 영화 데이터를 로드할 프로젝트를 실행합니다. 
- 마지막으로 응용 프로그램이 테스트 검색을 실행합니다.

## 영화를 검색할 간단한 HTML 응용 프로그램 만들기

Azure 검색 인덱스를 쿼리할 수 있는 전체 JavaScript 웹 응용 프로그램은 \\WebSite\\starter-template-complete에 있습니다.

처음부터 데모를 단계별로 수행하려는 경우 원본 CSS는 \\WebSite\\starter-template에서 사용할 수 있습니다.

\\WebSite\\starter-template-complete 내에서 search.js 파일을 열고 apiKey 및 azureSearchService를 Azure 검색 서비스 세부 정보를 사용하여 업데이트합니다.

이제 검색 상자에 입력하여 영화를 보기 위해 Chrome과 같은 브라우저에서 이 파일을 열 수 있어야 합니다.

## Mahout을 사용하여 추천 사항 만들기를 실행하는 명령

- Azure Blob 저장소에 data\\movie\_usage.txt 파일을 업로드합니다. 
- HDInsight 인스턴스(원격 데스크톱 사용)를 만들고 원격 데스크톱(Azure 포털에서 사용 가능)을 통해 컴퓨터에 연결합니다.
- HDInsight 컴퓨터에서 "Hadoop 명령줄"을 엽니다.
- C:\\apps\\dist의 Mahout bin 디렉터리로 변경합니다. 이 디렉터리는 C:\\apps\\dist\\mahout-1.0.0.2.3.3.0-2992\\bin일 수 있지만 이보다 최신 버전의 Mahout을 얻을 수 있습니다.
- [CONTAINER] 및 [STORAGEACT]를 Azure 저장소 세부 사항(movie\_usage.txt 파일 위치)으로 바꾸는 다음 명령줄을 실행합니다.

    mahout itemsimilarity -s SIMILARITY\_COSINE --input "wasb://[CONTAINER]@[STORAGEACT].blob.core.windows.net/movie\_usage.txt" --output "wasb://[CONTAINER]@[STORAGEACT].blob.core.windows.net/output/" --tempDir "wasb://[CONTAINER]@[STORAGEACT].blob.core.windows.net/temp" -m 5

이 작업은 몇 분만에 완료되지만 완료될 때 저장소 컨테이너에 영화 추천이 포함된 /movies/output/part-r-00000 파일이 있어야 합니다.

이 파일에는 [Item ID of Movie], [Item ID of Recommendations related to this Movie], [Similarity Percentage] 열이 있습니다.

## Mahout에서 Azure 검색으로 데이터 가져오기

Azure 검색 인덱스를 만든 프로그램이 `Recommendations`라고 하는 필드도 만들었습니다. 이는 컬렉션 유형(쉼표로 구분된 문자열 집합)입니다. 이 Azure 검색 인덱스를 사용하여 이전 단계에서 만든 데이터를 병합합니다.

- Visual Studio 솔루션 AzureSearchMovieRecommendations.sln에서 MahoutOutputLoader의 Program.cs를 엽니다.
- Azure 검색 서비스 세부 정보를 사용하여 SearchServiceName 및 SearchApiKey 업데이트
- Mahout 제품 추천 파일에 저장한 Azure 저장소 계정 세부 정보를 사용하여 StorageApiKey 및 StorageAccountName 업데이트
- 응용 프로그램을 실행하여 데이터 병합
 
## 추천 시각화
이때 웹 응용 프로그램으로 돌아가서 임의 영화를 클릭하여 추천을 확인할 수 있어야 합니다.

이 이미지를 클릭했을 때 추천이 반환된 방법을 보려면 Search.js를 열고 openMovieDetails() 함수를 살펴봅니다.

## 크레딧

데이터는 GroupLens(http://grouplens.org/datasets/hetrec-2011/)에서 제공했습니다.

이 데이터의 라이선싱에 대한 자세한 내용은 이 http://files.grouplens.org/datasets/hetrec2011/hetrec2011-movielens-readme.txt 페이지를 참조하세요.

<!---HONumber=Nov15_HO4-->