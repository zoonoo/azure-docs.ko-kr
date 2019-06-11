---
title: Microsoft Translator Hub 작업 영역 및 프로젝트를 마이그레이션하려면? - Custom Translator
titleSuffix: Azure Cognitive Services
description: 허브 작업 영역 및 프로젝트를 Custom Translator로 마이그레이션합니다.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: v-pawal
ms.topic: conceptual
ms.openlocfilehash: 901b5312d216c241331b90a6b61d263437ab5c70
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389298"
---
# <a name="migrate-hub-workspace-and-projects-to-custom-translator"></a>허브 작업 영역 및 프로젝트를 Custom Translator로 마이그레이션

[Microsoft Translator Hub](https://hub.microsofttranslator.com/) 작업 영역 및 프로젝트를 Custom Translator로 쉽게 마이그레이션할 수 있습니다. 마이그레이션은 작업 영역 또는 프로젝트를 선택하고, Custom Translator에서 작업 영역을 선택한 다음, 전송하려는 학습을 선택하여 Microsoft Hub에서 시작됩니다. 마이그레이션이 시작된 후 선택한 학습 설정은 모든 관련 문서를 통해 전송됩니다. 배포된 모델은 학습되며 완료 시 자동 배포될 수 있습니다.

마이그레이션하는 중에 이러한 작업을 수행합니다.
* 모든 문서와 프로젝트 정의에는 이름에 "hub_" 접두사를 추가하여 전송된 해당 이름이 있습니다. 자동으로 생성된 테스트 및 튜닝 데이터는 hub_systemtune_\<modelid> 또는 hub_systemtest_\<modelid>라고 합니다.
* 마이그레이션을 수행하는 경우 배포된 상태에 있던 모든 학습을 Hub 학습 문서를 사용하여 자동으로 학습합니다. 이 학습은 구독에 대한 비용은 청구되지 않습니다. 마이그레이션에 대해 자동 배포가 선택된 경우 완료 시 학습된 모델을 배포합니다. 일반 호스팅 요금을 적용합니다.
* 배포 상태에 있지 않던 모든 마이그레이션된 학습은 마이그레이션된 초안 상태에 놓이게 됩니다. 이 상태에서는 마이그레이션된 정의를 사용하여 모델을 학습하는 옵션이 있지만 일반적인 학습 비용이 적용됩니다.
* 허브 학습에서 마이그레이션된 BLEU 점수는 언제든 "MT 허브의 Bleu 점수" 제목의 모델의 TrainingDetails 페이지에서 찾을 수 있습니다.

> [!Note]
> 성공 하는 학습 시에 사용자 지정 변환기 고유 추출 된 문장 10,000 개가 필요 합니다. 사용자 지정 변환기 보다 적은 학습을 수행할 수 없습니다는 [제안 된 최소](sentence-alignment.md#suggested-minimum-number-of-extracted-and-aligned-sentences)합니다.

## <a name="find-custom-translator-workspace-id"></a>Custom Translator 작업 영역 ID 찾기

[Microsoft Translator Hub](https://hub.microsofttranslator.com/) 작업 영역을 마이그레이션하려면 Custom Translator의 대상 작업 영역 ID가 필요합니다. Custom Translator의 대상 작업 영역은 모든 허브 작업 영역 및 프로젝트를 마이그레이션할 위치입니다.

Custom Translator 설정 페이지에서 대상 작업 영역 ID를 찾을 수 있습니다.

1. Custom Translator 포털에서 “설정” 페이지로 이동합니다.

2. 기본 정보 섹션에서 작업 영역 ID를 찾을 수 있습니다.

    ![대상 작업 영역 ID를 찾는 방법](media/how-to/how-to-find-destination-ws-id.png)

3. 마이그레이션 프로세스 중에 참조할 대상 작업 영역 ID를 적어둡니다.

## <a name="migrate-a-project"></a>프로젝트 마이그레이션

선택적으로 프로젝트를 마이그레이션하려는 경우 Microsoft Translator Hub는 해당 기능을 제공합니다.

프로젝트를 마이그레이션하려면

1. Microsoft Translator Hub에 로그인합니다.

2. “프로젝트” 페이지로 이동합니다.

3. 해당 프로젝트에서 “마이그레이션” 링크를 클릭합니다.

    ![허브에서 마이그레이션하는 방법](media/how-to/how-to-migrate-from-hub.png)

4. 마이그레이션 링크를 누르면 다음을 수행할 수 있는 양식이 표시됩니다.
   * Custom Translator로 전송하려는 작업 영역 지정
   * 성공적인 학습 또는 배포된 학습만 사용하여 모든 학습을 전송하려는지 여부를 나타냅니다. 기본적으로 모든 성공적인 학습을 전송합니다.
   * 학습이 완료되면 학습을 자동 배포하려는지 여부를 나타냅니다. 기본적으로 완료되면 학습을 자동으로 배포합니다.

5. “요청 제출”을 클릭합니다.

## <a name="migrate-a-workspace"></a>작업 영역 마이그레이션

단일 프로젝트를 마이그레이션하는 것 외에도 작업 영역에서 성공적인 학습을 사용하여 모든 프로젝트를 마이그레이션할 수도 있습니다. 이렇게 하면 마치 마이그레이션 링크를 누른 것처럼 작업 영역에서 각 프로젝트를 평가할 수 있습니다. 이 기능은 동일한 설정을 사용하여 Custom Translator로 모든 프로젝트를 마이그레이션하려는 여러 프로젝트가 있는 사용자에게 적합합니다. Translator Hub의 설정 페이지에서 작업 영역 마이그레이션을 시작할 수 있습니다.

작업 영역을 마이그레이션하려면

1. Microsoft Translator Hub에 로그인합니다.

2. “설정” 페이지로 이동합니다.

3. “설정” 페이지에서 “Custom Translator로 작업 영역 데이터 마이그레이션”을 클릭합니다.

    ![허브에서 마이그레이션하는 방법](media/how-to/how-to-migrate-workspace-from-hub.png)

4. 다음 페이지에서 다음과 같은 두 옵션 중 하나를 선택합니다.

    a. 배포된 학습 전용: 이 옵션을 선택하면 배포된 시스템 및 관련된 문서만 마이그레이션됩니다.

    b. 모든 성공적인 학습: 이 옵션을 선택하면 성공적인 학습 및 관련된 문서가 모두 마이그레이션됩니다.

    다. Custom Translator의 대상 작업 영역 ID를 입력합니다.

    ![허브에서 마이그레이션하는 방법](media/how-to/how-to-migrate-from-hub-screen.png)

5. 요청 제출을 클릭합니다.

## <a name="migration-history"></a>마이그레이션 기록

허브에서 작업 영역/프로젝트 마이그레이션을 요청한 경우 Custom Translator 설정 페이지에서 마이그레이션 기록을 찾을 수 있습니다.

마이그레이션 기록을 보려면 다음 단계를 수행합니다.

1. Custom Translator 포털에서 “설정” 페이지로 이동합니다.

2. 설정 페이지의 마이그레이션 기록 섹션에서 마이그레이션 기록을 클릭합니다.

    ![마이그레이션 기록](media/how-to/how-to-migration-history.png)

마이그레이션 기록 페이지에는 요청한 모든 마이그레이션에 대한 요약으로 다음 정보가 표시됩니다.

1. 마이그레이션한 사람: 이 마이그레이션 요청을 제출한 사용자의 이름 및 이메일

2. 마이그레이션한 날짜: 마이그레이션의 날짜 및 타임스탬프

3. 프로젝트: 마이그레이션에 대해 요청된 프로젝트 수 v/s 성공적으로 마이그레이션된 프로젝트 수

4. 학습: 마이그레이션에 대해 요청된 학습 수 v/s 성공적으로 마이그레이션된 학습 수

5. 문서: 마이그레이션에 대해 요청된 문서 수 v/s 성공적으로 마이그레이션된 문서 수

    ![마이그레이션 기록 세부 정보](media/how-to/how-to-migration-history-details.png)

프로젝트, 학습 및 문서에 대한 더 자세한 마이그레이션 보고서를 보려면 CSV로 세부 정보 내보내기 옵션이 있습니다.

## <a name="implementation-notes"></a>구현 참고 사항
* 아직 시스템 언어를 사용 하 여 쌍에서 사용할 수 있는 사용자 지정 변환기만 데이터에 액세스 하거나 사용자 지정 변환기를 통해 배포 취소에 사용할 수 있습니다. 이러한 프로젝트는 프로젝트 페이지에서 "사용할 수 없음"으로 표시 됩니다. 사용자 지정 변환기를 사용 하 여 새 언어 쌍의 사용 가능 해지면 프로젝트 시스템을 학습 하 고 배포에 대해 활성화 됩니다. 
* 허브에서 Custom Translator로 프로젝트를 마이그레이션하면 허브 학습 또는 프로젝트에 아무런 영향도 주지 않습니다. 마이그레이션하는 중에는 허브에서 프로젝트 또는 문서를 삭제하지 않으며 모델 배포를 취소하지 않습니다.
* 프로젝트당 한 번만 마이그레이션할 수 있습니다. 프로젝트에서 마이그레이션을 반복해야 하는 경우 당사에 연락하세요.
* 사용자 지정 변환기 NMT 언어 쌍와 영어를 지원합니다. [지원 되는 언어의 전체 목록을 보려면](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization)합니다. 허브는 기준 모델이 필요하지 않으므로 수천 개의 언어를 지원합니다. 지원되지 않는 언어 쌍을 마이그레이션할 수 있지만 문서 및 프로젝트 정의만 마이그레이션합니다. 새 모델을 학습할 수 없습니다. 또한 이러한 문서와 프로젝트를 지금은 사용할 수 없는 것으로 나타내려면 해당 문서와 프로젝트를 비활성으로 표시합니다. 이러한 프로젝트 및/또는 문서에 대한 지원이 추가되면 활성화되고 학습이 가능해집니다.
* Custom Translator는 현재 단일어 학습 데이터를 지원하지 않습니다. 지원되지 않는 언어 쌍처럼 단일어 문서를 마이그레이션할 수 있지만 단일어 데이터가 지원될 때까지는 비활성으로 표시합니다.
* 학습하려면 Custom Translator에는 10,000개의 병렬 문장이 필요합니다. Microsoft Hub는 더 작은 데이터 세트에 대해 학습할 수 있습니다. 이 요구 사항을 충족하지 않는 학습이 마이그레이션되는 경우 해당 요구 사항은 학습되지 않습니다.

## <a name="custom-translator-versus-hub"></a>Custom Translator 및 허브

이 표에서는 Microsoft Translator Hub와 및 Custom Translator 간의 기능을 비교합니다.

|   | 허브 | Custom Translator |
|:-----|:----:|:----:|
|사용자 지정 기능 상태   | 일반 공급  | 일반 공급 |
| Text API 버전  | V2    | V3  |
| SMT 사용자 지정 | 예   | 아닙니다. |
| NMT 사용자 지정 | 아닙니다.    | 예 |
| 새로운 통합 Speech Service 사용자 지정 | 아닙니다.    | 예 |
| 추적 없음 | 예 | 예 |

## <a name="new-languages"></a>새 언어

커뮤니티 또는 조직에서 Microsoft Translator에 대 한 새 언어 시스템을 만드는 작업 인 경우에 연락 [ custommt@microsoft.com ](mailto:custommt@microsoft.com) 자세한 내용은 합니다.

## <a name="next-steps"></a>다음 단계

- [모델을 학습합니다](how-to-train-model.md).
- [Microsoft Translator Text API V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl)을 통해 배포된 사용자 지정 번역 모델 사용을 시작합니다.
