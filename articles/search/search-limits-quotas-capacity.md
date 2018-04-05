---
title: Azure Search의 서비스 제한 사항 | Microsoft Docs
description: 용량 계획에 사용되는 서비스 제한 및 Azure Search에 대한 요청 및 응답의 최대 제한입니다.
services: search
documentationcenter: ''
author: HeidiSteen
manager: jhubbard
editor: ''
tags: azure-portal
ms.assetid: 857a8606-c1bf-48f1-8758-8032bbe220ad
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 03/26/2018
ms.author: heidist
ms.openlocfilehash: fb2234e79e8deb98a94068f31a40c8f0b415d7ba
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2018
---
# <a name="service-limits-in-azure-search"></a>Azure Search의 서비스 제한 사항
저장소, 워크로드 및 인덱스, 문서, 기타 개체의 수량에 대한 최대 제한은 Azure Search를 **무료**, **기본** 또는 **표준** 가격 책정 계층 중 [어디에 프로비전하는지](search-create-service-portal.md)에 따라 달라집니다.

* **무료** 는 Azure 구독과 함께 제공되는 다중 테넌트 공유 서비스입니다. 
* **기본**은 소규모의 프로덕션 워크로드를 위한 전용 컴퓨팅 리소스를 제공합니다.
* **표준**은 모든 수준에서 더 많은 저장소 및 처리 용량으로 전용 컴퓨터에서 실행됩니다. 표준은 4가지 수준인 S1, S2, S3 및 S3 HD(S3 고밀도)로 제공됩니다.

> [!NOTE]
> 서비스는 특정 계층에서 프로비전됩니다. 용량을 얻기 위해 계층을 건너뛰려면 새 서비스를 프로비전해야 합니다(전체 업그레이드 없음). 자세한 내용은 [SKU 또는 계층 선택](search-sku-tier.md)을 참조하세요. 이미 프로비전한 서비스 내에서 용량을 조정하는 방법에 대한 자세한 내용은 [쿼리 및 인덱싱 워크로드에 대한 리소스 수준 크기 조정](search-capacity-planning.md)을 참조하세요.
>

## <a name="subscription-limits"></a>구독 제한
[!INCLUDE [azure-search-limits-per-subscription](../../includes/azure-search-limits-per-subscription.md)]

## <a name="service-limits"></a>서비스 제한
[!INCLUDE [azure-search-limits-per-service](../../includes/azure-search-limits-per-service.md)]

## <a name="index-limits"></a>인덱스 제한

| 리소스 | 무료 | Basic | S1 | S2 | S3 | S3 HD |
| --- | --- | --- | --- | --- | --- | --- |
| 인덱스당 최대 필드 |1000 |100 <sup>1</sup> |1000 |1000 |1000 |1000 |
| 인덱스당 최대 점수 매기기 프로필 |100 |100 |100 |100 |100 |100 |
| 프로필당 최대 함수 |8 |8 |8 |8 |8 |8 |

<sup>1</sup> 기본 계층은 인덱스당 100개 필드 제한보다 더 낮은 SKU입니다.

## <a name="indexer-limits"></a>인덱서 제한

| 리소스 | 무료 | Basic | S1 | S2 | S3 | S3 HD |
| --- | --- | --- | --- | --- | --- | --- |
| 호출당 최대 인덱싱 로드 |10,000개 문서 |최대 문서에 의해서만 제한됨 |최대 문서에 의해서만 제한됨 |최대 문서에 의해서만 제한됨 |최대 문서에 의해서만 제한됨 |N/A <sup>1</sup> |
| 최대 실행 시간 | 1-3분 <sup>2</sup> |24시간 |24시간 |24시간 |24시간 |N/A <sup>1</sup> |
| Blob 인덱서: 최대 Blob 크기(MB) |16 |16 |128 |256 |256 |N/A <sup>1</sup> |
| Blob 인덱서: Blob에서 추출된 콘텐츠의 최대 문자 |32,000 |64,000 |400만 |400만 |400만 |N/A <sup>1</sup> |

