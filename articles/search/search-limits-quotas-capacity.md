---
title: 계층 및 SKU에 대한 서비스 제한 - Azure Search
description: 용량 계획에 사용되는 서비스 제한 및 Azure Search에 대한 요청 및 응답의 최대 제한입니다.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 5187052316e229273aa49eb784bf200c0f16a0f7
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55165390"
---
# <a name="service-limits-in-azure-search"></a>Azure Search의 서비스 제한 사항
저장소, 워크로드 및 인덱스, 문서, 기타 개체의 수량에 대한 최대 제한은 Azure Search를 **무료**, **기본** 또는 **표준** 가격 책정 계층 중 [어디에 프로비전하는지](search-create-service-portal.md)에 따라 달라집니다.

+ **무료** 는 Azure 구독과 함께 제공되는 다중 테넌트 공유 서비스입니다.

+ **기본**은 소규모의 프로덕션 워크로드를 위한 전용 컴퓨팅 리소스를 제공합니다.

+ **표준**은 모든 수준에서 더 많은 저장소 및 처리 용량으로 전용 컴퓨터에서 실행됩니다. 표준은 4가지 수준인 S1, S2, S3 및 S3 HD로 제공됩니다.

  S3 HD(S3 고밀도)는 특정 작업에 대해 설계되었습니다. [다중 테넌트](search-modeling-multitenant-saas-applications.md) 및 많은 양의 작은 인덱스(인덱스당 1백만 개의 문서, 서비스당 3천 개의 인덱스) 이 계층은 [인덱서 기능](search-indexer-overview.md)을 제공하지 않습니다. S3 HD에서 데이터 수집은 원본에서 인덱스로 데이터를 푸시하는 API 호출을 사용하여 푸시 접근 방식을 활용해야 합니다. 

> [!NOTE]
> 서비스는 특정 계층에서 프로비전됩니다. 용량을 얻기 위해 계층을 건너뛰려면 새 서비스를 프로비전해야 합니다(전체 업그레이드 없음). 자세한 내용은 [SKU 또는 계층 선택](search-sku-tier.md)을 참조하세요. 이미 프로비전한 서비스 내에서 용량을 조정하는 방법에 대한 자세한 내용은 [쿼리 및 인덱싱 워크로드에 대한 리소스 수준 크기 조정](search-capacity-planning.md)을 참조하세요.
>

## <a name="subscription-limits"></a>구독 제한
[!INCLUDE [azure-search-limits-per-subscription](../../includes/azure-search-limits-per-subscription.md)]

## <a name="storage-limits"></a>저장소 제한
[!INCLUDE [azure-search-limits-per-service](../../includes/azure-search-limits-per-service.md)]

<a name="index-limits"></a>

## <a name="index-limits"></a>인덱스 제한

