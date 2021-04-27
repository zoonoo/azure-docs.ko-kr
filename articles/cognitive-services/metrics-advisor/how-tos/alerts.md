---
title: Metrics Advisor 경고 구성
titleSuffix: Azure Cognitive Services
description: 이메일, 웹 및 Azure DevOps용 후크를 사용하여 Metrics Advisor 경고를 구성하는 방법을 알아봅니다.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: mbullwin
ms.openlocfilehash: 30d8fdf99da7a4854db0985bed6256ecd6f7a366
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "93420923"
---
# <a name="how-to-configure-alerts-and-get-notifications-using-a-hook"></a>방법: 후크를 사용하여 경고를 구성하고 알림 가져오기

Metrics Advisor에서 변칙이 검색되면 후크를 사용하여 경고 설정에 따라 경고 알림이 트리거됩니다. 경고 설정은 여러 검색 구성과 함께 사용할 수 있으며, 다양한 매개 변수를 사용하여 경고 규칙을 사용자 지정할 수 있습니다.

## <a name="create-a-hook"></a>후크 만들기

Metrics Advisor는 세 가지 다른 유형의 후크인 이메일 후크, 웹 후크 및 Azure DevOps를 지원합니다. 특정 시나리오에 적합한 후크를 선택할 수 있습니다.       

### <a name="email-hook"></a>이메일 후크

