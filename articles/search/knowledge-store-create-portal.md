---
title: Azure Portal에서 지식 저장소 만들기 - Azure Search
description: Azure Portal에서 데이터 가져오기 마법사를 사용하여 인지 검색 파이프라인의 보강을 유지하기 위한 Azure Search 지식 저장소를 만듭니다.
author: lisaleib
services: search
ms.service: search
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: v-lilei
ms.openlocfilehash: fb979a7ff4144694aecad0985c5bce9be2de05bd
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71265207"
---
# <a name="create-an-azure-search-knowledge-store-in-the-azure-portal"></a>Azure Portal에서 Azure Search 지식 저장소 만들기

> [!Note]
> 지식 저장소는 미리 보기로 있으므로 프로덕션 환경에서 사용하면 안 됩니다. 이 기능은 [Azure Search REST API 버전 2019-05-06-Preview](search-api-preview.md)에서 제공됩니다. 지금은 .NET SDK 지원이 없습니다.
>

지식 저장소는 나중에 분석하거나 다른 다운스트림을 처리하기 위해 AI 보강 파이프라인의 출력을 유지하는 Azure Search의 기능입니다. AI 보강 파이프라인은 이미지 파일 또는 비정형 텍스트 파일을 받고, Azure Search를 사용하여 인덱싱하고, Cognitive Services의 AI 보강(예: 이미지 분석 및 자연어 처리)을 적용한 다음, 결과를 Azure 스토리지의 지식 저장소에 저장합니다. 그런 다음, Power BI 또는 Storage Explorer와 같은 도구를 사용하여 지식 저장소를 검색할 수 있습니다.

이 문서에서는 Azure Portal의 데이터 가져오기 마법사를 사용하여 AI 보강을 호텔 리뷰 세트에 수집, 인덱싱 및 적용합니다. 호텔 리뷰는 Azure Blog Storage로 가져오고, 결과는 지식 저장소로 Azure Table Storage에 저장됩니다.

지식 저장소가 만들어지면 Storage Explorer 또는 Power BI를 사용하여 이 지식 저장소에 액세스하는 방법을 알아볼 수 있습니다.

## <a name="prerequisites"></a>필수 조건

+ [Azure Search 서비스를 만들거나](search-create-service-portal.md) 현재 구독에서 [기존 서비스를 찾습니다](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). 이 자습서에서는 체험 서비스를 사용할 수 있습니다.

+ 샘플 데이터 및 지식 저장소를 저장하기 위한 [Azure 스토리지 계정을 만듭니다](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account). 스토리지 계정은 Azure Search 서비스와 동일한 위치(예: US-WE)를 사용해야 하며, *계정 종류*는 *StorageV2(범용 V2)* (기본값) 또는 *Storage(범용 V1)* 여야 합니다.

## <a name="load-the-data"></a>데이터 로드

Azure Search 인덱서에서 액세스하고 AI 보강 파이프라인을 통해 공급할 수 있도록 호텔 리뷰 CSV 파일을 Azure Blob 스토리지에 로드합니다.

### <a name="create-an-azure-blob-container-with-the-data"></a>데이터를 사용하여 Azure Blob 컨테이너 만들기

