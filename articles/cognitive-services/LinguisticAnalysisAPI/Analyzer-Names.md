---
title: 분석기 명명 구조 - Linguistic Analysis API
titlesuffix: Azure Cognitive Services
description: Linguistic Analysis API 분석기 명명 구조를 통해 유연성과 정확도가 구현되는 방식을 알아봅니다.
services: cognitive-services
author: RichardSunMS
manager: cgronlun
ms.service: cognitive-services
ms.subservice: linguistic-analysis
ms.topic: conceptual
ms.date: 03/23/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: 997dcdd6ce99815a79006b40b87568e573c63ff3
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55221686"
---
# <a name="analyzer-names"></a>분석기 이름

> [!IMPORTANT]
> Linguistic Analysis 미리 보기는 2018년 8월 9일부로 서비스 해제되었습니다. 텍스트 처리 및 분석에는 [Azure Machine Learning 텍스트 분석 모듈](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics)을 사용하는 것이 좋습니다.

우리는 분석기에서 유연성을 허용하고 이름이 의미하는 바를 정확하게 이해하기 위해 다소 복잡한 명명 체계를 분석기에 사용합니다.
분석기 이름은 ID, 종류, 사양, 구현의 네 부분으로 구성됩니다.
각 구성 요소의 역할은 아래에 정의되어 있습니다.

## <a name="id"></a>ID
첫째, 분석기에는 고유 ID인 GUID가 있습니다.
이러한 GUID는 되도록이면 변경하지 않는 것이 좋지만, 특정 분석기를 설명하는 유일한 방법입니다.

## <a name="kind"></a>종류
다음으로, 분석기마다 **종류**가 있습니다.
종류는 반환되는 분석 종류를 매우 방대한 용어로 정의하며, 해당 분석을 나타내는 데 사용되는 데이터 구조를 고유하게 정의해야 합니다.
현재 세 가지 고유한 종류가 있습니다.
 - [토큰](Sentences-and-Tokens.md)
 - [POS 태그](Pos-Tagging.md)
 - [팀원 트리](constituency-parsing.md)

## <a name="specification"></a>사양
하지만 종류가 지정된 경우 다른 전문가들이 특정 현상을 분석하는 방법에 동의하지 않을 수 있습니다.
프로그래밍 언어와는 달리, 분석 방법에 대한 명확하고 정확한 정의가 없습니다.

예를 들어 영어 문장 "He didn't go"에서 토큰을 찾으려 한다고 가정해 봅시다.
특히 문자열 "didn't"를 잘 생각해 보세요.
가능한 해석 중 하나는 이 문자열을 "did"와 "not"의 두 가지 토큰으로 분할하는 것입니다.
그러면 대체 문장 "He did not go"가 동일한 토큰 집합을 갖게 됩니다.
또 다른 가능성은 "did" 및 "n't" 토큰으로 분할하는 것입니다.
일반적으로 두 번째 토큰은 단어로 취급되지 않지만, 표면 문자열에 대한 더 많은 정보를 제공하므로 경우에 따라 유용할 수 있습니다.
또는 이 축약형을 한 단어로 간주해야 합니다.

무엇을 선택하든, 일관적이어야 합니다.
정확한 표현을 결정하는 것이 **사양**의 정확한 역할입니다.

분석기 출력은 같은 사양을 따르는 데이터와의 비교만 가능합니다.

## <a name="implementation"></a>구현

종종 동일한 결과를 얻으려고 시도하는 여러 모델이 있지만, 성능 특징이 다릅니다.
속도가 빠르지만 정확도가 낮은 모델처럼 모델마다 특징이 다릅니다.

사용자가 자신의 요구 사항에 가장 적합한 분석기를 선택할 수 있도록 분석기 이름의 **구현** 부분은 이 형식의 정보를 식별하는 데 사용됩니다.
