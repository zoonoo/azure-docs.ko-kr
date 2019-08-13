---
title: Power BI를 사용하여 지식 저장소에 연결 - Azure Search
description: Azure Portal에서 데이터 가져오기 마법사를 사용하여 지식 저장소를 만든 다음, 분석 및 검색을 위해 Power BI에 연결합니다.
author: heidisteen
services: search
ms.service: search
ms.subservice: cognitive-search
ms.devlang: NA
ms.topic: tutorial
ms.date: 07/30/2019
ms.author: heidist
ms.openlocfilehash: 518a96ae8ace5c9630d594fe70487635b6ec1d2c
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840846"
---
# <a name="create-an-azure-search-knowledge-store-and-connect-using-power-bi"></a>Azure Search 지식 저장소를 만들고 Power BI를 사용하여 연결

> [!Note]
> 지식 저장소는 미리 보기로 있으므로 프로덕션 환경에서 사용하면 안 됩니다. 이 기능은 [Azure Search REST API 버전 2019-05-06-Preview](search-api-preview.md)에서 제공됩니다. 지금은 .NET SDK 지원이 없습니다.
>

지식 저장소는 후속 분석 또는 기타 다운스트림 처리를 위해 AI 보강 파이프라인의 출력을 유지하는 Azure Search의 기능입니다. AI 보강 파이프라인은 지원되는 데이터 원본에서 이미지 파일 또는 비정형 텍스트 파일을 받아서 Azure Search 인덱싱으로 보내고, Cognitive Services(이미지 분석 및 자연어 처리 등)의 AI 보강을 적용한 다음, 결과를 Azure 스토리지의 지식 저장소에 저장합니다. 지식 저장소에서 Power BI 또는 Storage Explorer와 같은 도구를 연결하여 결과를 검색할 수 있습니다.

이 문서에서는 지식 저장소를 포털에 만든 다음, Power BI Desktop의 파워 쿼리를 사용하여 연결하고 검색합니다. 

이 연습에서는 고객 리뷰 및 등급, Cognitive Services의 감정 점수, 파워 쿼리로 구성된 데이터 세트를 사용하여 문서를 쿼리합니다. 이 데이터는 Kaggle.com의 호텔 리뷰 데이터에서 비롯되었습니다. 

## <a name="prerequisites"></a>필수 조건

+ [호텔 리뷰 CSV 파일(HotelReviews_Free.csv)을 다운로드합니다](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). 이 데이터 세트에는 호텔에 대한 고객 의견 기록이 포함되어 있습니다.

