---
title: 문제 해결-Personalizer
description: 이 문서에는 Personalizer에 대 한 자주 묻는 질문에 대 한 답변이 포함 되어 있습니다.
ms.topic: troubleshooting
ms.date: 02/26/2020
ms.openlocfilehash: 55b9068dbd7bfbb3ea3972c3381fda6d9cde5076
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87126111"
---
# <a name="personalizer-troubleshooting"></a>Personalizer 문제 해결

이 문서에는 Personalizer에 대 한 자주 묻는 질문에 대 한 답변이 포함 되어 있습니다.

## <a name="configuration-issues"></a>구성 문제

### <a name="i-changed-a-configuration-setting-and-now-my-loop-isnt-performing-at-the-same-learning-level-what-happened"></a>구성 설정을 변경 했 고 이제 루프가 동일한 학습 수준에서 수행 되지 않습니다. 무슨 일이 일어났나요?

일부 구성 설정은 [모델을 다시 설정](how-to-settings.md#settings-that-include-resetting-the-model)합니다. 구성 변경 내용을 신중 하 게 계획 해야 합니다.

### <a name="when-configuring-personalizer-with-the-api-i-received-an-error-what-happened"></a>API를 사용 하 여 Personalizer를 구성 하는 경우 오류가 발생 합니다. 무슨 일이 일어났나요?

단일 API 요청을 사용 하 여 서비스를 구성 하 고 학습 동작을 변경 하는 경우 오류가 발생 합니다. 두 개의 별도 API 호출을 수행 해야 합니다. 먼저 서비스를 구성한 다음 학습 동작을 전환 합니다.

## <a name="transaction-errors"></a>트랜잭션 오류

### <a name="i-get-an-http-429-too-many-requests-response-from-the-service-what-can-i-do"></a>서비스에서 HTTP 429 (너무 많은 요청) 응답을 받습니다. 어떻게 해야 하나요?

Personalizer 인스턴스를 만들 때 무료 가격 책정 계층을 선택한 경우 허용 되는 순위 요청 수에 대 한 할당량 한도가 있습니다. Personalizer 리소스에 대 한 Azure Portal의 메트릭 창에서 순위 API에 대 한 API 호출 요금을 검토 하 고, 통화 볼륨이 선택한 가격 책정 계층의 임계값을 초과 하 여 증가할 것으로 예상 되는 경우 가격 책정 계층 (가격 책정 계층 창에서)을 조정 합니다.

### <a name="im-getting-a-5xx-error-on-rank-or-reward-apis-what-should-i-do"></a>순위 또는 보상 Api에 대 한 5xx 오류가 발생 합니다.   어떻게 해야 합니까?

이러한 문제는 투명 해야 합니다. 계속 하는 경우 Personalizer 리소스에 대 한 Azure Portal **지원 + 문제 해결** 섹션에서 **새 지원 요청** 을 선택 하 여 지원 담당자에 게 문의 하세요.

## <a name="learning-loop"></a>학습 루프

### <a name="the-learning-loop-doesnt-attain-a-100-match-to-the-system-without-personalizer-how-do-i-fix-this"></a>학습 루프가 Personalizer 없이 시스템에 100% 일치 하지 않습니다. 이 문제를 해결하려면 어떻게 해야 합니까?

학습 루프를 사용 하 여 목표를 달성 하지 못한 이유는 다음과 같습니다.
* Rank API 호출로 전송 된 기능이 부족 합니다.
* 전송 된 기능의 버그 (예: 타임 스탬프와 같은 집계할 수 없는 기능 데이터를 순위 API에 보내기)
* 이벤트에 대 한 보상 API에 보상 데이터를 전송 하지 않는 등 루프 처리를 사용 하는 버그

이 문제를 해결 하려면 루프로 전송 된 기능을 변경 하 여 처리를 변경 하거나 보상의 응답 품질을 정확 하 게 평가 해야 합니다.

### <a name="the-learning-loop-doesnt-seem-to-learn-how-do-i-fix-this"></a>학습 루프가 학습 되지 않는 것 같습니다. 이 문제를 해결하려면 어떻게 해야 합니까?

학습 루프는 순위 호출에 효과적으로 우선 순위를 지정 하기 전에 몇 천 개의 보상 호출이 필요 합니다.

현재 학습 루프가 어떻게 동작 하는지 확실 하지 않은 경우 [오프 라인 평가](concepts-offline-evaluation.md)를 실행 하 고 수정 된 학습 정책을 적용 합니다.

### <a name="i-keep-getting-rank-results-with-all-the-same-probabilities-for-all-items-how-do-i-know-personalizer-is-learning"></a>모든 항목에 대해 동일한 확률을 모두 사용 하 여 순위 결과를 계속 가져옵니다. Personalizer 알고? 어떻게 할까요?

Personalizer는 방금 시작 되 고 _빈_ 모델이 있거나, Personalizer 루프를 다시 설정 하 고 모델이 **모델 업데이트 주기** 기간 내에 있는 경우 Rank API 결과에서 동일한 확률을 반환 합니다.

새 업데이트 기간이 시작 되 면 업데이트 된 모델이 사용 되며, 확률 변경이 표시 됩니다.

### <a name="the-learning-loop-was-learning-but-seems-to-not-learn-anymore-and-the-quality-of-the-rank-results-isnt-that-good-what-should-i-do"></a>학습 루프가 학습 중이지만 더 이상 잘 모르는 것 같지만 순위 결과의 품질은 적절 하지 않습니다.   어떻게 해야 합니까?

* 해당 Personalizer 리소스 (학습 루프)에 대해 Azure Portal 한 번에 하나의 평가를 완료 하 고 적용 했는지 확인 합니다.
* 보상 API를 통해 모든 보상을 보내고 처리 했는지 확인 합니다.

### <a name="how-do-i-know-that-the-learning-loop-is-getting-updated-regularly-and-is-used-to-score-my-data"></a>학습 루프가 정기적으로 업데이트 되 고 데이터의 점수를 매기는 데 사용 되는 어떻게 할까요??

Azure Portal의 **모델 및 학습 설정** 페이지에서 모델이 마지막으로 업데이트 된 시간을 확인할 수 있습니다. 이전 타임 스탬프가 표시 되는 경우 순위 및 보상 호출을 보내지 않기 때문일 수 있습니다. 서비스에 들어오는 데이터가 없으면 학습이 업데이트 되지 않습니다. 학습 루프가 자주 업데이트 되지 않는 것으로 나타나는 경우 루프의 **모델 업데이트 빈도**를 편집할 수 있습니다.

## <a name="offline-evaluations"></a>오프라인 평가

### <a name="an-offline-evaluations-feature-importance-returns-a-long-list-with-hundreds-or-thousands-of-items-what-happened"></a>오프 라인 평가의 기능 중요도는 수백 또는 수천 개의 항목을 포함 하는 긴 목록을 반환 합니다. 무슨 일이 일어났나요?

이는 일반적으로 타임 스탬프, 사용자 Id 또는 기타 일부 세분화 된 기능으로 인해 발생 합니다.

### <a name="i-created-an-offline-evaluation-and-it-succeeded-almost-instantly-why-is-that-i-dont-see-any-results"></a>오프 라인 평가를 만들었으며 거의 즉시 성공 했습니다. 그 이유는 무엇입니까? 결과가 표시 되지 않나요?

오프 라인 평가는 해당 기간의 이벤트에서 학습 된 모델 데이터를 사용 합니다. 계산 시작 시간과 종료 시간 사이의 시간에 데이터를 보내지 않은 경우 결과 없이 완료 됩니다. Personalizer로 보내진 이벤트가 있는 시간 범위를 선택 하 여 새 오프 라인 평가를 제출 합니다.

## <a name="learning-policy"></a>학습 정책

### <a name="how-do-i-import-a-learning-policy"></a>학습 정책을 가져올 어떻게 할까요? 있나요?

[학습 정책 개념](concept-active-learning.md#understand-learning-policy-settings) 및 새로운 학습 정책을 [적용 하는 방법](how-to-manage-model.md) 에 대해 자세히 알아보세요. 학습 정책을 선택 하지 않으려면 현재 이벤트를 기반으로 하 여 학습 정책을 제안 하기 위해 [오프 라인 평가](how-to-offline-evaluation.md) 를 사용할 수 있습니다.


## <a name="security"></a>보안

### <a name="the-api-key-for-my-loop-has-been-compromised-what-can-i-do"></a>내 루프의 API 키가 손상 되었습니다. 어떻게 해야 하나요?

다른 키를 사용 하도록 클라이언트를 바꾼 후 한 키를 다시 생성할 수 있습니다. 키가 두 개인 경우 가동 중지 시간이 없어도 지연 방식으로 키를 전파할 수 있습니다. 보안 조치로 정기적으로이 작업을 수행 하는 것이 좋습니다.


## <a name="next-steps"></a>다음 단계

[모델 업데이트 빈도 구성](how-to-settings.md#model-update-frequency)