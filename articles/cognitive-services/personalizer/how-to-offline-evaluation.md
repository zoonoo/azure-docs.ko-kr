---
title: 오프라인 평가 수행 방법 - 개인화
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 오프라인 평가를 사용하여 앱의 효과를 측정하고 학습 루프를 분석하는 방법을 보여 줍니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: diberry
ms.openlocfilehash: ce85c2d264b2b4849a4a36ed757150292fdf39f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77622769"
---
# <a name="analyze-your-learning-loop-with-an-offline-evaluation"></a>오프라인 평가로 학습 루프 분석

오프라인 평가를 완료하고 결과를 이해하는 방법을 알아봅니다.

오프라인 평가를 사용하면 Personalr가 응용 프로그램의 기본 동작과 얼마나 효과적인지 측정하고, 개인 설정에 가장 많이 기여하는 기능을 학습하고, 새로운 기계 학습 값을 자동으로 검색할 수 있습니다.

자세한 내용은 [오프라인 평가](concepts-offline-evaluation.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* 구성된 개인화 루프
* 개인 설정 루프에는 대표적인 양의 데이터가 있어야 합니다 . 필요에 따라 이전에 내보낸 _학습 정책_ 파일을 동일한 평가에서 비교하고 테스트할 수 있습니다.

## <a name="run-an-offline-evaluation"></a>오프라인 평가 실행

1. Azure [포털에서](https://azure.microsoft.com/free/)개인 설정 자 리소스를 찾습니다.
1. Azure 포털에서 평가 섹션으로 이동하여 **평가 만들기를** **선택합니다.**
    ![Azure 포털에서 **평가** 섹션으로 이동하여 **평가 만들기**를 선택합니다.](./media/offline-evaluation/create-new-offline-evaluation.png)
1. 다음 값을 구성 합니다.

    * 평가 이름입니다.
    * 시작 및 종료 날짜 - 평가에 사용할 데이터 범위를 지정하는 날짜입니다. 이 데이터는 [데이터 보존](how-to-settings.md) 값에 지정된 대로 로그에 있어야 합니다.
    * 최적화 검색은 **예로**설정됩니다.

    > [!div class="mx-imgBorder"]
    > ![오프라인 평가 설정 선택](./media/offline-evaluation/create-an-evaluation-form.png)

1. **확인을**선택하여 평가를 시작합니다.

## <a name="review-the-evaluation-results"></a>평가 결과 검토

처리할 데이터의 양, 비교할 학습 정책의 수, 최적화 요청 여부에 따라 평가가 오래 걸릴 수 있습니다.

평가 목록에서 평가를 선택한 다음 **응용 프로그램의 점수와 다른 잠재적 학습 설정 비교를**선택할 수 있습니다. 현재 학습 정책이 새 정책과 비교하여 어떻게 수행되는지 확인하려면 이 기능을 선택합니다.

1. [학습 정책의](concepts-offline-evaluation.md#discovering-the-optimized-learning-policy)성능을 검토합니다.

    > [!div class="mx-imgBorder"]
    > [![평가 결과 검토](./media/offline-evaluation/evaluation-results.png)](./media/offline-evaluation/evaluation-results.png#lightbox)

1. 데이터에 가장 적합한 모델을 개선하는 정책을 적용하려면 **적용을** 선택합니다.

## <a name="next-steps"></a>다음 단계

* [오프라인 평가의 작동 방식에](concepts-offline-evaluation.md)대해 자세히 알아보십시오.
