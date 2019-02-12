---
title: Azure Portal에서 AI 기반 인덱싱용 인식 검색 파이프라인 빌드 - Azure Search
description: Azure Portal에서 샘플 데이터를 사용하여 데이터 추출, 자연어 및 이미지 처리 기술을 보여주는 예제입니다.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: quickstart
ms.date: 01/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 191cff21cdaa6a4e94358ed0b9c63cd942f71a6e
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2019
ms.locfileid: "55564564"
---
# <a name="quickstart-create-a-cognitive-search-pipeline-using-skills-and-sample-data"></a>빠른 시작: 기술 및 샘플 데이터를 사용하여 Cognitive Search 파이프라인 만들기

인식 검색(미리 보기)은 Azure Search 인덱싱 파이프라인에 데이터 추출, NLP(자연어 처리) 및 이미지 처리 기술을 추가하여 검색할 수 없거나 구조화되지 않은 콘텐츠를 검색할 수 있게 만들어 줍니다. 

인식 검색 파이프라인은 [OCR](cognitive-search-skill-ocr.md), [언어 감지](cognitive-search-skill-language-detection.md), [엔터티 인식](cognitive-search-skill-entity-recognition.md) 등의 [Cognitive Services 리소스](https://azure.microsoft.com/services/cognitive-services/)를 인덱싱 프로세스에 통합합니다. Cognitive Services의 AI 알고리즘은 Azure Search를 기반으로 한 전체 텍스트 검색 솔루션에서 사용할 수 있는 구조 및 텍스트 콘텐츠를 반환하면서 원본 데이터에서 패턴, 기능 및 특징을 찾는 데 사용됩니다.

이 빠른 시작에서는 [Azure Portal](https://portal.azure.com)에서 첫 번째 보강 파이프라인을 만든 후 단일 코드 줄을 작성합니다.

> [!div class="checklist"]
> * Azure Blob Storage에서 샘플 데이터로 시작
> * 인식 인덱싱 및 보강에 대해 [**데이터 가져오기**](search-import-data-portal.md) 마법사 구성 
> * 마법사 실행(엔터티 기술이 사람, 위치 및 조직을 감지)
> * [**검색 탐색기**](search-explorer.md)를 사용하여 보강된 데이터 쿼리

## <a name="supported-regions"></a> 지원되는 지역

다음 지역에 만든 Azure Search 서비스에서 인식 검색을 시도할 수 있습니다.

* 미국 중서부
* 미국 중남부
* 미국 동부
* 미국 동부 2
* 미국 서부 2
* 캐나다 중부
* 서유럽
* 영국 남부
* 북유럽
* 브라질 남부
* 동남아시아
* 인도 중부
* 오스트레일리아 동부

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

> [!NOTE]
> 2018년 12월 21일부터 Cognitive Services 리소스를 Azure Search 기술과 연결할 수 있습니다. 이를 통해 Microsoft는 기술 실행 요금을 부과할 수 있습니다. 또한 문서 해독 단계의 일부로 이미지 추출에 대한 요금 청구가 이 날짜에서 시작됩니다. 문서에서의 텍스트 추출은 추가 비용 없이 계속 제공됩니다.
>
> 기본 제공 기술의 실행에 대한 요금은 기존 [Cognitive Services 종량제 가격](https://azure.microsoft.com/pricing/details/cognitive-services/)으로 청구됩니다. 이미지 추출 가격은 미리 보기 가격으로 책정되며 [Azure Search 가격 페이지](https://go.microsoft.com/fwlink/?linkid=2042400)에 설명되어 있습니다. [자세한 정보](cognitive-search-attach-cognitive-services.md)

## <a name="prerequisites"></a>필수 조건

["인식 검색이란?"](cognitive-search-concept-intro.md) 보강 아키텍처 및 구성 요소를 소개합니다. 

Azure 서비스는 이 시나리오에 단독으로 사용됩니다. 준비 과정에는 필요한 서비스 만들기가 포함되어 있습니다.

+ [Azure Blob 스토리지](https://azure.microsoft.com/services/storage/blobs/): 원본 데이터 제공
+ [Cognitive Services](https://azure.microsoft.com/services/cognitive-services/): 파이프라인을 지정하는 경우 이러한 리소스 인라인을 만들 수 있는 AI 제공
+ [Azure Search](https://azure.microsoft.com/services/search/): 사용자 지정 앱을 사용하기 위해 보강된 인덱싱 파이프라인 및 다양한 자유 형식 텍스트 검색 환경 제공

### <a name="set-up-azure-search"></a>Azure Search 설정

먼저 Azure Search 서비스에 등록합니다. 

1. [Azure Portal](https://portal.azure.com)로 이동한 후 Azure 계정을 사용하여 로그인합니다.

1. **리소스 만들기**를 클릭하고, Azure Search를 검색하고, **만들기**를 클릭합니다. 검색 서비스를 처음으로 설정하고 도움이 필요한 경우 [포털에서 Azure Search 서비스 만들기](search-create-service-portal.md)를 참조하세요.

  ![대시보드 포털](./media/cognitive-search-tutorial-blob/create-search-service-full-portal.png "포털에서 Azure Search 서비스 만들기")

1. 리소스 그룹에서는 이 빠른 시작에서 만드는 모든 리소스를 포함할 새 리소스 그룹을 만듭니다. 이렇게 하면 빠른 시작을 마친 후 보다 쉽게 리소스를 정리할 수 있습니다.

1. 위치의 경우 인식 검색에 대해 [지원되는 지역](#supported-regions) 중 하나를 선택합니다.

1. 가격 책정 계층으로는 자습서와 빠른 시작을 완료할 수 있는 **무료** 서비스를 만듭니다. 사용자 고유의 데이터를 사용하여 자세히 조사하려면 **기본** 또는 **표준** 같은 [유료 서비스](https://azure.microsoft.com/pricing/details/search/)를 만듭니다. 

  무료 서비스는 인덱스 3개, blob 크기 최대 16MB, 인덱싱 2분으로 제한되며, 이는 인식 검색의 전체 기능을 실행하기에는 부족합니다. 다른 계층의 제한에 대한 내용은 [서비스 제한](search-limits-quotas-capacity.md)을 참조하세요.

  ![포털의 서비스 정의 페이지](./media/cognitive-search-tutorial-blob/create-search-service2.png "포털의 서비스 정의 페이지")

  > [!NOTE]
  > 인식 검색은 현재 공개 미리 보기로 제공됩니다. 무료 계층을 포함한 모든 계층에서 기술 집합을 사용할 수 있습니다. 유료 Cognitive Services 리소스를 연관시키지 않고 제한된 수의 보강을 수행할 수 있습니다. [자세히](cognitive-search-attach-cognitive-services.md) 알아봅니다.

1. 서비스를 대시보드에 고정하면 서비스 정보에 빠르게 액세스할 수 있습니다.

  ![포털의 서비스 정의 페이지](./media/cognitive-search-tutorial-blob/create-search-service3.png "포털의 서비스 정의 페이지")

### <a name="set-up-azure-blob-service-and-load-sample-data"></a>Azure Blob service를 설정하고 샘플 데이터 로드

보강 파이프라인은 [Azure Search 인덱서](search-indexer-overview.md)가 지원하는 Azure 데이터 소스에서 데이터를 가져옵니다. Azure Table Storage에서는 Cognitive Search가 지원되지 않습니다. 이 연습에서는 Blob Storage를 사용하여 여러 가지 콘텐츠 형식을 보여줍니다.

1. 여러 종류의 작은 파일 집합으로 구성된 [샘플 데이터를 다운로드](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4)하세요. 

1. Azure Blob 스토리지에 가입하고, 스토리지 계정을 만들며, Blob 서비스 페이지를 열고 컨테이너를 만듭니다. 컨테이너에서 공용 액세스 수준을 **컨테이너**로 설정합니다. 자세한 내용은 *Search 비정형 데이터* 자습서에서 [“컨테이너 만들기” 섹션](../storage/blobs/storage-unstructured-search.md#create-a-container)을 참조하세요.

1. 만든 컨테이너에서 **업로드**를 클릭하여 이전 단계에서 다운로드한 샘플 파일을 업로드합니다.

  ![Azure Blob Storage의 원본 파일](./media/cognitive-search-quickstart-blob/sample-data.png)

## <a name="create-the-enrichment-pipeline"></a>보강 파이프라인 만들기

Azure Search 서비스 대시보드 페이지로 돌아가서 명령 모음에서 **데이터 가져오기**를 클릭하여 다음 네 단계를 통해 인식 보강을 설정합니다.

  ![데이터 가져오기 명령](media/cognitive-search-quickstart-blob/import-data-cmd2.png)

### <a name="step-1-create-a-data-source"></a>1단계: 데이터 소스 만들기

**데이터에 연결**에서 **Azure Blob 스토리지**를 선택하고 만든 계정 및 컨테이너를 선택합니다. 데이터 원본의 이름을 지정하고, 나머지는 기본값을 사용합니다. 

  ![Azure Blob 구성](./media/cognitive-search-quickstart-blob/blob-datasource.png)

다음 페이지를 계속합니다.

  ![인식 검색에 대한 다음 페이지 단추](media/cognitive-search-quickstart-blob/next-button-add-cog-search.png)

### <a name="step-2-add-cognitive-skills"></a>2단계: 인식 기술 추가

다음으로, 인덱싱 파이프라인에 보강 단계를 추가합니다. Cognitive Services 리소스가 없으면 매일 20개의 트랜잭션을 제공하는 체험 버전에 가입할 수 있습니다. 샘플 데이터는 14개의 파일로 구성되므로 이 마법사를 실행하는 경우 매일 할당량을 대부분 사용할 수 있습니다.

1. **Cognitive Services 연결**을 확장하여 Cognitive Services API에 리소스를 제공하는 옵션을 확인합니다. 이 자습서의 용도로 **무료** 리소스를 사용할 수 있습니다.

  ![Cognitive Services 연결](media/cognitive-search-quickstart-blob/cog-search-attach.png)

2. **보강 추가**를 확장하고 자연어 처리를 수행하는 기술을 선택합니다. 이 빠른 시작에서는 사람, 조직 및 위치에 대한 엔터티 인식을 선택합니다.

  ![Cognitive Services 연결](media/cognitive-search-quickstart-blob/skillset.png)

  포털에서는 OCR 처리 및 텍스트 분석을 위해 기본 제공 기술을 제공합니다. 포털에서 한 기술 집합은 한 원본 필드에 대해 작동합니다. 작은 대상처럼 보일 수 있지만, Azure BLOB의 경우 `content` 필드에 대부분의 BLOB 문서가 포함됩니다(예: Word 문서 또는 PowerPoint 데크). BLOB의 모든 콘텐츠가 이 필드에 있기 때문에 이 필드는 가장 이상적인 입력입니다.

3. 다음 페이지를 계속합니다.

  ![다음 페이지 사용자 지정 인덱스](media/cognitive-search-quickstart-blob/next-button-customize-index.png)

> [!NOTE]
> 자연어 처리 기술은 샘플 데이터 집합의 텍스트 콘텐츠에 대해 작동합니다. OCR 옵션을 선택하지 않았으므로 샘플 데이터 세트에서 발견된 JPEG 및 PNG 파일은 이 빠른 시작에서 처리되지 않습니다. 

### <a name="step-3-configure-the-index"></a>3단계: 인덱스 구성

마법사는 일반적으로 기본 인덱스를 유추할 수 있습니다. 이 단계에서는 생성된 인덱스 스키마를 확인하고 설정을 수정할 수 있습니다. 데모 Blob 데이터 세트용으로 만든 기본 인덱스는 다음과 같습니다.

이 빠른 시작에서 마법사는 기본값을 적절하게 설정합니다. 

+ 기본 이름은 데이터 원본 유형을 기반으로 하는 *azureblob-index*입니다. 

+ 기본 필드는 원본 소스 데이터 필드(`content`) 및 인식 파이프라인에 의해 생성된 출력 필드(`people`, `organizations` 및 `locations`)를 기반으로 합니다. 기본 데이터 형식은 메타데이터 및 데이터 샘플링에서 유추됩니다.

+ 기본 키는 *metadata_storage_path*(이 필드는 고유 값 포함)입니다.

+ 기본 특성은 이러한 필드에 대해 **조회 가능**하고 **검색 가능**합니다. **검색 가능**은 필드를 검색할 수 있다는 뜻입니다. **조회 가능**은 결과에 반환될 수 있다는 뜻입니다. 마법사는 사용자가 기술 세트를 통해 필드를 생성했기 때문에 이 필드를 조회 및 검색 가능하도록 하려고 한다고 가정합니다.

  ![인덱스 필드](media/cognitive-search-quickstart-blob/index-fields.png)

`content` 필드의 **조회 가능** 특성에서 취소선과 물음표를 확인합니다. 텍스트가 많은 Blob 문서의 경우, `content` 필드에는 대량의 파일이 포함되어 있으며, 수천 줄이 포함될 수 있습니다. 파일 내용을 클라이언트 코드에 전달해야 하는 경우 **조회 가능**이 선택되어 있는지 확인합니다. 그렇지 않은 경우 추출된 요소(`people`, `organizations` 및 `locations`)가 사용자 목적에 충분하면 `content`에서 이 특성을 선택 취소하는 것이 좋습니다.

필드를 **조회 가능**으로 표시한다고 해서 필드가 검색 결과에 *나타나야* 하는 것은 아닙니다. **$select** 쿼리 매개 변수로 포함할 필드를 지정하여 검색 결과의 구성을 정확하게 제어할 수 있습니다. `content`와 같이 텍스트가 많은 필드의 경우 **$select** 매개 변수는 애플리케이션의 사용자에게 관리 가능한 검색 결과를 제공하는 솔루션으로, 클라이언트 코드에서 **조회 가능** 특성을 통해 필요한 모든 정보에 액세스할 수 있도록 해줍니다.
  
다음 페이지를 계속합니다.

  ![다음 페이지 인덱서 만들기](media/cognitive-search-quickstart-blob/next-button-create-indexer.png)

### <a name="step-4-configure-the-indexer"></a>4단계: 인덱서 구성

인덱서는 인덱싱 프로세스를 구동하는 상위 수준 리소스입니다. 인덱서는 데이터 원본 이름, 대상 인덱스 및 실행 빈도를 지정합니다. **데이터 가져오기** 마법사의 최종 결과는 항상 반복적으로 실행할 수 있는 인덱서입니다.

**인덱서** 페이지에서 기본 이름을 적용하고 **한 번 실행** 예약 옵션을 사용하여 즉시 실행할 수 있습니다. 

  ![인덱서 정의](media/cognitive-search-quickstart-blob/indexer-def.png)

**제출**을 클릭하여 인덱서를 만드는 동시에 실행합니다.

## <a name="monitor-indexing"></a>인덱싱 모니터링

보강 단계는 일반적인 텍스트 기반 인덱싱보다 완료하는 데 오래 걸립니다. 마법사는 진행률을 추적할 수 있도록 개요 페이지에서 인덱서 목록을 열어야 합니다. 자체 탐색을 위해 개요 페이지로 이동하고 **인덱서**를 클릭합니다.

JPG 및 PNG 파일이 이미지 파일이므로 경고가 발생합니다. 따라서 이 파이프라인에서 OCR 기술을 생략했습니다. 또한 잘림 알림이 표시됩니다. Azure Search는 무료 계층에서 추출을 32,000자로 제한합니다.

  ![Azure 검색 알림](./media/cognitive-search-quickstart-blob/indexer-notification.png)

인덱싱 및 보강에 시간이 걸릴 수 있으므로 탐색 속도를 높일 수 있도록 소량의 데이터 집합을 권장합니다. 

## <a name="query-in-search-explorer"></a>Search 탐색기에서 쿼리

인덱스를 만든 후에는 인덱스에서 문서를 반환하는 쿼리를 제출할 수 있습니다. 포털에서 **Search 탐색기**를 사용하여 쿼리를 실행하고 결과를 봅니다. 

1. 검색 서비스 대시보드 페이지의 명령 모음에서 **Search 탐색기**를 클릭합니다.

1. 맨 위에서 **인덱스 변경**을 선택하고 앞에서 만든 인덱스를 선택합니다.

1. `search=Microsoft&searchFields=organizations`처럼 인덱스를 쿼리하는 검색 문자열을 입력합니다.

결과는 자세한 정보 표시가 가능하고 읽기 어려울 수 있는 JSON으로 반환되며, Azure BLOB에서 오는 대용량 문서인 경우에 특히 그렇습니다. 결과를 쉽게 검색할 수 없는 경우 문서 내에서 CTRL + F 키를 사용하여 검색합니다. 이 쿼리의 경우 JSON 내에서 특정 용어를 검색할 수 있습니다. 

CTRL + F 키는 특정 결과 집합에 문서가 몇 개나 있는지 확인하는 데 사용할 수도 있습니다. Azure BLOB의 경우 포털에서는 각 값이 문서에 고유한 "metadata_storage_path"를 키로 선택합니다. CTRL + F 키로 "metadata_storage_path"를 검색하여 문서 수를 가져옵니다. 

  ![Search 탐색기 예제](./media/cognitive-search-quickstart-blob/search-explorer.png)

## <a name="takeaways"></a>핵심 내용

이제 첫 번째 인식 보강 인덱싱 연습을 완료했습니다. 이 빠른 시작의 목적은 사용자가 고유의 데이터를 사용하여 인식 검색 솔루션의 프로토타입을 신속하게 만들 수 있도록 중요한 개념을 소개하고 마법사를 연습하는 것이었습니다.

여러분이 꼭 기억했으면 하는 주요 개념 중 하나는 Azure 데이터 원본에 대한 종속성입니다. 인식 검색 보강은 인덱서에 바인딩되고, 인덱서는 Azure와 소스로 한정됩니다. 이 빠른 시작에서는 Azure Blob Storage를 사용하지만 다른 Azure 데이터 원본도 가능합니다. 자세한 내용은 [Azure Search의 인덱서](search-indexer-overview.md)를 참조하세요.

또 다른 중요한 개념으로, 기술은 입력 필드에 대해 작동합니다. 포털에서 모든 기술에 대한 단일 원본 필드를 선택해야 합니다. 코드에서 입력은 다른 필드이거나 업스트림 기술의 출력일 수 있습니다.

 기술에 대한 입력은 인덱스의 출력 필드에 매핑됩니다. 내부적으로, 포털은 [주석](cognitive-search-concept-annotations-syntax.md)을 설정하고 [기술 집합](cognitive-search-defining-skillset.md)을 정의하여 작업 및 일반적인 흐름의 순서를 설정합니다. 이러한 단계는 포털에서 숨겨지지만, 코드 작성을 시할 때 이러한 개념이 중요합니다.

마지막으로, 인덱스를 쿼리하여 결과를 볼 수 있다는 것을 배웠습니다. 결국 Azure Search는 검색 가능한 인덱스를 제공하며, 이 인덱스는 [단순](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 또는 [완전히 확장된 쿼리 구문](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)을 사용하여 쿼리할 수 있습니다. 보강된 필드를 포함하는 인덱스는 다른 인덱스와 비슷합니다. 표준 또는 [사용자 지정 분석기](search-analyzers.md), [점수 매기기 프로필](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [동의어](search-synonyms.md), [패싯 필터](search-filters-facets.md), 지역 검색, 기타 Azure Search 기능을 통합하고 싶은 경우 그렇게 하시면 됩니다.

## <a name="clean-up-resources"></a>리소스 정리

탐색을 마친 후 정리하는 가장 빠른 방법은 Azure Search 서비스 및 Azure Blob service를 포함하고 있는 리소스 그룹을 삭제하는 것입니다.  

두 서비스를 동일한 그룹에 배치한 경우 리소스 그룹을 삭제하면 서비스와 이 연습에서 만들고 저장한 콘텐츠를 포함하여 리소스 그룹에 들어 있는 모든 것이 영구적으로 삭제됩니다. 포털에서 리소스 그룹 이름은 각 서비스의 개요 페이지에 있습니다.

## <a name="next-steps"></a>다음 단계

Cognitive Services 리소스를 프로비전하는 방법에 따라 다른 기술 및 원본 데이터 필드가 포함된 마법사를 다시 실행하여 인덱싱 및 보강을 실험할 수 있습니다. 단계를 반복하려면 인덱스 및 인덱서를 삭제한 다음, 새로운 선택 항목으로 인덱서를 다시 만듭니다.

+ **개요** > **인덱스**에서 이전에 만든 인덱스를 선택하고 **삭제**를 클릭합니다.

+ **개요**에서 **인덱서** 타일을 두 번 클릭합니다. 앞에서 만든 인덱서를 찾아 삭제합니다.

또는 앞에서 만든 샘플 데이터 및 서비스를 다시 사용하고, 그 다음 자습서에서 동일한 작업을 프로그래밍 방식으로 수행하는 방법을 알아보세요. 

> [!div class="nextstepaction"]
> [자습서: 인식 검색 REST API 알아보기](cognitive-search-tutorial-blob.md)