<sup>1</sup> S3 HD는 현재 인덱서를 지원하지 않습니다. 이 기능이 긴급하게 필요한 경우 Azure 지원 서비스에 문의하세요.

<sup>2</sup> 무료 계층의 인덱서 최대 실행 시간은 Blob 원본의 경우 3분이고 기타 모든 데이터 원본의 경우 1분입니다.


## <a name="document-size-limits"></a>문서 크기 제한
| 리소스 | 무료 | Basic | S1 | S2 | S3 | S3 HD |
| --- | --- | --- | --- | --- | --- | --- |
| 인덱스 API당 개별 문서 크기 |16MB 미만 |16MB 미만 |16MB 미만 |16MB 미만 |16MB 미만 |16MB 미만 |

인덱스 API를 호출할 때 최대 문서 크기를 참조하세요. 문서 크기는 실제로 인덱스 API 요청 본문의 크기를 제한합니다. 여러 문서를 포함하는 배치를 한꺼번에 인덱스 API에 전달할 수 있으므로 실제로 크기 제한은 배치에 포함된 문서 수에 따라 좌우됩니다. 단일 문서를 포함하는 배치의 경우 최대 문서 크기는 16MB의 JSON입니다.

문서 크기를 줄이려면 요청에서 쿼리할 수 없는 데이터를 제외해야 합니다. 이미지 및 기타 이진 데이터는 및 직접 쿼리할 수 없으므로 인덱스에 저장하지 않아야 합니다. 쿼리할 수 없는 데이터를 검색 결과에 통합하려면 리소스에 대한 URL 참조를 저장하는 검색할 수 없는 필드를 정의합니다.

## <a name="queries-per-second-qps"></a>초당 쿼리 수(QPS)

QPS 예상 값은 모든 고객에 의해 독립적으로 개발되어야 합니다. 인덱스 크기와 복잡성, 쿼리 크기와 복잡성 및 트래픽 양은 QPS의 주 결정자입니다. 이러한 요소를 알 수 없는 경우 의미 있는 예상 값을 제공할 방법이 없습니다.

예측 값은 전용 리소스(기본 계층 및 표준 계층)에서 실행 중인 서비스에 대해 계산되는 경우 더 예측 가능해 집니다. 매개 변수를 더 잘 제어할 수 있기 때문에 QPS를 더 근접하게 예상할 수 있습니다. 예측 값에 접근하는 방법에 대한 지침은 [Azure Search 성능 및 최적화](search-performance-optimization.md)를 참조하세요.

## <a name="api-request-limits"></a>API 요청 제한
* 요청당 최대 16MB <sup>1</sup>
* URL 길이 최대 8KB
* 인덱스 업로드, 병합 또는 삭제 배치당 최대 1000개의 문서
* $orderby 절에 최대 32개의 필드
* 최대 검색어 크기는 UTF-8 인코딩된 텍스트로 32,766바이트(32KB에서 2바이트를 뺀 값)입니다.

<sup>1</sup> Azure Search에서는 이론적 제한으로 달리 제한되지 않는 개별 필드 또는 컬렉션의 콘텐츠에 대한 실질적 한도를 부과하기 위해 요청 본문에 16MB의 상한이 적용됩니다(필드 컴퍼지션 및 제한 사항에 대한 자세한 내용은 [지원되는 데이터 형식](https://msdn.microsoft.com/library/azure/dn798938.aspx) 참조).

## <a name="api-response-limits"></a>API 응답 제한
* 검색 결과 페이지당 반환되는 문서 최대 1000개
* 제안 API 요청당 반환되는 제안 최대 100개

## <a name="api-key-limits"></a>API 키 제한
API 키는 서비스 인증에 사용됩니다. 두 가지 형식이 있습니다. 관리자 키는 요청 헤더에서 지정되고 서비스에 전체 읽기/쓰기 액세스 권한을 부여합니다. 쿼리 키는 읽기 전용이며 URL에 지정되고 일반적으로 클라이언트 응용 프로그램에 배포됩니다.

* 서비스당 최대 2개의 관리자 키
* 서비스당 최대 50개의 쿼리 키
