---
title: Personalizer 구성
description: 서비스 구성에는 서비스의 보상 처리 방법, 서비스의 탐색 빈도, 모델을 다시 학습하는 빈도 및 저장할 데이터의 양이 포함됩니다.
ms.topic: conceptual
ms.date: 02/19/2020
ms.openlocfilehash: ac31a9f907defeb44dbd4748a4395d3aec34d30c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79221214"
---
# <a name="configure-personalizer-learning-loop"></a>Personalizer learning 루프 구성

서비스 구성에는 서비스의 보상 처리 방법, 서비스의 탐색 빈도, 모델을 다시 학습하는 빈도 및 저장할 데이터의 양이 포함됩니다.

Personalizer 리소스에 대 한 Azure Portal **구성** 페이지에서 학습 루프를 구성 합니다.

<a name="configure-service-settings-in-the-azure-portal"></a>
<a name="configure-reward-settings-for-the-feedback-loop-based-on-use-case"></a>

## <a name="configure-rewards-for-the-feedback-loop"></a>피드백 루프의 보상 구성

학습 루프의 보상 사용에 대 한 서비스를 구성 합니다. 다음 값을 변경 하면 현재 Personalizer 모델을 다시 설정 하 고 지난 2 일간의 데이터를 다시 학습 합니다.

> [!div class="mx-imgBorder"]
> 피드백 루프의 보상 값을 구성 ![](media/settings/configure-model-reward-settings.png)

|값|목적|
|--|--|
|보상 대기 시간|Personalizer가 순위 호출에 대한 보상 값을 수집하는 시간 길이를 설정하며, 순위 호출이 발생하는 순간부터 시작합니다. 이 값은 "Personalizer가 보상 호출을 대기 하는 시간"을 요청 하 여 설정 됩니다. 이 기간 후에 도착하는 보상은 기록되지만 학습에 사용되지는 않습니다.|
|기본 보상|순위 호출과 연결된 보상 대기 시간 동안 Personalizer가 보상 호출을 하나도 받지 않은 경우 Personalizer는 기본 보상을 할당합니다. 기본적으로 및 대부분의 시나리오에서 기본 보상은 0입니다.|
|보상 집계|동일한 순위 API 호출에 대해 여러 보상이 수신되는 경우 집계 메서드 **sum** 또는 **earliest**가 사용됩니다. Earliest는 가장 먼저 받은 점수를 선택하고 나머지 점수를 무시합니다. 이는 중복 된 호출에서 고유한 보상을 원하는 경우에 유용 합니다. |

이러한 값을 변경한 후에는 **저장**을 선택 해야 합니다.

## <a name="configure-exploration-to-allow-the-learning-loop-to-adapt"></a>학습 루프가 조정 될 수 있도록 탐색 구성

개인 설정은 학습 된 모델의 예측을 사용 하는 대신 대안을 탐색 하 여 새 패턴을 검색 하 고 시간에 따른 사용자 동작 변경에 맞게 조정할 수 있습니다. 탐색 **값은** 탐색으로 응답 되는 순위 호출의 비율을 결정 합니다.

이 값을 변경 하면 현재 Personalizer 모델을 다시 설정 하 고 지난 2 일간의 데이터를 다시 학습 합니다.

![탐색 값은 탐색으로 응답 되는 순위 호출의 비율을 결정 합니다.](media/settings/configure-exploration-setting.png)

이 값을 변경한 후에는 **저장**을 선택 해야 합니다.

<a name="model-update-frequency"></a>

## <a name="configure-model-update-frequency-for-model-training"></a>모델 학습을 위한 모델 업데이트 빈도 구성

**모델 업데이트 빈도** 는 모델을 학습 하는 빈도를 설정 합니다.

|빈도 설정|목적|
|--|--|
|1분|1 분 업데이트 빈도는 Personalizer를 사용 하 여 응용 프로그램 코드를 **디버깅** 하거나, 데모를 수행 하거나, 기계 학습 측면을 대화형으로 테스트할 때 유용 합니다.|
|15분|높은 모델 업데이트 빈도는 사용자 동작의 **변경 내용을 면밀** 하 게 추적 하려는 경우에 유용 합니다. 라이브 뉴스, 바이럴 콘텐츠 또는 라이브 상품 입찰에서 실행하는 사이트를 예로 들 수 있습니다. 이러한 시나리오에서 15분 빈도를 사용할 수 있습니다. |
|1시간|대부분 사용 사례의 경우 낮은 업데이트 빈도가 효과적입니다.|

![모델 업데이트 빈도는 새 Personalizer 모델을 다시 학습시키는 빈도를 설정합니다.](media/settings/configure-model-update-frequency-settings-15-minutes.png)

이 값을 변경한 후에는 **저장**을 선택 해야 합니다.

## <a name="data-retention"></a>데이터 보존

**데이터 보존 기간**은 Personalizer가 데이터 로그를 보관하는 기간(일)을 설정합니다. 과거 데이터 로그는 Personalizer의 효율성을 측정하고 학습 정책을 최적화하는 데 사용되는 [오프라인 평가](concepts-offline-evaluation.md)를 수행하는 데 필요합니다.

이 값을 변경한 후에는 **저장**을 선택 해야 합니다.

<a name="clear-data-for-your-learning-loop"></a>

## <a name="settings-that-include-resetting-the-model"></a>모델 재설정을 포함 하는 설정

다음 동작에는 지난 2 일간의 데이터를 사용 하 여 모델을 즉시 다시 학습 하는 작업이 포함 됩니다.

* 보상
* 탐색

모든 데이터를 [지우려면](how-to-manage-model.md) * * 모델 및 학습 설정 * * 페이지를 사용 합니다.

## <a name="next-steps"></a>다음 단계

[모델을 관리 하는 방법 알아보기](how-to-manage-model.md)