+ [Power BI Desktop](https://powerbi.microsoft.com/downloads/)

+ [Azure Search](search-create-service-portal.md). 이 연습에서는 체험 서비스를 사용할 수 있습니다. 

+ [Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal). 계정이 "범용"(기본값인 *StorageV2(범용 V2)* 또는 *Storage(범용 V1)* )인지 확인합니다. Azure Search와 동일한 지역을 선택합니다.
 
## <a name="prepare-data"></a>데이터 준비 

Azure Search 인덱서에서 액세스할 수 있도록 .csv 파일을 Azure Blob 스토리지에 로드합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 Azure Storage 계정으로 이동한 후 **Blobs**를 클릭하고 **+ 컨테이너**를 클릭합니다.

1. 샘플 데이터가 포함되도록 [Blob 컨테이너를 만듭니다](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal). 

   1. 컨테이너 이름을 `hotel-reviews`로 지정합니다. 
   
   1. 유효한 값에 대한 공용 액세스 수준을 설정합니다. 기본값을 사용했습니다.

1. 컨테이너를 만들었으면 연 다음, 명령 모음에서 **업로드**를 선택합니다.

1. **HotelReviews-Free.csv**가 포함된 폴더로 이동하여 파일을 선택하고, **업로드**를 클릭합니다.

   ![.csv 파일 업로드](media/knowledge-store-howto-powerbi/hotel-reviews-blob-container.png ".csv 파일 업로드")

1. Azure 스토리지에 있는 동안 연결 문자열 및 컨테이너 이름을 가져옵니다.  데이터 원본 개체를 만들 때 다음 두 문자열이 모두 필요합니다.

   1. 개요 페이지에서 **액세스 키**를 클릭하고 *연결 문자열*을 복사합니다. `DefaultEndpointsProtocol=https;`로 시작하고 `EndpointSuffix=core.windows.net`으로 끝납니다. 사용자 계정 이름 및 키가 사이에 있습니다. 

   1. 컨테이너 이름은 `hotel-reviews` 또는 할당한 이름이어야 합니다. 

## <a name="create-and-run-ai-enrichments"></a>AI 보강 만들기 및 실행

데이터 가져오기 마법사를 사용하여 지식 저장소를 만듭니다. 데이터 세트를 가져오고, 보강을 선택하고, 지식 저장소와 인덱스를 구성한 다음, 실행합니다.

1. Azure Portal에서 [검색 서비스를 찾습니다](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).

1. 명령 모음에서 **데이터 가져오기**를 클릭합니다.

1. 마법사의 첫 번째 페이지에서 데이터 원본 개체를 만듭니다.

   - **Azure Blob Storage**를 선택합니다.

   - 데이터 원본 이름(예: *hotel-reviews-ds*)을 지정합니다.

   - 데이터가 .csv이므로 구문 분석 모드에 대해 **분리된 텍스트**를 선택하고, **첫 줄에 헤더 포함**을 선택하고, 구분 기호 문자가 쉼표인지 확인합니다.

   - Azure Storage 계정에 대한 연결 문자열은 포털의 **액세스 키** 아래에서 가져올 수 있습니다.

   - 컨테이너 이름은 포털의 Azure Storage Blob 목록에서도 가져올 수 있습니다.

      ![데이터 원본 개체 만들기](media/knowledge-store-howto-powerbi/hotel-reviews-ds.png "데이터 원본 개체 만들기")

1. 마법사의 두 번째 페이지에서 보강을 추가하고 지식 저장소를 구성합니다.

   - **Cognitive Services 연결**에서 하루 최대 20개의 트랜잭션을 허용하는 Free 리소스를 사용할 수 있습니다. HotelReviews-Free.csv의 레코드 수는 20개 미만입니다.

   - **보강 추가**에서 기술 세트의 이름을 *hotel-reviews-ss*로 지정하고, *reviews_text* 필드를 선택하고, *페이지(5,000자 청크)* 의 세분성 수준을 선택한 다음, *핵심 구 추출*, *언어 검색*, *감정 검색*의 세 가지 인지 기술을 선택합니다.

      ![기술 세트 만들기](media/knowledge-store-howto-powerbi/hotel-reviews-ss.png "기술 세트 만들기")

   - **지식 저장소에 보강 저장**에서 범용 Azure Storage 계정에 대한 연결 문자열을 제공합니다. 이 섹션에서 *Azure Table 프로젝트* 옵션을 선택하면 지식 저장소가 Azure Table 스토리지에 만들어집니다.

      ![지식 저장소 구성](media/knowledge-store-howto-powerbi/hotel-reviews-ks.png "지식 저장소 구성")
   
   **다음: 대상 인덱스 사용자 지정**을 클릭하여 다음 단계로 계속 진행합니다.

1. Azure Search에서 선택적 전체 텍스트 검색 쿼리에 대한 인덱스를 구성합니다. 이 연습에서는 Azure Table 스토리지에 연결된 Power BI에 집중하지만, **데이터 가져오기** 마법사에서도 Azure Search의 전체 텍스트 검색에 사용되는 인덱스를 만들 수도 있습니다. 

   마법사는 데이터 원본을 샘플링하여 필드 및 데이터 형식을 유추하므로 원하는 동작을 수행하는 데 필요한 특성만 선택하면 됩니다. 예를 들어 *조회 가능*은 서비스에서 필드 콘텐츠를 검색할 수 있음을 의미하고, *검색 가능*은 선택한 필드에 대해 전체 텍스트 검색을 사용하도록 설정합니다.

   - 인덱스 이름(예: *hotel-reviews-idx*)을 지정합니다.
   - 모든 필드를 **조회 가능**으로 설정합니다.
   - *city*, *name*, *reviews_text*, *language*, *keyphrases*를 **검색 가능**으로 설정합니다.
   - *sentiment*, *language*, *keyphrases*를 **필터링 가능** 및 **패싯 가능**으로 설정합니다. 
   
    인덱스는 다음 이미지와 비슷합니다.

     ![인덱스 구성](media/knowledge-store-howto-powerbi/hotel-reviews-idx.png "인덱스 구성")

   **다음: 인덱서 만들기**를 클릭하여 다음 단계로 계속 진행합니다.

1. 이름과 실행 케이던스를 지정하여 인덱서를 구성합니다. 이 연습에서는 *hotel-reviews-idxr*을 인덱서 이름으로 사용하고, 기본적인 **한 번** 예약을 사용하여 인덱서를 즉시 실행합니다.

   인덱서 실행은 이전 구성을 모두 이동시킵니다. 추출, 처리 및 수집은 모두 여기의 이 단계에서 수행됩니다.

1. 포털에서 알림 큐의 인덱싱을 모니터링합니다. 실행을 완료하는 데 몇 분 정도 걸립니다.

## <a name="connect-with-power-bi"></a>Power BI로 연결

1. Power BI Desktop을 시작하고, **데이터 가져오기**를 선택합니다.

1. [데이터 가져오기]에서 **Azure**, **Azure Table Storage**를 차례로 선택합니다. **연결**을 클릭합니다.

1. [계정 이름] 또는 [URL]에서 Azure Storage 계정 이름을 붙여넣습니다(전체 URL이 확인됨).

1. 계정 키를 입력합니다.

1. Document, KeyPhrases 및 Pages를 선택합니다. 이러한 테이블은 지식 저장소 구성에서 동일한 이름의 항목을 선택할 때 데이터 가져오기 마법사에서 만든 테이블입니다. **로드**를 클릭합니다.

1. **쿼리 편집** 명령을 클릭하여 파워 쿼리를 엽니다.

   ![파워 쿼리 열기](media/knowledge-store-howto-powerbi/powerbi-edit-queries.png "파워 쿼리 열기")

1. Document에 대해 다음을 수행합니다.

   - Azure Table 스토리지에서 만든 PartitionKey, RowKey 및 Timestamp 열을 제거합니다. 지식 저장소는 이 분석에 사용되는 관계를 제공합니다.

   - 콘텐츠 열을 확장합니다.

     ![테이블 편집](media/knowledge-store-howto-powerbi/powerbi-edit-table.png "테이블 편집")

1. 모든 필드를 선택한 다음, "metadata"로 시작하는 필드를 선택 취소합니다.

1. 각 열에서 ABC-123 아이콘을 사용하여 다음 열에 대한 데이터 형식을 수정합니다.

   - Date(날짜) 열은 **날짜/시간**이어야 합니다.
   - *Latitude*(위도)는 **10진수**여야 합니다.
   - *Longitude*(경도)는 **10진수**여야 합니다.

1. KeyPhrases에 대해 이전 단계를 반복하고, PartitionKey 및 기타 열을 제거하고, 콘텐츠 열을 확장하고, *SentimentScore*를 **10진수**로 설정합니다.

1. Pages에 대해 다시 반복하고, PartitionKey 및 기타 열을 제거하고, 콘텐츠 열을 확장합니다. 이 테이블의 데이터 형식은 수정할 필요가 없습니다.

1. 파워 쿼리 명령 모음의 왼쪽 끝에서 **닫기 및 적용**을 클릭합니다.

1. 지식 저장소에서 데이터 내에 만든 관계를 Power BI에서 인식하는지 확인합니다. 왼쪽 탐색 창에서 관계 타일을 클릭합니다. 세 테이블이 모두 관련되어야 합니다. 관계를 편집하고 "교차 필터 방향"이 둘 다로 설정되어 있는지 확인합니다. 이렇게 하면 필터를 적용할 때 모든 시각적 개체가 새로 고쳐집니다.

   ![관계 유효성 검사](media/knowledge-store-howto-powerbi/powerbi-relationships.png "관계 유효성 검사")

## <a name="try-with-larger-data-sets"></a>더 큰 데이터 세트를 사용하여 시도

데모 연습에 대한 요금을 방지하기 위해 의도적으로 데이터 세트를 작게 유지했습니다. 더 현실적으로 경험하기 위해 청구 가능한 Cognitive Services 리소스를 만들고 연결하여 감정 분석기, 핵심 구 추출 및 언어 검색기 기술에 대해 더 많은 수의 트랜잭션을 수행할 수 있습니다.

새 컨테이너를 Azure Blob 스토리지에 만들고, 각 CSV 파일을 자체 컨테이너에 업로드합니다. 데이터 가져오기 마법사의 데이터 원본 만들기 단계에서 이러한 컨테이너 중 하나를 지정합니다.

| 설명 | 링크 |
|-------------|------|
| 무료 계층   | [HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| 작음(500개 레코드) | [HotelReviews_Small.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Small.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| 중간(6,000개 레코드)| [HotelReviews_Medium.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Medium.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D)
| 큼(전체 데이터 세트, 35,000개 레코드) | [HotelReviews_Large.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Large.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). 매우 큰 데이터 세트를 처리하는 경우 비용이 많이 듭니다. 이 비용은 약 1,000달러입니다.|

더 큰 데이터 세트를 사용하려면 마법사의 보강 단계에서 Azure Search와 동일한 지역의 *S0* 계층에서 만든 청구 가능한 [Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) 리소스를 연결합니다. 

  ![Cognitive Services 리소스 만들기](media/knowledge-store-howto-powerbi/create-cognitive-service.png "Cognitive Services 리소스 만들기")

## <a name="next-steps"></a>다음 단계

이 연습을 반복하거나 다른 AI 보강 연습을 수행하려면 방금 만든 *hotel-reviews-idx* 인덱서를 삭제합니다. 인덱서를 삭제하면 사용 가능한 일별 트랜잭션 카운터가 0으로 다시 설정됩니다. 

지식 저장소를 시연하는 단계별 연습에 대해 자세히 알아보려면 REST API 및 Postman을 사용하여 지식 저장소를 만드는 방법을 보여 주는 다음 문서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [지식 저장소 시작](knowledge-store-howto.md)
