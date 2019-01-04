---
title: Web Language Model API 개요
titleSuffix: Azure Cognitive Services
description: Microsoft Cognitive Services의 Web Language Model API는 자연어 처리를 위한 첨단 도구를 제공합니다.
services: cognitive-services
author: piyushbehre
manager: cgronlun
ms.service: cognitive-services
ms.component: web-language-model
ms.topic: overview
ms.date: 08/12/2016
ms.author: pibehre
ROBOTS: NOINDEX
ms.openlocfilehash: 3c7f9e4cc4cf023aa93bd80ad1165ba9595e9034
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/28/2018
ms.locfileid: "53807440"
---
# <a name="what-is-the-web-language-model-api-preview"></a>Web Language Model API란? (미리 보기)

> [!IMPORTANT]
> Web Language Model 미리 보기는 2018년 8월 9일부로 서비스 해제되었습니다. 텍스트 처리 및 분석에는 [Azure Machine Learning 텍스트 분석 모듈](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics)을 사용하는 것이 좋습니다.

Microsoft Web Language Model API는 자연어 처리를 위한 첨단 도구를 제공하는 REST 기반 클라우드 서비스입니다. 이 API를 사용하면 Bing이 en-US 시장에서 수집한 웹 규모 자료에 대해 학습한 언어 모델을 통해 빅 데이터의 강력한 힘을 애플리케이션에서 활용할 수 있습니다.

5차 마르코프 사슬까지 지원하는 이러한 평탄화된 백오프 N-gram 언어 모델은 다음 자료에 대해 학습됩니다.

- 웹 페이지 본문 텍스트
- 웹 페이지 제목 텍스트
- 웹 페이지 앵커 텍스트
- 웹 검색 쿼리 텍스트

Web Language Model API는 다음과 같은 네 가지 조회 작업을 지원합니다.

1. 단어 시퀀스의 결합(log10) 확률.
2. 이전 단어의 시퀀스가 주어졌을 때 한 단어의 조건부(log10) 확률.
3. 지정된 단어 시퀀스를 따를 가능성이 가장 높은 단어 목록(완성).
4. 공백이 없는 문자열의 단어 분리.

## <a name="getting-started"></a>시작하기

1. 서비스를 구독합니다.
2. [SDK](https://www.github.com/microsoft/cognitive-weblm-windows)를 다운로드합니다.
3. SDK 샘플 코드를 실행합니다.
4. 다양한 언어로 작성된 코드 조각을 포함하여 엔드포인트에 대한 전체 세부 정보는 [API 참조](http://web.archive.org/web/20170503191852/westus.dev.cognitive.microsoft.com/docs/services/55de9ca4e597ed1fd4e2f104/operations/55de9ca4e597ed19b0de8a51)에서 확인할 수 있습니다.

## <a name="underlying-technology"></a>기본 기술

다음 문서는 이러한 언어 모델의 개발에 대한 세부 정보를 제공하며, 이 서비스를 사용하는 연구 간행물에 인용됩니다.

- [Microsoft Web N-gram 자료 및 애플리케이션 개요](https://research.microsoft.com/apps/pubs/default.aspx?id=130762), NAACL HLT 2010

이 연구를 인용하는 현재 논문 목록을 보려면 [여기](https://academic.microsoft.com/#/search?iq=And%28Ty%3D'0'%2CRId%3D2145833060%29&q=papers%20citing%20an%20overview%20of%20microsoft%20web%20n%20gram%20corpus%20and%20applications&filters=&from=0&sort=0)를 클릭하세요.
