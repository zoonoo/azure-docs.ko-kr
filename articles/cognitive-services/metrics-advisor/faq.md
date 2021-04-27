---
title: Metrics Advisor 질문과 대답
titleSuffix: Azure Cognitive Services
description: Metrics Advisor 서비스에 대한 질문과 대답입니다.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: mbullwin
ms.openlocfilehash: 0c4c296cb1454ed89eef102732533589b1c8ca0d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "93420962"
---
# <a name="metrics-advisor-frequently-asked-questions"></a>Metrics Advisor 질문과 대답

### <a name="what-is-the-cost-of-my-instance"></a>내 인스턴스 비용은 얼마인가요?

현재 미리 보기 중에는 인스턴스를 사용하는 데 비용이 들지 않습니다.

### <a name="why-cant-i-create-the-resource-the-pricing-tier-is-unavailable-and-it-says-you-have-already-created-1-s0-for-this-subscription"></a>리소스를 만들 수 없는 이유는 무엇인가요? "가격 책정 계층"을 사용할 수 없으며 "이미 이 구독에 대해 S0 1개를 만들었습니다."라는 메시지가 표시됩니다.

:::image type="content" source="media/pricing.png" alt-text="F0 리소스가 이미 있는 경우의 메시지":::

공개 미리 보기 중에는 구독에서 Metrics Advisor 인스턴스를 지역당 하나만 만들 수 있습니다.

동일한 구독을 사용하여 동일한 지역에서 만든 인스턴스가 이미 있는 경우 다른 지역 또는 다른 구독을 통해 새 인스턴스를 만들 수 있습니다. 기존 인스턴스를 삭제하여 새 인스턴스를 만들 수도 있습니다.

기존 인스턴스를 이미 삭제했지만 오류가 계속 표시되는 경우 새 인스턴스를 만들려면 먼저 리소스를 삭제한 후 20분 정도 기다려야 합니다.

## <a name="basic-concepts"></a>기본 개념

### <a name="what-is-multi-dimensional-time-series-data"></a>다차원 시계열 데이터란 무엇인가요?

