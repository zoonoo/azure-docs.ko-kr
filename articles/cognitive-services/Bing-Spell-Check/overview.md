---
title: Bing Spell Check API란?
titlesuffix: Azure Cognitive Services
description: 상황에 맞는 맞춤법 검사를 위해 기계 학습 및 통계 기계 번역을 사용하는 Bing Spell Check API에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: overview
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: 22f75efb3cb4baa645030e7ad64072674de662ed
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "60593202"
---
# <a name="what-is-the-bing-spell-check-api"></a>Bing Spell Check API란?

Bing Spell Check API를 사용하면 텍스트에 대해 문맥 문법 및 맞춤법 검사를 수행할 수 있습니다. 대부분의 맞춤법 검사기는 사전 기반 규칙 세트에 의존하지만 Bing 맞춤법 검사기는 기계 학습 및 통계 기계 번역을 활용하여 정확한 문맥 교정을 제공합니다. 

## <a name="features"></a>기능


|  |  |
|---------|---------|
|여러 맞춤법 검사 모드     | 여러 맞춤법 검사 모드를 사용하면 문법 및/또는 맞춤법에 중점을 둔 교정을 받을 수 있습니다. |
|은어 및 비격식 언어 인식     | 텍스트에 사용된 일반적인 표현 및 비격식 용어를 인식합니다.         |
|유사한 단어 구별     | 발음이 비슷하지만 의미가 다른 단어(예: "see"와 "sea") 사이에서 올바른 사용법을 찾습니다.        |
|브랜드, 타이틀 및 인기있는 표현 사용 지원     | 새로운 브랜드, 타이틀 및 기타 인기있는 표현이 등장하면 인식합니다. |

## <a name="workflow"></a>워크플로

Bing Spell Check API는 HTTP 요청을 수행하고 JSON 응답을 구문 분석할 수 있는 모든 프로그래밍 언어에서 쉽게 호출할 수 있습니다. 이 서비스는 REST API 또는 Bing Spell Check SDK를 사용하여 액세스할 수 있습니다. 

1. Bing Search API에 대한 액세스 권한이 있는 [Cognitive Services API 계정](../cognitive-services-apis-create-account.md)을 만듭니다. Azure 구독이 없는 경우 체험 계정을 만들 수 있습니다. 
2. Bing Web Search API에 요청을 보냅니다.
3. JSON 응답 구문 분석

## <a name="next-steps"></a>다음 단계

먼저 Bing Spell Check Search API [대화형 데모](https://azure.microsoft.com/services/cognitive-services/spell-check/)를 시도하여 다양한 텍스트를 신속하게 확인할 수 있는 방법을 살펴봅니다.

API를 호출할 준비가 되면 [Cognitive Services API 계정](../../cognitive-services/cognitive-services-apis-create-account.md)을 만듭니다. Azure 구독이 없는 경우 [무료 계정](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)을 만들 수 있습니다.
