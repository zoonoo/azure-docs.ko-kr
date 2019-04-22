---
title: Text Analytics API의 예제 사용자 시나리오
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 Text Analytics API를 서비스 및 프로세스에 통합하는 몇 가지 일반적인 시나리오를 살펴봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 04/04/2019
ms.author: aahi
ms.openlocfilehash: d8f8d110a13e768d93f0be3c4baaf5715436f2ed
ms.sourcegitcommit: 41015688dc94593fd9662a7f0ba0e72f044915d6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/11/2019
ms.locfileid: "59502435"
---
# <a name="example-user-scenarios-for-the-text-analytics-api"></a>Text Analytics API의 예제 사용자 시나리오

Text Analytics API는 텍스트에 대한 고급 자연어 처리를 제공하는 클라우드 기반 서비스입니다. 이 문서에서는 API를 비즈니스 솔루션 및 프로세스에 통합하는 몇 가지 예제 사용 사례를 설명합니다. 

## <a name="analyze-survey-results"></a>설문 조사 결과 분석

감정 분석을 통해 원시 텍스트 응답을 처리하여 고객 및 직원 설문 조사 결과에서 인사이트를 획득합니다. 알아낸 사실을 집계하여 분석, 후속 조치 및 고객 참여 유도에 활용합니다.

![고객 및 직원 설문 조사를 대상으로 감정 분석을 수행하는 방법을 보여주는 이미지](media/use-cases/survey-results.svg)

## <a name="analyze-recorded-inbound-customer-calls"></a>녹음된 인바운드 고객 전화 분석

Text to Speech, 감정 분석 및 핵심 구 추출을 사용하여 고객 서비스 전화에서 인사이트를 추출합니다. 고객을 보다 정확하게 이해하고, 고객 서비스 추세를 강조 표시하고, 고객 참여를 유도할 수 있도록 결과를 Power BI 대시보드 또는 포털에 표시합니다. API 요청을 보고용 일괄 처리로 보내거나 작업을 위해 실시간으로 보냅니다. 

![감정 분석을 사용하여 고객 서비스 전화에서 인사이트를 획득하는 과정을 자동화하는 방법을 설명하는 이미지](media/use-cases/azure-inbound.svg)

## <a name="process-and-categorize-support-incidents"></a>지원 인시던트 처리 및 분류

핵심 구 추출 및 엔터티 인식을 사용하여 비정형 텍스트 형식으로 제출된 지원 요청을 처리합니다. 추출된 구와 엔터티를 사용하여 리소스 계획 및 추세 분석에 대한 요청을 분류합니다.

![핵심 구 추출 및 엔터티 인식을 사용하여 인시던트 보고서와 추세를 분류하는 방법을 설명하는 이미지](media/use-cases/support-incidents.svg)

## <a name="monitor-your-products-social-media-feeds"></a>제품의 소셜 미디어 피드 모니터링

제품의 twitter 또는 Facebook 페이지에 올라오는 사용자 제품 피드백을 모니터링합니다. 데이터를 사용하여 신제품 출시를 위해 고객 감정을 분석하고, 기능 및 기능 요청에 대한 핵심 구를 추출하고, 발생하는 고객 불만 사항을 해결합니다.

![핵심 구 추출을 사용하여 소셜 미디어의 제품 및 회사 피드백을 모니터링하는 방법을 설명하는 이미지](media/use-cases/social-feed.svg)

## <a name="next-steps"></a>다음 단계

* [텍스트 분석 API란?](overview.md)
* [C#을 사용하여 Text Analytics API로 요청 보내기](quickstarts/csharp.md)