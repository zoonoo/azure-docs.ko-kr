---
title: 설정 구성
titleSuffix: Azure Cognitive Services
description: 서비스 구성에는 서비스의 보상 처리 방법, 서비스의 탐색 빈도, 모델을 다시 학습하는 빈도 및 저장할 데이터의 양이 포함됩니다.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: 0cd08e1191c68c57975d3e68648134925155e7f2
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026687"
---
# <a name="personalizer-settings"></a>Personalizer 설정

서비스 구성에는 서비스의 보상 처리 방법, 서비스의 탐색 빈도, 모델을 다시 학습하는 빈도 및 저장할 데이터의 양이 포함됩니다.

각 피드백 루프에 대한 Personalizer 리소스를 만듭니다. 

## <a name="configure-service-settings-in-the-azure-portal"></a>Azure Portal에서 서비스 설정 구성

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Personalizer 리소스를 찾습니다. 
1. **리소스 관리** 섹션에서 **설정**을 선택합니다.

    ![Azure 포털에 로그인합니다. Personalizer 리소스를 찾습니다. [리소스 관리] 섹션에서 [설정]을 선택합니다.](media/settings/how-to-configure.png)

### <a name="reward-settings-for-the-feedback-loop"></a>피드백 루프에 대한 보상 설정

피드백 루프의 보상 사용에 대한 서비스 설정을 구성합니다. 다음 설정을 변경하면 현재 Personalizer 모델이 초기화되고 최근 2일의 데이터로 재학습됩니다.

![피드백 루프에 대한 보상 설정 구성](media/settings/configure-model-reward-settings.png)

|설정|목적|
|--|--|
|보상 대기 시간|Personalizer가 순위 호출에 대한 보상 값을 수집하는 시간 길이를 설정하며, 순위 호출이 발생하는 순간부터 시작합니다. 이 값은 "Personalizer가 보상 호출을 얼마나 오래 기다려야 하나요?"라는 질문에 대한 답변에 따라 설정됩니다. 이 기간 후에 도착하는 보상은 기록되지만 학습에 사용되지는 않습니다.|
|기본 보상|순위 호출과 연결된 보상 대기 시간 동안 Personalizer가 보상 호출을 하나도 받지 않은 경우 Personalizer는 기본 보상을 할당합니다. 기본적으로 대부분의 시나리오에서 기본 보상은 0입니다.|
|보상 집계|동일한 순위 API 호출에 대해 여러 보상이 수신되는 경우 집계 메서드 **sum** 또는 **earliest**가 사용됩니다. Earliest는 가장 먼저 받은 점수를 선택하고 나머지 점수를 무시합니다. 중복 가능성이 있는 여러 호출 중에 고유한 보상을 원하는 경우에 유용합니다. |

이 설정을 변경한 후 꼭 **저장**을 선택해야 합니다.

### <a name="exploration-setting"></a>탐색 설정 

개인 설정은 대안을 탐색하여 새 패턴을 검색하고 사용자 동작 변경에 맞게 조정할 수 있습니다. **탐색** 설정은 탐색을 통해 답변되는 순위 호출의 비율을 결정합니다. 

이 설정을 변경하면 현재 Personalizer 모델이 초기화되고 최근 2일의 데이터로 재학습됩니다.

![탐색 설정은 탐색을 통해 답변되는 순위 호출의 비율을 결정합니다.](media/settings/configure-exploration-setting.png)

이 설정을 변경한 후 꼭 **저장**을 선택해야 합니다.

### <a name="model-update-frequency"></a>모델 업데이트 빈도

**모델 업데이트 빈도**는 새 Personalizer 모델을 다시 학습시키는 빈도를 설정합니다. 

![모델 업데이트 빈도는 새 Personalizer 모델을 다시 학습시키는 빈도를 설정합니다.](media/settings/configure-model-update-frequency-settings.png)

이 설정을 변경한 후 꼭 **저장**을 선택해야 합니다.

### <a name="data-retention"></a>데이터 보존

**데이터 보존 기간**은 Personalizer가 데이터 로그를 보관하는 기간(일)을 설정합니다. 과거 데이터 로그는 Personalizer의 효율성을 측정하고 학습 정책을 최적화하는 데 사용되는 [오프라인 평가](concepts-offline-evaluation.md)를 수행하는 데 필요합니다.

이 설정을 변경한 후 꼭 **저장**을 선택해야 합니다.

## <a name="export-the-personalizer-model"></a>Personalizer 모델 내보내기

**모델 및 정책**의 리소스 관리 섹션에서 모델 생성 및 마지막 업데이트 날짜를 검토하고 현재 모델을 내보냅니다.

![현재 Personalizer 모델 내보내기](media/settings/export-current-personalizer-model.png)

## <a name="import-and-export-learning-policy"></a>학습 정책 가져오기 및 내보내기

**모델 및 정책**의 리소스 관리 섹션에서 새 학습 정책을 가져오거나 현재 학습 정책을 내보냅니다.

## <a name="next-steps"></a>다음 단계

[보충 학습](concepts-reinforcement-learning.md) 
