---
title: 학습 동작 구성
description: 수련 여는 Personalizer 서비스 및 해당 기계 학습 기능을 안정적으로 제공 하 고 온라인 트래픽 위험 없이 서비스에서 확인할 수 있는 정보를 전송 하는 메트릭을 제공 합니다.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 05/01/2020
ms.openlocfilehash: 10e98cd2f0ad4793aa43f9bb3316c522b44f1d2c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91303542"
---
# <a name="configure-the-personalizer-learning-behavior"></a>Personalizer 학습 동작 구성

[수련](concept-apprentice-mode.md) 여는 Personalizer 서비스 및 해당 기계 학습 기능에서 신뢰와 신뢰도를 제공 하 고, 온라인 트래픽 위험 없이 서비스에 제공 되는 정보를 받을 수 있도록 보장 합니다.

[!INCLUDE [Important Blue Box - Apprentice mode pricing tier](./includes/important-apprentice-mode.md)]

## <a name="configure-apprentice-mode"></a>수련 여 모드 구성

1. Personalizer 리소스에 대 한 [Azure Portal](https://portal.azure.com)에 로그인 합니다.

1. **구성** 페이지의 **학습 동작** 탭에서 **기본 작업 반환을 선택 하 고, 수련 수련으로 학습** 한 후 **저장**을 선택 합니다.

> [!div class="mx-imgBorder"]
> ![Azure Portal에서 수련 생 모드 학습 동작을 구성 하는 스크린샷](media/settings/configure-learning-behavior-azure-portal.png)

## <a name="changes-to-the-existing-application"></a>기존 응용 프로그램에 대 한 변경 내용

기존 응용 프로그램은 현재 표시 되는 작업을 선택 하거나 응용 프로그램에서 해당 작업의 **보상** 값을 확인 하는 방법을 변경 하지 않아야 합니다. 응용 프로그램에 대 한 유일한 변경 내용은 Personalizer의 Rank API로 전송 되는 작업의 순서 일 수 있습니다. 응용 프로그램이 현재 표시 하는 작업은 작업 목록의 _첫 번째 작업_ 으로 전송 됩니다. [RANK API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank) 는이 첫 번째 작업을 사용 하 여 Personalizer 모델을 학습 합니다.

### <a name="configure-your-application-to-call-the-rank-api"></a>Rank API를 호출 하도록 응용 프로그램 구성

응용 프로그램에 Personalizer를 추가 하려면 순위 및 보상 Api를 호출 해야 합니다.

1. 작업 및 해당 기능 목록을 결정 하는 기존 응용 프로그램 논리의 시점 뒤에 [순위 API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank) 호출을 추가 합니다. 작업 목록의 첫 번째 작업은 기존 논리에서 선택한 작업 이어야 합니다.

1. Rank API 응답의 **보상 동작 ID**와 연결 된 작업을 표시 하도록 코드를 구성 합니다.

### <a name="configure-your-application-to-call-reward-api"></a>보상 API를 호출 하도록 응용 프로그램 구성

1. 기존 비즈니스 논리를 사용 하 여 표시 된 작업의 **보상** 을 계산할 수 있습니다. 값은 0에서 1 사이의 범위에 있어야 합니다. [보상 API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward)를 사용 하 여 Personalizer에이 보상을 보냅니다. 보상 값은 즉시 예상 되지 않으며 비즈니스 논리에 따라 일정 시간 동안 지연 될 수 있습니다.

1. 구성 된 **보상 대기 시간**내에 보상을 반환 하지 않으면 기본 보상을 대신 사용 합니다.

## <a name="evaluate-apprentice-mode"></a>수련 생 모드 평가

Azure Portal의 Personalizer 리소스에 대 한 **평가** 페이지에서 **현재 학습 동작 성능을**검토 합니다.

> [!div class="mx-imgBorder"]
> ![Azure Portal에서 수련 생 모드 학습 동작의 평가를 검토 하는 스크린샷](media/settings/evaluate-apprentice-mode.png)

수련 여 모드는 다음과 같은 **평가 메트릭을**제공 합니다.
* **기준선 – 평균 보상**: 응용 프로그램의 기본 (기준선)의 평균 보상입니다.
* **Personalizer – 평균 보상**: 총 보상 Personalizer의 평균에 도달 했을 가능성이 있습니다.
* 최근 **1000 이벤트에 대 한 보상 성과 비율**: 최신 1000 이벤트에 대해 기준선 및 Personalizer 보상의 비율입니다.

## <a name="evaluate-apprentice-mode-features"></a>수련 생 모드 기능 평가

[오프 라인 평가](how-to-offline-evaluation.md)를 사용 하 여 기능을 평가 합니다.

## <a name="switch-behavior-to-online-mode"></a>온라인 모드로 동작 전환

평균 75-85% 이동 평균을 사용 하 여 Personalizer를 학습 하는 경우 모델은 온라인 모드로 전환할 준비가 된 것입니다.

Personalizer 리소스에 대 한 Azure Portal **구성** 페이지의 **학습 동작** 탭에서 **최상의 작업 반환** 을 선택한 다음 **저장**을 선택 합니다.

Rank 및 보상 API 호출을 변경할 필요는 없습니다.

## <a name="next-steps"></a>다음 단계

* [모델 및 학습 설정 관리](how-to-manage-model.md)
