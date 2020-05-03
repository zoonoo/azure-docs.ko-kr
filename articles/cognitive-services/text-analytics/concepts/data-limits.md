---
title: Text Analytics API에 대한 데이터 제한
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services의 Text Analytics API에 대한 데이터 제한.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 04/27/2020
ms.author: aahi
ms.reviewer: chtufts
ms.openlocfilehash: 4af2d060c11b804c5fa09bfdabbcb9753f7d5885
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204366"
---
# <a name="data-and-rate-limits-for-the-text-analytics-api"></a>Text Analytics API에 대한 데이터 및 속도 제한
<a name="data-limits"></a>

이 문서를 사용하여 Text Analytics API에 데이터를 보낼 수 있는 크기 및 속도에 대한 제한을 찾습니다. 

## <a name="data-limits"></a>데이터 제한

> [!NOTE]
> * 허용 한도보다 큰 문서를 분석해야 하는 경우 텍스트를 API로 보내기 전에 텍스트를 더 작은 텍스트 청크로 분할할 수 있습니다. 
> * 문서는 텍스트 문자의 단일 문자열입니다.  

| 제한 | 값 |
|------------------------|---------------|
| 단일 문서의 최대 크기 | [StringInfo.LengthInTextElements](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements)에 의해 측정된 5,120자. |
| 전체 요청의 최대 크기 | 1MB |

단일 요청으로 보낼 수 있는 최대 문서 수는 사용 중인 API 버전 및 기능에 따라 달라집니다.

#### <a name="version-30-preview"></a>[버전 3.0 미리 보기](#tab/version-3)

> [!NOTE]
> v3 API 요청이 이러한 제한을 초과하지만 v2 제한 내에 있으면 API 응답에 경고가 반환됩니다. 2020년 7월 15일부터 400개의 오류 코드가 대신 반환됩니다. 

API의 v3에서 다음과 같은 제한 사항이 변경되었습니다. 아래 제한을 초과하면 API 응답에 경고가 생성됩니다.


| 기능 | 요청당 최대 문서 수 | 
|----------|-----------|
| 언어 검색 | 1000 |
| 감정 분석 | 10 |
| 핵심 문구 추출 | 10 |
| 명명된 엔터티 인식 | 5 |
| 엔터티 연결 | 5 |

#### <a name="version-2"></a>[버전 2](#tab/version-2)

| 기능 | 요청당 최대 문서 수 | 
|----------|-----------|
| 언어 검색 | 1000 |
| 감정 분석 | 1000 |
| 핵심 문구 추출 | 1000 |
| 명명된 엔터티 인식 | 1000 |
| 엔터티 연결 | 1000 |

---

## <a name="rate-limits"></a>속도 제한

속도 제한은 [가격 책정 계층](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)에 따라 달라집니다. 이러한 제한은 API의 두 버전 모두에 동일합니다.

| 계층          | 초당 요청 | 분당 요청 |
|---------------|---------------------|---------------------|
| S / 다중 서비스 | 1000                | 1000                |
| S0 / F0         | 100                 | 300                 |
| S1            | 200                 | 300                 |
| S2            | 300                 | 300                 |
| S3            | 500                 | 500                 |
| S4            | 1000                | 1000                |

요청은 각 Text Analytics 기능에 대해 개별적으로 측정됩니다. 예를 들어 가격 책정 계층에 대한 최대 요청 수를 각 기능에 동시에 보낼 수 있습니다.  


## <a name="see-also"></a>참고 항목

* [Text Analytics API란?](../overview.md)
* [가격 정보](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)
