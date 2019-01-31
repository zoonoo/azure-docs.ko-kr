---
title: 반복적인 앱 디자인
titleSuffix: Language Understanding - Azure Cognitive Services
description: LUIS는 모델 변경, 발화 예제, 게시 및 엔드포인트 쿼리에서 데이터 수집의 반복 주기에서 가장 좋은 사례를 학습합니다.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: diberry
ms.openlocfilehash: 3959aa89f006d413d31f6172ad23d7eae6d9ee22
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55221099"
---
# <a name="authoring-cycle-for-your-luis-app"></a>LUIS 앱에 대한 주기 작성
LUIS는 모델 변경, 발화 예제, 게시 및 엔드포인트 쿼리에서 데이터 수집의 반복 주기에서 가장 좋은 사례를 학습합니다. 

![제작 주기](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-model"></a>LUIS 모델 빌드
모델의 목적은 사용자가 요청하는 내용(의도) 및 대답을 결정하는 데 도움이 되는 세부 정보를 제공하는 질문의 부분(엔터티)을 파악하는 것입니다. 

일반적인 단어 순서뿐 아니라 관련된 단어 및 구문을 확인하려면 모델이 앱 도메인에 관련되어야 합니다. 

모델에는 의도, 엔터티가 포함됩니다. 

## <a name="add-training-examples"></a>학습 예제 추가
LUIS에는 의도의 예제 발화가 필요합니다. 예제에는 발화의 목적인 의도를 결정할 수 있는 단어 선택 및 단어 순서의 충분한 변형이 필요합니다. 각 예제 발화에는 엔터티로 레이블이 지정된 필수 데이터가 있어야 합니다. 

발화를 **None** 의도에 할당하여 앱 도메인에 관련되지 않은 발화를 무시하도록 LUIS에 지시합니다. 발화에서 끌어올 필요가 없는 단어나 구문에는 레이블을 지정할 필요가 없습니다. 무시할 단어 또는 구문에는 레이블이 없습니다. 

## <a name="train-and-publish-the-app"></a>앱 학습 및 게시
각 의도에 10~15개의 여러 발화가 포함되고 필요한 엔터티에 레이블이 지정되면 학습시킨 다음, 게시합니다. 게시 성공 알림에서 링크를 사용하여 엔드포인트를 가져옵니다. 필요한 [엔드포인트 지역](luis-reference-regions.md)에서 사용할 수 있도록 앱을 만들고 앱을 게시해야 합니다. 

## <a name="https-endpoint-testing"></a>HTTPS 엔드포인트 테스트
HTTPS 엔드포인트에서 LUIS 앱을 테스트할 수 있습니다. 엔드포인트에서 테스트하면 LUIS가 검토를 위해 신뢰도가 낮은 발화를 선택할 수 있습니다.  

## <a name="recycle"></a>재순환
작성 주기를 완료하면 다시 시작할 수 있습니다. 먼저 신뢰도가 낮게 표시된 엔드포인트 발화 LUIS를 검토합니다. 의도 및 엔터티에 모두 이러한 발화를 확인합니다. 발화를 검토한 후 검토 목록이 비어 있어야 합니다.  

## <a name="batch-testing"></a>일괄 테스트
일괄 테스트는 LUIS에서 점수가 매겨진 예제 수를 확인하는 방법입니다. 예제는 LUIS에 새로운 내용이어야 하고 LUIS가 찾을 의도 및 엔터티로 올바르게 레이블이 지정되어야 합니다. 테스트 결과는 발화 집합에서 LUIS가 얼마나 잘 작동하는지를 나타냅니다. 

## <a name="next-steps"></a>다음 단계

[공동 작업](luis-concept-collaborator.md) 개념을 알아봅니다.