| 리소스 | 무료 | 기본&nbsp;<sup>1</sup>  | S1 | S2 | S3 | S3&nbsp;HD |
| -------- | ---- | ------------------- | --- | --- | --- | --- |
| 최대 인덱스 |3 |5 또는 15 |50 |200 |200 |파티션당 1000 또는 서비스당 3000 |
| 인덱스당 최대 필드 |1000 |100 |1000 |1000 |1000 |1000 |
| 인덱스당 최대 [확인기](https://docs.microsoft.com/rest/api/searchservice/suggesters) |1 |1 |1 |1 |1 |1 |
| 인덱스당 [최대 점수 매기기](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) 프로필 |100 |100 |100 |100 |100 |100 |
| 프로필당 최대 함수 |8 |8 |8 |8 |8 |8 |

<sup>1</sup> 2017년 후반 이후에 생성된 기본 서비스는 인덱스, 데이터 원본 및 인덱서 한도가 15개로 증가되었습니다. 앞에서 만든 서비스는 5개입니다. 기본 계층은 인덱스당 100개 필드 제한보다 더 낮은 SKU입니다.

<a name="document-limits"></a>

## <a name="document-limits"></a>문서 제한 

2018년 10월을 기준으로 모든 지역의 모든 청구 가능 계층(기본, S1, S2, S3, S3 HD)에서 생성된 모든 새 서비스에 대한 문서 제한이 더 이상 적용되지 않습니다. 2017년 11월/12월 이래로 대부분의 지역에서는 문서 수 제한이 없어졌지만 아직 5개 지역에서는 문서 제한을 적용하고 있습니다. 검색 서비스를 만든 시기 및 위치에 따라, 여전히 문서 제한이 적용되는 서비스를 실행하고 있을 수 있습니다.

서비스에 문서 제한이 있는지 여부를 확인하려면 서비스의 개요 페이지에서 사용량 타일을 확인합니다. 문서 개수에는 제한이 없거나 계층에 따라 제한을 받습니다.

  ![사용량 타일](media/search-limits-quotas-capacity/portal-usage-tile.png)

### <a name="regions-previously-having-document-limits"></a>이전에 문서 제한이 있던 지역

포털에서 문서 제한이 지정하는 경우 2017년 후반 이전에 서비스가 만들어졌거나, Azure Search 서비스를 호스트하는 데 저용량 클러스터를 사용하는 데이터 센터에서 만들어진 것입니다.

+ 오스트레일리아 동부
+ 동아시아
+ 인도 중부
+ 일본 서부
+ 미국 중서부

문서 제한을 받는 서비스의 경우 다음과 같은 최대 제한이 적용됩니다.

|  무료 | Basic | S1 | S2 | S3 | S3&nbsp;HD |
|-------|-------|----|----|----|-------|
|  10000 |1백만 |파티션당 1천 5백만, 서비스당 1억 8천만 |파티션당 6천만, 서비스당 7억 2천만 |파티션당 1억 2천만, 서비스당 14억 |인덱스당 1백만, 파티션당 2억만 |

서비스의 제한 때문에 작업이 차단되는 경우 새 서비스를 만들고 해당 서비스에 모든 콘텐츠를 다시 게시합니다. 백그라운드 작업을 통해 새 하드웨어에 서비스를 원활하게 다시 프로비전할 수 있는 메커니즘은 없습니다.

> [!Note] 
> 2017년 후반 이후에 생성된 S3 고밀도 서비스의 경우 파티션당 2억 개의 문서가 제거되었지만 인덱스당 백만 개의 문서 한도가 남아 있습니다.


### <a name="document-size-limits-per-api-call"></a>API 호출당 문서 크기 제한

인덱스 API를 호출할 때 최대 문서 크기는 약 16MB입니다.

문서 크기는 실제로 인덱스 API 요청 본문의 크기를 제한합니다. 여러 문서를 포함하는 배치를 한꺼번에 인덱스 API에 전달할 수 있으므로 현실적으로 크기 제한은 배치에 포함된 문서 수에 따라 좌우됩니다. 단일 문서를 포함하는 배치의 경우 최대 문서 크기는 16MB의 JSON입니다.

문서 크기를 줄이려면 요청에서 쿼리할 수 없는 데이터를 제외해야 합니다. 이미지 및 기타 이진 데이터는 및 직접 쿼리할 수 없으므로 인덱스에 저장하지 않아야 합니다. 쿼리할 수 없는 데이터를 검색 결과에 통합하려면 리소스에 대한 URL 참조를 저장하는 검색할 수 없는 필드를 정의합니다.

## <a name="indexer-limits"></a>인덱서 제한

2017년 후반 이후에 생성된 기본 서비스는 인덱스, 데이터 원본, 기술 집합 및 인덱서의 한도가 15개로 증가되었습니다.

Azure Blob 인덱싱의 이미지 분석이나 인식 검색의 자연어 처리와 같이 리소스가 많이 사용되는 작업은 다른 인덱싱 작업이 수용될 수 있도록 최대 실행 시간이 짧습니다. 인덱싱 작업이 허용된 최대 시간 내에 완료될 수 없는 경우에는 일정에 따라 실행해보십시오. 스케줄러는 인덱싱 상태를 추적합니다. 어떤 이유로든 예약된 인덱싱 작업이 중단되면 다음에 예약된 실행에서 인덱서가 지난 번에 중단된 위치부터 작업을 이어갈 수 있습니다.

| 리소스 | 무료&nbsp;<sup>1</sup> | 기본&nbsp;<sup>2</sup>| S1 | S2 | S3 | S3&nbsp;HD&nbsp;<sup>3</sup>|
| -------- | ----------------- | ----------------- | --- | --- | --- | --- |
| 최대 인덱서 |3 |5 또는 15|50 |200 |200 |해당 없음 |
| 최대 데이터 원본 |3 |5 또는 15 |50 |200 |200 |해당 없음 |
| 최대 기술 집합 <sup>4</sup> |3 |5 또는 15 |50 |200 |200 |해당 없음 |
| 호출당 최대 인덱싱 로드 |10,000개 문서 |최대 문서에 의해서만 제한됨 |최대 문서에 의해서만 제한됨 |최대 문서에 의해서만 제한됨 |최대 문서에 의해서만 제한됨 |해당 없음 |
| 최대 실행 시간 <sup>5</sup> | 1-3분 |24시간 |24시간 |24시간 |24시간 |해당 없음  |
| 인식 검색 기능이나 이미지 분석을 통한 Blob 인덱싱의 최대 실행 시간 <sup>5</sup> | 3~10분 |2시간 |2시간 |2시간 |2시간 |해당 없음  |
| Blob 인덱서: 최대 Blob 크기(MB) |16 |16 |128 |256 |256 |해당 없음  |
| Blob 인덱서: Blob에서 추출된 콘텐츠의 최대 문자 |32,000 |64,000 |400만 |400만 |400만 |해당 없음 |

<sup>1</sup> 무료 계층의 인덱서 최대 실행 시간은 Blob 원본의 경우 3분이고 기타 모든 데이터 원본의 경우 1분입니다.

<sup>2</sup> 2017년 후반 이후에 생성된 기본 서비스는 인덱스, 데이터 원본 및 인덱서 한도가 15개로 증가되었습니다. 앞에서 만든 서비스는 5개입니다.

<sup>3</sup> S3 HD 서비스는 인덱서 지원을 포함하지 않습니다.

<sup>4</sup> 기술 집합당 최대 30개의 기술입니다.

<sup>5</sup> 인식 검색 워크로드와 Azure Blob 인덱싱 이미지 분석의 실행 시간은 일반 텍스트 인덱싱보다 짧습니다. 이미지 분석과 자연어 처리는 계산 집약적이며 사용 가능한 처리 능력을 과도하게 많이 소비합니다. 큐의 다른 작업에 실행할 기회를 제공하기 위해 실행 시간이 감소되었습니다.  

## <a name="queries-per-second-qps"></a>초당 쿼리 수(QPS)

QPS 예상 값은 모든 고객에 의해 독립적으로 개발되어야 합니다. 인덱스 크기와 복잡성, 쿼리 크기와 복잡성 및 트래픽 양은 QPS의 주 결정자입니다. 이러한 요소를 알 수 없는 경우 의미 있는 예상 값을 제공할 방법이 없습니다.

예측 값은 전용 리소스(기본 계층 및 표준 계층)에서 실행 중인 서비스에 대해 계산되는 경우 더 예측 가능해 집니다. 매개 변수를 더 잘 제어할 수 있기 때문에 QPS를 더 근접하게 예상할 수 있습니다. 예측 값에 접근하는 방법에 대한 지침은 [Azure Search 성능 및 최적화](search-performance-optimization.md)를 참조하세요.

## <a name="data-limits-cognitive-search"></a>데이터 제한(인식 검색)

[엔터티 인식](cognitive-search-skill-entity-recognition.md), [핵심 구 추출](cognitive-search-skill-keyphrases.md), [감정 분석](cognitive-search-skill-sentiment.md) 및 [언어 감지](cognitive-search-skill-language-detection.md)에 대한 텍스트 분석 리소스로 호출을 만드는 [인식 검색 파이프라인](cognitive-search-concept-intro.md)은 데이터가 제한될 수 있습니다. 레코드의 최대 크기는 `String.Length`에 의해 측정된 대로 50,000자여야 합니다. 감성 분석기로 보내기 전에 데이터를 분할해야 할 경우 [텍스트 분할 기술](cognitive-search-skill-textsplit.md)을 사용합니다.

## <a name="api-request-limits"></a>API 요청 제한
* 요청당 최대 16MB <sup>1</sup>
* URL 길이 최대 8KB
* 인덱스 업로드, 병합 또는 삭제 배치당 최대 1000개의 문서
* $orderby 절에 최대 32개의 필드
* 최대 검색어 크기는 UTF-8 인코딩된 텍스트로 32,766바이트(32KB에서 2바이트를 뺀 값)입니다.

<sup>1</sup> Azure Search에서는 이론적 제한으로 달리 제한되지 않는 개별 필드 또는 컬렉션의 콘텐츠에 대한 실질적 한도를 부과하기 위해 요청 본문에 16MB의 상한이 적용됩니다(필드 컴퍼지션 및 제한 사항에 대한 자세한 내용은 [지원되는 데이터 형식](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) 참조).

## <a name="api-response-limits"></a>API 응답 제한
* 검색 결과 페이지당 반환되는 문서 최대 1000개
* 제안 API 요청당 반환되는 제안 최대 100개

## <a name="api-key-limits"></a>API 키 제한
API 키는 서비스 인증에 사용됩니다. 두 가지 형식이 있습니다. 관리자 키는 요청 헤더에서 지정되고 서비스에 전체 읽기/쓰기 액세스 권한을 부여합니다. 쿼리 키는 읽기 전용이며 URL에 지정되고 일반적으로 클라이언트 애플리케이션에 배포됩니다.

* 서비스당 최대 2개의 관리자 키
* 서비스당 최대 50개의 쿼리 키