1. [CSV 파일(HotelReviews_Free.csv)에 저장된 호텔 리뷰 데이터를 다운로드합니다](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). 이 데이터는 Kaggle.com에서 가져온 것이며 호텔에 대한 고객 피드백을 포함하고 있습니다.
1. [Azure Portal에 로그인](https://portal.azure.com)하고, Azure 스토리지 계정으로 이동합니다.
1. [Blob 컨테이너를 만듭니다](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal). 이렇게 하려면 스토리지 계정의 왼쪽 탐색 모음에서 **Blob**을 클릭한 다음, 명령 모음에서 **+ 컨테이너**를 클릭합니다.
1. 새 컨테이너 **이름**에 대해 `hotel-reviews`를 입력합니다.
1. **퍼블릭 액세스 수준**을 선택합니다. 기본값을 사용했습니다.
1. **확인**을 클릭하여 Azure Blob 컨테이너를 만듭니다.
1. 새 `hotels-review` 컨테이너를 열고, **업로드**를 클릭하고, 첫 번째 단계에서 다운로드한 **HotelReviews-Free.csv** 파일을 선택합니다.

    ![데이터 업로드](media/knowledge-store-create-portal/upload-command-bar.png "호텔 리뷰 업로드")

1. **업로드**를 클릭하여 CSV 파일을 Azure Blob Storage로 가져옵니다. 새 컨테이너가 표시됩니다.

    ![Azure Blob 컨테이너 만들기](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "Azure Blob 컨테이너 만들기")

### <a name="get-the-azure-storage-account-connection-string"></a>Azure Storage 계정 연결 문자열 가져오기

1. 포털에서 Azure Storage 계정으로 이동합니다.
1. 서비스의 왼쪽 탐색 영역에서 **액세스 키**를 클릭합니다.
1. **키 1** 아래에서 *연결 문자열*을 복사하여 저장합니다. 문자열은 `DefaultEndpointsProtocol=https`로 시작합니다. 스토리지 계정 이름과 키는 문자열에 포함되어 있습니다. 이 문자열을 편리하게 유지합니다. 이는 이후 단계에서 필요합니다.

## <a name="create-and-run-ai-enrichments"></a>AI 보강 만들기 및 실행

데이터 가져오기 마법사를 사용하여 지식 저장소를 만듭니다. 데이터 원본을 만들고, 보강을 선택하고, 지식 저장소와 인덱스를 구성한 다음, 실행합니다.

### <a name="start-the-import-data-wizard"></a>데이터 가져오기 마법사 시작

1. Azure Portal에서 [검색 서비스를 찾습니다](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).

1. 명령 모음에서 **데이터 가져오기**를 클릭하여 가져오기 마법사를 시작합니다.

### <a name="connect-to-your-data-import-data-wizard"></a>데이터에 연결(데이터 가져오기 마법사)

이 마법사 단계에서는 호텔 데이터를 사용하여 Azure Blob에서 데이터 원본을 만듭니다.

1. **데이터 원본** 목록에서 **Azure Blob Storage**를 선택합니다.
1. **이름**에 대해 `hotel-reviews-ds`를 입력합니다.
1. **구문 분석 모드**에 대해 **분리된 텍스트**를 선택한 다음, **첫 줄에 헤더 포함** 확인란을 선택합니다. **구분 기호 문자**가 쉼표(,)인지 확인합니다.
1. 이전 단계에서 저장한 스토리지 서비스 **연결 문자열**을 입력합니다.
1. **컨테이너 이름**에 대해 `hotel-reviews`를 입력합니다.
1. **다음: 인지 검색 추가(선택 사항)** 를 클릭합니다.

      ![데이터 원본 개체 만들기](media/knowledge-store-create-portal/hotel-reviews-ds.png "데이터 원본 개체 만들기")

## <a name="add-cognitive-search-import-data-wizard"></a>인지 검색 추가(데이터 가져오기 마법사)

이 마법사 단계에서는 인지 기술 보강을 사용하여 기술 세트를 만듭니다. 이 샘플에서 사용하는 기술은 핵심 구를 추출하고 언어와 감정을 감지합니다. 이러한 보강은 Azure 테이블로 지식 저장소에 "프로젝션"됩니다.

1. **Cognitive Services 연결** 섹션을 펼칩니다. **무료(제한적 보강)** 가 기본적으로 선택되어 있습니다. HotelReviews-Free.csv의 레코드 수가 19개이고 이 무료 리소스에서 하루 최대 20개의 트랜잭션을 허용하므로 이 리소스를 사용할 수 있습니다.
1. **보강 추가**를 펼칩니다.
1. **기술 세트 이름**에 대해 `hotel-reviews-ss`를 입력합니다.
1. **원본 데이터 필드**에 대해 **reviews_text*를 선택합니다.
1. **보강 세분성 수준**에 대해 **페이지(5,000자 청크)** 를 선택합니다.
1. 다음 인지 기술을 선택합니다.
    + **핵심 구 추출**
    + **언어 감지**
    + **감정 감지**

      ![기술 세트 만들기](media/knowledge-store-create-portal/hotel-reviews-ss.png "기술 세트 만들기")

1. **지식 저장소에 보강 저장**을 펼칩니다.
1. 이전 단계에서 저장한 **스토리지 계정 연결 문자열**을 입력합니다.
1. 다음 **Azure 테이블 프로젝션**을 선택합니다.
    + **문서**
    + **페이지**
    + **핵심 구**

    ![지식 저장소 구성](media/knowledge-store-create-portal/hotel-reviews-ks.png "지식 저장소 구성")

1. **다음: 대상 인덱스 사용자 지정**을 클릭합니다.

### <a name="import-data-import-data-wizard"></a>데이터 가져오기(데이터 가져오기 마법사)

이 마법사 단계에서는 선택적 전체 텍스트 검색 쿼리에 대한 인덱스를 구성합니다. 마법사는 데이터 원본을 샘플링하여 필드와 데이터 형식을 유추합니다. 원하는 동작의 특성만 선택하면 됩니다. 예를 들어 **조회 가능** 특성을 사용하면 검색 서비스에서 필드 값을 반환할 수 있지만, **검색 가능**을 사용하면 필드에서 전체 텍스트를 검색할 수 있습니다.

1. **인덱스 이름**에 대해 `hotel-reviews-idx`를 입력합니다.
1. 특성에 대해 다음과 같이 선택합니다.
    + 모든 필드에 대해 **조회 가능**을 선택합니다.
    + 다음 필드에 대해 **필터링 가능**, **패싯 가능**을 선택합니다. *Sentiment*, *Language*, *Keyphrases*
    + *city*, *name*, *reviews_text*, *language*, *Keyphrases* 필드에 대해 **검색 가능**을 선택합니다.

    인덱스는 다음 이미지와 비슷합니다. 긴 목록이므로 일부 필드가 이미지에 표시되지 않습니다.

    ![인덱스 구성](media/knowledge-store-create-portal/hotel-reviews-idx.png "인덱스 구성")

1. **다음: 인덱서 만들기**를 클릭합니다.

### <a name="create-an-indexer"></a>인덱서 만들기

이 마법사 단계에서는 이전 마법사 단계에서 정의한 데이터 원본, 기술 세트 및 인덱스를 함께 가져오는 인덱서를 구성합니다.

1. **이름**에 `hotel-reviews-idxr`를 입력합니다.
1. **일정**에 대해 **한 번**(기본값)을 유지합니다.
1. **제출**을 클릭하여 인덱서를 실행합니다. 데이터 추출, 인덱싱, 인지 기술 적용은 모두 이 단계에서 수행됩니다.

### <a name="monitor-the-notifications-queue-for-status"></a>알림 큐의 상태 모니터링

1. Azure Portal에서 클릭 가능한 **Azure Search 알림** 상태 링크에 대한 알림 활동 로그를 모니터링합니다. 실행을 완료하는 데 몇 분 정도 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

이제 인지 서비스를 사용하여 데이터를 보강하고 결과를 지식 저장소에 프로젝션했으므로 Storage Explorer 또는 Power BI를 사용하여 보강된 데이터 세트를 검색할 수 있습니다.

Storage Explorer를 사용하여 이 지식 저장소를 검색하는 방법을 알아보려면 다음 연습을 참조하세요.

> [!div class="nextstepaction"]
> [Storage Explorer를 사용하여 보기](knowledge-store-view-storage-explorer.md)

이 지식 저장소를 Power BI에 연결하는 방법을 알아보려면 다음 연습을 참조하세요.

> [!div class="nextstepaction"]
> [Power BI와 연결](knowledge-store-connect-power-bi.md)

이 연습을 반복하거나 다른 AI 보강 연습을 수행하려면 *hotel-reviews-idxr* 인덱서를 삭제합니다. 인덱서를 삭제하면 사용 가능한 일별 트랜잭션 카운터가 0으로 다시 설정됩니다.