> [!Note]
> 변칙 경고를 보내려면 먼저 Metrics Advisor 리소스 관리자가 이메일 설정을 구성하고 Metrics Advisor에 SMTP 관련 정보를 입력해야 합니다. 리소스 그룹 관리자 또는 구독 관리자는 Metrics Advisor 리소스의 액세스 제어 탭에서 하나 이상의 *Cognitive Services Metrics Advisor 관리자* 역할을 할당해야 합니다. [이메일 설정 구성에 대해 자세히 알아보기](../faq.md#how-to-set-up-email-settings-and-enable-alerting-by-email)

이메일 후크를 만들기 위해 다음 매개 변수를 사용할 수 있습니다. 

이메일 후크는 **받는 사람 이메일** 섹션에 지정된 이메일 주소로 변칙 경고를 보내는 채널입니다. 두 가지 유형의 경고 이메일(*데이터 피드를 사용할 수 없음* 경고 및 하나 이상의 변칙을 포함하는 *인시던트 보고서*)이 전송됩니다. 

|매개 변수 |Description  |
|---------|---------|
| 이름 | 이메일 후크의 이름입니다. |
| 받는 사람 이메일| 경고를 보낼 사람의 이메일 주소입니다.|
| 외부 링크 | 문제 해결 메모와 같이 사용자 지정 리디렉션을 사용하도록 설정한 선택적 필드입니다. |
| 사용자 지정 변칙 경고 제목 | 제목 템플릿은 `${severity}`, `${alertSettingName}`, `${datafeedName}`, `${metricName}`, `${detectConfigName}`, `${timestamp}`, `${topDimension}`, `${incidentCount}`, `${anomalyCount}`를 지원합니다.

**확인** 을 클릭하면 이메일 후크가 만들어집니다. 모든 경고 설정에서 사용하여 변칙 경고를 수신할 수 있습니다. 

### <a name="web-hook"></a>웹후크

> [!Note]
> * **POST** 요청 메서드를 사용합니다.
> * 요청 본문은 다음과 유사합니다.  
    `{"timestamp":"2019-09-11T00:00:00Z","alertSettingGuid":"49635104-1234-4c1c-b94a-744fc920a9eb"}`
> * 웹후크가 만들어지거나 수정되면 API는 요청 본문이 비어 있는 테스트로 호출됩니다. API는 200 HTTP 코드를 반환해야 합니다.

웹후크는 Metrics Advisor 서비스에서 사용할 수 있는 모든 정보에 대한 진입점으로, 경고가 트리거될 때 사용자가 제공한 API를 호출합니다. 모든 경고는 웹후크를 통해 보낼 수 있습니다.

웹후크를 만들려면 다음 정보를 추가해야 합니다.

|매개 변수 |Description  |
|---------|---------|
|엔드포인트     | 경고가 트리거될 때 호출되는 API 주소.        |
|사용자 이름/암호 | API 주소를 인증하는 데 사용할 수 있습니다. 인증이 필요하지 않은 경우 비워 둡니다.         |
|헤더     | API 호출의 사용자 지정 헤더.        |

:::image type="content" source="../media/alerts/create-web-hook.png" alt-text="웹후크 만들기 창.":::

웹후크를 통해 알림이 푸시되면 다음 API를 사용하여 경고에 대한 세부 정보를 가져올 수 있습니다. API 서비스에서 푸시되는 *timestamp* 및 *alertSettingGuid* 를 설정한 후 다음 쿼리를 사용합니다. 
- `query_alert_result_anomalies`
- `query_alert_result_incidents`

### <a name="azure-devops"></a>Azure DevOps

또한 Metrics Advisor는 변칙이 검색될 때 문제/버그를 추적하기 위해 Azure DevOps에서 작업 항목을 자동으로 만드는 기능도 지원합니다. 모든 경고는 Azure DevOps 후크를 통해 전송될 수 있습니다.

Azure DevOps 후크를 만들려면 다음 정보를 추가해야 합니다.

|매개 변수 |Description  |
|---------|---------|
| 이름 | 후크 이름 |
| 조직 | DevOps가 속한 조직 |
| 프로젝트 | DevOps의 특정 프로젝트 |
| 액세스 토큰 |  DevOps에 인증하기 위한 토큰 | 

> [!Note]
> Metrics Advisor가 변칙 경고를 기반으로 작업 항목을 생성하도록 하려면 쓰기 권한을 부여해야 합니다. 후크를 만든 후에는 모든 경고 설정에서 후크를 사용할 수 있습니다. **후크 설정** 페이지에서 후크를 관리합니다.

## <a name="add-or-edit-alert-settings"></a>경고 설정 추가 또는 편집

메트릭 세부 정보 페이지로 이동하여 메트릭 세부 정보 페이지의 왼쪽 아래에 있는 **경고 설정** 섹션을 찾습니다. 선택한 검색 구성에 적용되는 모든 경고 설정이 나열됩니다. 새 검색 구성이 만들어지면 경고 설정이 없으므로 경고가 전송되지 않습니다.  
**추가**, **편집** 및 **삭제** 아이콘을 사용하여 경고 설정을 수정할 수 있습니다.

:::image type="content" source="../media/alerts/alert-setting.png" alt-text="경고 설정 메뉴 항목":::

**추가** 또는 **편집** 단추를 클릭하면 경고 설정을 추가하거나 편집할 수 있는 창이 나타납니다.

:::image type="content" source="../media/alerts/edit-alert.png" alt-text="경고 설정 추가 또는 편집":::

**경고 설정 이름**: 이 경고 설정의 이름입니다. 이 이름은 경고 이메일 제목에 표시됩니다.

**후크**: 경고를 보낼 후크 목록입니다.

위의 스크린샷에 표시된 섹션은 하나의 검색 구성에 대한 설정입니다. 다양한 검색 구성에 대해 서로 다른 경고 설정을 지정할 수 있습니다. 이 창의 세 번째 드롭다운 목록을 사용하여 대상 구성을 선택합니다. 

### <a name="filter-settings"></a>필터 설정 

다음은 하나의 검색 구성에 대한 필터 설정입니다.

**경고 대상** 에는 변칙 필터링을 위한 4가지 옵션이 있습니다.

* **모든 계열의 변칙**: 모든 변칙이 경고에 포함됩니다.         
* **계열 그룹의 변칙**: 차원 값을 기준으로 계열을 필터링합니다. 일부 차원에 대해 특정 값을 설정합니다. 계열이 지정된 값과 일치하는 경우에만 변칙이 경고에 포함됩니다.       
* **즐겨찾는 계열의 변칙**: 즐겨찾기로 표시된 계열만 경고에 포함됩니다.        |
* **모든 계열의 상위 N개 변칙**: 이 필터는 값이 상위 N개에 속하는 계열에만 관심이 있는 경우에 사용됩니다. 일부 타임스탬프를 다시 살펴보고 이 타임스탬프의 계열 값이 상위 N개에 속하는지 확인합니다. "상위 n개"가 지정된 수보다 클 경우 변칙이 경고에 포함됩니다.        |

**변칙 옵션 필터링** 은 다음 옵션이 있는 추가 필터입니다.

- **심각도**: 변칙 심각도가 지정된 범위 내에 있는 경우에만 변칙이 포함됩니다.
- **다시 알림**: 경고에서 트리거될 때 다음 N개 포인트(마침표)의 변칙에 대한 경고를 일시적으로 중지합니다.
    - **다시 알림 유형**: **계열** 로 설정될 때 트리거된 변칙은 해당 계열만 다시 알립니다. **메트릭** 의 경우 변칙 하나가 트리거되면 이 메트릭의 모든 계열을 다시 알립니다.
    - **다시 알림 수**: 다시 알릴 포인트(마침표) 수입니다.
    - **비연속인 경우 초기화**: 선택한 경우 변칙이 트리거되면 연속적인 다음 n개의 변칙을 다시 알립니다. 다음 데이터 포인트 중 하나가 변칙이 아닌 경우 해당 포인트부터 다시 알림이 초기화됩니다. 이 옵션을 선택하지 않으면 연속적인 데이터 포인트가 변칙이 아니더라도 한 변칙이 트리거되면 다음 n개 포인트(마침표)를 다시 알립니다.
- **값**(선택 사항): 값을 기준으로 필터링합니다. 조건을 충족하는 포인트 값인 경우에만 변칙이 포함됩니다. 다른 메트릭의 해당 값을 사용하는 경우 두 메트릭의 차원 이름이 일치해야 합니다.

필터링되지 않은 변칙은 경고로 전송됩니다.

### <a name="add-cross-metric-settings"></a>교차 메트릭 설정 추가

다른 섹션을 추가하려면 경고 설정 페이지에서 **+ 교차 메트릭 설정 추가** 를 클릭합니다.

**연산자** 선택기는 각 섹션의 논리적 관계로, 경고의 전송 여부를 결정합니다.


|연산자  |Description  |
|---------|---------|
|AND     | 계열이 각 경고 섹션과 일치하고 모든 데이터 포인트가 변칙인 경우에만 경고를 보냅니다. 메트릭에 다른 차원 이름이 있는 경우 경고가 트리거되지 않습니다.         |
|OR     | 하나 이상의 섹션에 변칙이 포함되어 있는 경우 경고를 보냅니다.         |

:::image type="content" source="../media/alerts/alert-setting-operator.png" alt-text="여러 경고 설정 섹션에 대한 연산자":::

## <a name="next-steps"></a>다음 단계

- [피드백을 사용하여 변칙 검색 조정](anomaly-feedback.md)
- [인시던트 진단](diagnose-incident.md)
- [메트릭을 구성하고 구성 감지 미세 조정](configure-metrics.md)