용어집에서 [다차원 메트릭](glossary.md#multi-dimensional-metric) 정의를 참조하세요.

### <a name="how-much-data-is-needed-for-metrics-advisor-to-start-anomaly-detection"></a>Metrics Advisor에서 변칙 검색을 시작하려면 얼마나 많은 데이터가 필요한가요?

적어도 하나의 데이터 포인트가 있어야 변칙 검색을 트리거할 수 있습니다. 그러나 최상의 정확도가 제공되지는 않습니다. 이 서비스는 데이터 피드를 만드는 동안 "채우기 간격" 규칙으로 지정한 값을 사용하여 이전 데이터 포인트의 기간을 가정합니다.

검색하려는 타임스탬프 이전의 데이터를 일부 포함하는 것이 좋습니다.
데이터의 세분성에 따라 권장되는 데이터 양은 아래와 같이 다양합니다.

| 세분성 | 검색에 권장되는 데이터 양 |
| ----------- | ------------------------------------- |
| 5분 미만 | 4일간의 데이터 |
| 5분~1일 | 28일간의 데이터 |
| 2일 이상, 31일 이하 | 4년간의 데이터 |
| 31일 초과 | 48년간의 데이터 |

### <a name="why-metrics-advisor-doesnt-detect-anomalies-from-historical-data"></a>Metrics Advisor가 기록 데이터에서 변칙을 검색하지 않는 이유는 무엇인가요?

Metrics Advisor는 라이브 스트리밍 데이터를 검색하도록 설계되었습니다. 서비스가 과거의 변칙 검색을 실행하는 기록 데이터의 최대 길이에는 제한이 있습니다. 즉, 가장 빠른 특정 타임스탬프 이후의 데이터 포인트만 변칙 검색 결과를 갖게 됩니다. 가장 빠른 타임스탬프는 데이터의 세분성에 따라 달라집니다.

데이터의 세분성에 따라 변칙 검색 결과를 갖는 기록 데이터의 길이는 아래와 같습니다.

| 세분성 | 변칙 검색에 대한 기록 데이터의 최대 길이 |
| ----------- | ------------------------------------- |
| 5분 미만 | 온보드 시간 ~ 13시간 |
| 5분 ~ 1시간 미만 | 온보드 시간 ~ 4일  |
| 1시간 ~ 1일 미만 | 온보드 시간 ~ 14일  |
| 1일 | 온보드 시간 - 28일  |
| 1일 초과, 31일 미만 | 온보드 시간 ~ 2년  |
| 31일 초과 | 온보드 시간 - 24년   |

### <a name="more-concepts-and-technical-terms"></a>추가 개념 및 기술 용어

자세한 내용은 [용어집](glossary.md)을 참조하세요.

###  <a name="how-do-i-write-a-valid-query-for-ingesting-my-data"></a>데이터 수집을 위해 올바른 쿼리를 작성하려면 어떻게 해야 하나요?  

Metrics Advisor가 데이터를 수집하려면 단일 타임스탬프에서 데이터 차원을 반환하는 쿼리를 만들어야 합니다. Metrics Advisor는 이 쿼리를 여러 번 실행하여 각 타임스탬프에서 데이터를 가져옵니다. 

지정된 타임스탬프에서 쿼리는 각 차원 조합에 대해 최대 하나의 레코드를 반환해야 합니다. 반환된 모든 레코드의 타임스탬프는 동일해야 합니다. 쿼리에서 반환되는 레코드에는 중복 레코드가 없어야 합니다.

예를 들어 일별 메트릭에 대해 아래 쿼리를 만든다고 가정합니다. 
 
`select timestamp, city, category, revenue from sampledata where Timestamp >= @StartTime and Timestamp < dateadd(DAY, 1, @StartTime)`

시계열에 대해 정확한 세분성을 사용해야 합니다. 시간별 메트릭에 대해 다음을 사용합니다. 

`select timestamp, city, category, revenue from sampledata where Timestamp >= @StartTime and Timestamp < dateadd(hour, 1, @StartTime)`

이러한 쿼리는 단일 타임스탬프의 데이터만 반환하며 Metrics Advisor가 수집하는 모든 차원 조합을 포함합니다. 

:::image type="content" source="media/query-result.png" alt-text="단일 타임스탬프의 쿼리 결과" lightbox="media/query-result.png":::


### <a name="how-do-i-detect-spikes--dips-as-anomalies"></a>급증 및 급락을 변칙으로 검색하려면 어떻게 해야 하나요?

하드 임계값을 미리 정의한 경우 [변칙 검색 구성](how-tos/configure-metrics.md#anomaly-detection-methods)에서 "하드 임계값"을 실제 수동으로 설정할 수 있습니다.
임계값이 없는 경우 AI로 구동되는 "스마트 검색"을 사용할 수 있습니다. 자세한 내용은 [검색 구성 조정](how-tos/configure-metrics.md#tune-the-detecting-configuration)을 참조하세요.

### <a name="how-do-i-detect-inconformity-with-regular-seasonal-patterns-as-anomalies"></a>정기적인(계절적인) 패턴을 변칙으로 사용하여 불일치를 검색하려면 어떻게 해야 하나요?

"스마트 검색"은 계절적인 패턴을 포함한 데이터 패턴을 학습할 수 있습니다. 그런 다음 정기적인 패턴을 따르지 않는 데이터 포인트를 변칙으로 검색합니다. 자세한 내용은 [검색 구성 조정](how-tos/configure-metrics.md#tune-the-detecting-configuration)을 참조하세요.

### <a name="how-do-i-detect-flat-lines-as-anomalies"></a>플랫 선을 변칙으로 검색하려면 어떻게 해야 하나요?

데이터가 일반적으로 상당히 불안정하고 변동이 심하여 너무 안정적이거나 심지어 플랫 선이 될 때 경고를 받으려는 경우, 변경이 너무 작을 때 이러한 데이터 포인트를 검색하도록 "변경 임계값"을 구성할 수 있습니다.
자세한 내용은 [변칙 검색 구성](how-tos/configure-metrics.md#anomaly-detection-methods)을 참조하세요.

### <a name="how-to-set-up-email-settings-and-enable-alerting-by-email"></a>이메일 설정을 지정하고 이메일로 경고를 받도록 설정하는 방법

1.  구독 관리자 또는 리소스 그룹 관리자 권한이 있는 사용자는 Azure Portal에서 만든 Metrics Advisor 리소스로 이동하여 **액세스 제어(IAM)** 탭을 선택해야 합니다. 
2.  **역할 할당 추가** 를 선택합니다.
3.  **Cognitive Services Metrics Advisor 관리자** 역할을 선택하고 아래 이미지와 같이 계정을 선택합니다.
4.  **저장** 단추를 클릭하면 Metrics Advisor 리소스의 관리자로 추가됩니다. 위의 모든 작업은 구독 관리자 또는 리소스 그룹 관리자가 수행해야 합니다. 

:::image type="content" source="media/access-control.png" alt-text="역할 할당 추가가 선택된 액세스 제어(IAM) 메뉴 페이지, 이어서 Cognitive Services Metrics Advisor 관리자의 액세스 역할로 표시된 선택한 사용자에게 액세스 권한을 할당하는 상자, 이어서 사용자를 검색하고 특정 수준의 액세스 권한을 추가하는 단계를 설명하기 위해 선택된 UI의 저장 단추" lightbox="media/access-control.png":::


5.  권한을 전파하는 데 최대 1분 정도 걸릴 수 있습니다. 그런 다음 Metrics Advisor 작업 영역을 선택하고 왼쪽 탐색 패널에서 **이메일 설정** 옵션을 선택합니다. 필수 항목(특히 SMTP 관련 정보)을 입력합니다. 
6.  **저장** 을 선택하면 이메일 구성 설정이 완료됩니다. 새 후크를 만들고 거의 실시간으로 경고를 받을 수 있도록 메트릭 변칙을 구독할 수 있습니다. 

## <a name="advanced-concepts"></a>고급 개념

### <a name="how-does-metric-advisor-build-an-incident-tree-for-multi-dimensional-metrics"></a>Metric Advisor는 다차원 메트릭에 대한 인시던트 트리를 어떻게 구축하나요?

메트릭은 차원을 통해 여러 시계열로 분할될 수 있습니다. 예를 들어 메트릭 `Response latency`은 팀에서 소유하는 모든 서비스에 대해 모니터링됩니다. `Service` 범주는 메트릭을 보강하는 차원으로 사용될 수 있으므로 `Response latency`이 `Service1`, `Service2` 등으로 분할됩니다. 각 서비스는 여러 데이터 센터의 여러 컴퓨터에 배포될 수 있으므로 메트릭은 `Machine` 및 `Data center`로 추가로 분할될 수 있습니다.

|서비스| 데이터 센터| 컴퓨터  | 
|----|------|----------------   |
| S1 |  DC1 |   M1 |
| S1 |  DC1 |   M2 |
| S1 |  DC2 |   M3 |
| S1 |  DC2 |   M4 |
| S2 |  DC1 |   M1 |
| S2 |  DC1 |   M2 |
| S2 |  DC2 |   M5 |
| S2 |  DC2 |   M6 |
| ...|      |      |

총 `Response latency`에서 시작하여 `Service`, `Data center` 및 `Machine`별로 메트릭을 드릴다운할 수 있습니다. 그러나 서비스 소유자가 `Service` -> `Data center` -> `Machine` 경로를 사용하거나 인프라 엔지니어가 `Data Center` -> `Machine` -> `Service` 경로를 사용하는 것이 더 합리적일 수도 있습니다. 이는 사용자의 개별 비즈니스 요구 사항에 따라 달라집니다. 

Metric Advisor에서 사용자는 계층 토폴로지의 한 노드에서 드릴다운하거나 롤업할 경로를 지정할 수 있습니다. 더 정확하게 말하면, 계층 토폴로지는 트리 구조가 아니라 방향성 비순환 그래프입니다. 다음과 같이 모든 잠재적 차원 조합으로 구성된 전체 계층 토폴로지가 있습니다. 

:::image type="content" source="media/dimension-combinations-view.png" alt-text="숫자 범위 1~6에 해당하는 S, DC 및 M 레이블이 지정된 여러 차원에서 여러 상호 연결 꼭짓점과 에지로 구성된 계층적 토폴로지 다이어그램" lightbox="media/dimension-combinations-view.png":::

이론적으로, 차원 `Service`에 `Ls` 고유 값, 차원 `Data center`에 `Ldc` 고유 값, 차원 `Machine`에 `Lm` 고유 값이 있는 경우 계층 토폴로지에는 `(Ls + 1) * (Ldc + 1) * (Lm + 1)` 차원 조합이 있을 수 있습니다. 

그러나 일반적으로 모든 차원 조합이 유효한 것은 아니므로 복잡성을 크게 줄일 수 있습니다. 현재 사용자가 직접 메트릭 자체를 집계하는 경우 차원 수를 제한하지 않습니다. Metrics Advisor가 제공하는 롤업 기능을 사용해야 하는 경우 차원 수는 6보다 커야 합니다. 그러나 메트릭에 대한 차원으로 확장되는 시계열 수는 10,000 미만으로 제한됩니다.

진단 페이지의 **인시던트 트리** 도구에는 전체 토폴로지가 아닌 변칙이 검색된 노드만 표시됩니다. 이는 현재 문제에 초점을 맞출 수 있도록 하기 위한 것입니다. 또한 메트릭 내의 모든 변칙을 표시하지 않을 수 있으며, 대신 기여도에 따라 상위 변칙을 표시합니다. 이러한 방식으로 비정상적인 데이터의 영향, 범위 및 확산 경로를 빠르게 확인할 수 있습니다. 이로 인해 초점을 맞춰야 하는 변칙 수가 현저하게 감소하고 사용자가 주요 문제를 이해하고 찾을 수 있습니다. 
 
예를 들어 `Service = S2 | Data Center = DC2 | Machine = M5`에서 변칙이 발생하면 변칙의 편차가 변칙을 검색한 부모 노드 `Service= S2`에 영향을 주지만 변칙이 `DC2`의 전체 데이터 센터와 `M5`의 모든 서비스에는 영향을 주지 않습니다. 인시던트 트리는 아래 스크린샷과 같이 구축되고 상위 변칙은 `Service = S2`에서 캡처되며, 근본 원인은 둘 다 `Service = S2 | Data Center = DC2 | Machine = M5`로 이어지는 두 경로로 분석될 수 있습니다.

 :::image type="content" source="media/root-cause-paths.png" alt-text="두 개의 고유 경로가 레이블이 S2로 지정된 공통 노드의 에지에 연결되고 레이블이 지정된 5개의 꼭짓점. 상위 변칙은 Service = S2에서 캡처되고, 근본 원인은 Service = S2 | Data Center = DC2 | Machine = M5로 이어지는 두 가지 경로로 분석될 수 있습니다." lightbox="media/root-cause-paths.png":::

## <a name="next-steps"></a>다음 단계
- [Metrics Advisor 개요](overview.md)
- [웹 포털 사용](quickstarts/web-portal.md)