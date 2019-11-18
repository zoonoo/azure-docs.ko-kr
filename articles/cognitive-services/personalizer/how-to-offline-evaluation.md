---
title: 오프 라인 평가를 수행 하는 방법-Personalizer
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 오프 라인 평가를 사용 하 여 앱의 효율성을 측정 하 고 학습 루프를 분석 하는 방법을 보여 줍니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: c2aec0db2d1f9865188f2749a0eeb765a14d04ed
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73952987"
---
# <a name="analyze-your-learning-loop-with-an-offline-evaluation"></a>오프 라인 평가를 사용 하 여 학습 루프 분석

오프라인 평가를 완료하고 결과를 이해하는 방법을 알아봅니다.

오프 라인 평가를 사용 하면 효과적인 Personalizer를 응용 프로그램의 기본 동작과 비교 하 고, 개인 설정에 가장 적합 한 기능을 알아보고, 새 machine learning 값을 자동으로 검색할 수 있습니다.

자세한 내용은 [오프라인 평가](concepts-offline-evaluation.md)를 참조하세요.


## <a name="prerequisites"></a>선행 조건

* 구성 된 Personalizer 루프
* Personalizer 루프에는 대표적인 양의 데이터가 있어야 합니다. 개략적인는 의미 있는 평가 결과를 위해 로그에 5만 개 이상의 이벤트를 권장 합니다. 필요에 따라 이전에 내보낸 _학습 정책_ 파일을 동일한 평가에서 비교하고 테스트할 수 있습니다.

## <a name="steps-to-start-a-new-offline-evaluation"></a>새 오프 라인 평가를 시작 하는 단계

1. [Azure Portal](https://azure.microsoft.com/free/)에서 개인 설정 리소스를 찾습니다.
1. Azure Portal에서 평가 섹션으로 이동 하 고 **평가 만들기**를 **선택 합니다.**
    Azure Portal에서 ![* * 평가 * * 섹션으로 이동 하 고 * * 평가 만들기 * *를 선택 합니다.](./media/offline-evaluation/create-new-offline-evaluation.png)
1. 다음 값을 구성 합니다.

    * 평가 이름
    * 시작 및 종료 날짜-계산에 사용할 데이터 범위를 지정 하는 과거의 날짜입니다. 이 데이터는 [데이터 보존](how-to-settings.md) 값에 지정 된 대로 로그에 표시 되어야 합니다.
    * 최적화 검색을 **예** 로 설정 합니다.

    ![오프 라인 평가 설정 선택](./media/offline-evaluation/create-an-evaluation-form.png)

1. **확인**을 선택 하 여 평가를 시작 합니다. 

## <a name="results"></a>결과

처리할 데이터의 양, 비교할 학습 정책의 수, 최적화 요청 여부에 따라 평가가 오래 걸릴 수 있습니다.

완료 되 면 평가 목록에서 평가를 선택할 수 있습니다. 

학습 정책의 비교는 다음과 같습니다.

* **온라인 정책**: Personalizer에서 사용 되는 현재 학습 정책
* **기준선**: 응용 프로그램의 기본값 (순위 호출에서 보낸 첫 번째 작업에 의해 결정 됨)
* **임의 정책**: 제공 된 작업에서 항상 임의 선택 작업을 반환 하는 허수 랭크 동작입니다.
* **사용자 지정 정책**: 평가를 시작할 때 추가 학습 정책이 업로드 됩니다.
* **최적화 된 정책**: 최적화 된 정책을 검색 하는 옵션을 사용 하 여 평가를 시작한 경우에도 비교 되며,이를 다운로드 하거나 온라인 학습 정책으로 만들어 현재 항목을 바꿀 수 있습니다.

![오프 라인 평가 설정의 결과 차트](./media/offline-evaluation/evaluation-results.png)

작업 및 컨텍스트에 대한 [기능](concepts-features.md)의 효율성

## <a name="next-steps"></a>다음 단계

* [오프라인 평가 수행 방법](concepts-offline-evaluation.md)을 알아보세요.
