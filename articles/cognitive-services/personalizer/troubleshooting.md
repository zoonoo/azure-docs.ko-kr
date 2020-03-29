---
title: 문제 해결 - 개인 설정
description: 이 문서에는 개인 설정에 대한 자주 묻는 문제 해결 질문에 대한 답변이 포함되어 있습니다.
ms.topic: troubleshooting
ms.date: 02/26/2020
ms.author: diberry
ms.openlocfilehash: 904953f028eb31afe42cf477ac05be43e8b72a4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336020"
---
# <a name="personalizer-troubleshooting"></a>개인 맞춤 문제 해결

이 문서에는 개인 설정에 대한 자주 묻는 문제 해결 질문에 대한 답변이 포함되어 있습니다.

## <a name="transaction-errors"></a>트랜잭션 오류

<details>
<summary><b>서비스에서 HTTP 429(너무 많은 요청) 응답을 받습니다. 무엇을 도와드릴까요?</b></summary>

**답변**: 개인 설정 인스턴스를 만들 때 무료 가격 계층을 선택한 경우 허용되는 Rank 요청 수에 대한 할당량 제한이 있습니다. 순위 API에 대한 API 호출 속도(개인 설정 리소스에 대한 Azure 포털의 메트릭 창에서)를 검토하고 통화 량이 선택한 가격 책정 계층의 임계값을 초과할 것으로 예상되는 경우 가격 책정 계층(가격 책정 계층 창)을 조정합니다.

</details>

<details>
<summary><b>랭크 또는 리워드 API에서 5xx 오류가 발생했습니다. 제가 뭘 해야 하나요?</b></summary>

**답변**: 이러한 문제는 투명해야 합니다. 계속하면 개인 설정 리소스에 대한 Azure 포털의 **지원 + 문제 해결** 섹션에서 새 지원 **요청을** 선택하여 지원팀에 문의합니다.

</details>

## <a name="learning-loop"></a>학습 루프

<details>
<summary>
<b>학습 루프는 개인화없이 시스템에 100 % 일치를 달성하지 않습니다. 이 문제를 해결하려면 어떻게 해야 합니까?</b></summary>

**답변**: 학습 루프로 목표를 달성하지 못하는 이유 :
* 랭크 API 호출로 전송된 기능이 충분하지 않음
* 전송된 기능의 버그-예: 타임스탬프와 같은 집계되지 않은 기능 데이터를 순위 API로 보내는 경우
* 반복 처리가 있는 버그 - 이벤트에 대한 보상 API에 보상 데이터를 보내지 않는 경우

수정하려면 루프로 전송된 기능을 변경하여 처리를 변경하거나 보상이 Rank 응답의 품질에 대한 올바른 평가인지 확인해야 합니다.

</details>

<details>
<summary>
<b>학습 루프는 학습하지 않는 것 같습니다. 이 문제를 해결하려면 어떻게 해야 합니까?</b></summary>

**답변**: 랭크 호출이 효과적으로 우선 순위를 지정하기 전에 학습 루프에는 수천 개의 보상 호출이 필요합니다.

학습 루프가 현재 어떻게 작동하는지 잘 모르는 경우 [오프라인 평가를](concepts-offline-evaluation.md)실행하고 수정된 학습 정책을 적용합니다.

</details>

<details>
<summary><b>모든 항목에 대해 동일한 확률로 순위 결과를 계속 얻습니다. Personalizer가 학습중인지 어떻게 알 수 있습니까?</b></summary>

**답변**: Personalizer는 방금 시작하고 _빈_ 모델이 있거나 개인 설정 루프를 재설정할 때 Windows API 결과에서 동일한 확률을 반환하며 모델은 **여전히 모델 업데이트 빈도** 기간 내에 있습니다.

새 업데이트 기간이 시작되면 업데이트된 모델이 사용되고 확률이 변경됩니다.

</details>

<details>
<summary><b>학습 루프는 학습했지만 더 이상 배우지 않는 것 같고 순위 결과의 품질은 좋지 않습니다. 제가 뭘 해야 하나요?</b></summary>

**대답**:
* 해당 개인 설정 리소스(학습 루프)에 대해 Azure 포털에서 하나의 평가를 완료하고 적용했는지 확인합니다.
* 모든 보상이 리워드 API를 통해 전송되고 처리되는지 확인합니다.

</details>


<details>
<summary><b>학습 루프가 정기적으로 업데이트되고 내 데이터를 점수 매기는 데 사용된다는 것을 어떻게 알 수 있습니까?</b></summary>

**답변**: Azure 포털의 모델 및 학습 **설정** 페이지에서 모델이 마지막으로 업데이트된 시간을 확인할 수 있습니다. 이전 타임스탬프가 표시되면 랭크 및 리워드 호출을 보내지 않기 때문일 수 있습니다. 서비스에 들어오는 데이터가 없는 경우 학습을 업데이트하지 않습니다. 학습 루프가 자주 업데이트되지 않는 경우 루프의 모델 업데이트 **빈도를**편집할 수 있습니다.

</details>

## <a name="offline-evaluations"></a>오프라인 평가

<details>
<summary><b>오프라인 평가의 기능 중요도는 수백 또는 수천 개의 항목이 있는 긴 목록을 반환합니다. 무슨 일이 있었나요?</b></summary>

**답변**: 이것은 일반적으로 타임 스탬프, 사용자 번호 또는 전송 된 다른 미세 한 그레인 된 기능 때문입니다.

</details>

<details>
<summary><b>나는 오프라인 평가를 만들어 거의 즉시 성공했다. 왜일까요? 결과가 표시되지 않습니까?</b></summary>

**답변**: 오프라인 평가는 해당 기간의 이벤트에서 학습된 모델 데이터를 사용합니다. 평가의 시작 시간과 종료 시간 사이의 기간에 데이터를 보내지 않은 경우 결과 없이 완료됩니다. 개인 설정에 전송된 이벤트가 있는 시간 범위를 선택하여 새 오프라인 평가를 제출합니다.

</details>


## <a name="learning-policy"></a>학습 정책

<details>
<summary><b>학습 정책을 가져오면 어떻게 해야 합니까?</b></summary>

**답변**: [학습 정책 개념](concept-active-learning.md#understand-learning-policy-settings) 및 새로운 학습 정책을 적용하는 [방법에](how-to-manage-model.md) 대해 자세히 알아봅니다. 학습 정책을 선택하지 않으려면 [오프라인 평가를](how-to-offline-evaluation.md) 사용하여 현재 이벤트를 기반으로 학습 정책을 제안할 수 있습니다.

</details>

## <a name="security"></a>보안

<details>
<summary><b>내 루프의 API 키가 손상되었습니다. 무엇을 도와드릴까요?</b></summary>

**답변**: 다른 키를 사용하도록 클라이언트를 교환 한 후 하나의 키를 다시 생성 할 수 있습니다. 두 개의 키를 사용하면 가동 중지 시간을 갖지 않고도 지연 방식으로 키를 전파할 수 있습니다. 보안 조치로 정기적으로 이 작업을 수행하는 것이 좋습니다.

</details>

## <a name="next-steps"></a>다음 단계

[모델 업데이트 빈도 구성](how-to-settings.md#model-update-frequency)