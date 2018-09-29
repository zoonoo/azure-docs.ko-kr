---
title: Content Moderator란?
titlesuffix: Azure Cognitive Services
description: Content Moderator를 사용하여 사용자 생성 콘텐츠에서 부적절한 콘텐츠를 추적하고, 플래깅하고, 평가하고, 필터링하는 방법을 알아봅니다.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: overview
ms.date: 06/15/2017
ms.author: sajagtap
ms.openlocfilehash: e109376f47d921fb18d7bb9a6252e80315419ec0
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47226044"
---
# <a name="what-is-content-moderator"></a>Content Moderator란?

콘텐츠 조정은 가능한 불쾌감을 주거나, 원치 않거나, 위험한 콘텐츠를 모니터링하는 프로세스입니다. 조정되는 콘텐츠는 이미지, 텍스트 및 비디오일 수 있습니다.

## <a name="where-it-is-used"></a>사용 위치

다음 목록에서는 Content Moderator가 사용되는 몇 가지 예제 시나리오를 보여 줍니다.

- 제품 카탈로그 및 사용자 생성 콘텐츠를 조정하는 온라인 마켓플레이스
- 사용자 생성 게임 아티팩트 및 대화방을 조정하는 게임 회사
- 사용자가 추가한 이미지, 텍스트 및 비디오를 조정하는 소셜 메시징 플랫폼
- 중앙에서 콘텐츠를 조정하도록 구현하는 엔터프라이즈 미디어 회사
- 학생 및 교육자를 위해 불량하고 불쾌감을 주는 콘텐츠를 필터링하는 K-12 교육 솔루션 공급자

## <a name="what-it-includes"></a>포함되는 항목

Content Moderator는 이미지, 텍스트 및 비디오를 조정하는 데 도움이 되는 몇 가지 웹 서비스 API 및 기본 제공된 인간 참여형 검토 도구로 구성되어 있습니다.

![Content Moderator 블록 다이어그램](images/content-moderator-block-diagram.png)

## <a name="apis"></a>API

Content Moderator는 다음 API를 포함합니다.
  - [**텍스트 조정 API**](text-moderation-api.md): 이 API를 사용하여 텍스트에서 가능한 욕설, 노골적 표현, 외설적 표현, 불쾌감을 주는 표현 및 PII(개인 식별 정보)를 검색합니다.
  - [**사용자 지정 용어 목록 API**](try-terms-list-api.md): 이 API를 사용하여 기본 제공 용어 외에 사용자 지정 용어 목록에서 일치하는 항목을 검색합니다. 이러한 목록을 사용하여 콘텐츠 정책에 따라 콘텐츠를 허용하거나 차단할 수 있습니다.  
  - [**이미지 조정 API**](image-moderation-api.md): 이 API를 사용하여 이미지에서 성인 및 외설 콘텐츠를 검색하고, OCR(광학 문자 인식) 기능을 사용하여 이미지에서 텍스트를 감지하고, 얼굴을 감지합니다.
  - [**사용자 지정 이미지 목록 API**](try-image-list-api.md): 이 API를 사용하여 사용자 지정 이미지 목록, 다시 분류할 필요가 없는 미리 식별된 콘텐츠에서 일치하는 이미지를 검색합니다.
  - [**비디오 조정 API**](video-moderation-api.md): 이 API를 사용하여 비디오에서 잠재적인 성인 및 외설 콘텐츠를 검색합니다.
  - [**검토 API**](try-review-api-job.md): [작업](try-review-api-job.md), [검토](try-review-api-review.md) 및 [워크플로](try-review-api-workflow.md) 작업을 사용하여 검토 도구 내에서 인간 참여형 워크플로를 만들고 자동화합니다.

## <a name="human-review-tool"></a>사용자 검토 도구

Content Moderator 구독에는 기본 제공 [사용자 검토 도구](Review-Tool-User-Guide/human-in-the-loop.md)가 포함됩니다. 앞에서 언급한 검토 API를 사용하여 중재자가 최종 결정을 내릴 수 있도록 텍스트, 이미지 및 비디오 검토를 만듭니다.

![Content Moderator 비디오 검토 도구](images/video-review-default-view.png)

## <a name="next-steps"></a>다음 단계

[빠른 시작](quick-start.md)을 사용하여 Content Moderator를 시작합니다.
