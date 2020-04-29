---
title: Azure Advisor에 대 한 권장 사항 다이제스트
description: 활성 권장 사항에 대 한 정기 요약 받기
ms.topic: article
ms.date: 03/16/2020
ms.author: sagupt
ms.openlocfilehash: 98f1bfd42a486e005cd1e777a83f5b615a7c8304
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79502465"
---
# <a name="configure-periodic-summary-for-recommendations"></a>권장 구성에 대 한 정기 요약 구성

Advisor **권장 다이제스트** 는 다양 한 범주에 걸쳐 활성 권장 사항을 유지 하는 쉽고 적극적인 방법을 제공 합니다. 이 기능은 다양 한 범주에 걸쳐 모든 활성 권장 사항의 요약에 대 한 주기적인 알림을 구성 하는 기능을 제공 합니다. 작업 그룹을 사용 하 여 메일, sms 또는 기타와 같은 알림에 대해 원하는 채널을 선택할 수 있습니다. 이 문서에서는 Advisor 권장 사항에 대 한 **권장 다이제스트** 를 설정 하는 방법을 보여 줍니다.


## <a name="setting-up-your-recommendation-digest"></a>권장 사항 다이제스트 설정 

**권장 사항 다이제스트** 생성 환경을 통해 요약을 구성할 수 있습니다. 구성에 대 한 다음 매개 변수를 선택할 수 있습니다.
1. 범주: 비용, 고가용성, 성능 및 운영 등의 권장 범주가 있습니다. 보안 권장 사항에 대 한 기능을 아직 사용할 수 없습니다.
2. 다이제스트 빈도: 요약 알림의 빈도는 주별, 격주, 매월 일 수 있습니다.
3. 작업 그룹: 기존 작업 그룹을 선택 하거나 새 작업 그룹을 만들 수 있습니다. 작업 그룹에 대 한 자세한 내용은 [작업 그룹 만들기 및 관리](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)를 참조 하세요.
4. 다이제스트 언어
5. 권장 사항 다이제스트 이름: 사용자에 게 친숙 한 문자열을 사용 하 여 다이제스트를 보다 효율적으로 추적 하 고 모니터링할 수 있습니다.

## <a name="steps-to-create-recommendation-digest-in-azure-portal"></a>Azure Portal에서 권장 사항 다이제스트를 만드는 단계

**권장 사항 다이제스트** 를 만드는 단계는 다음과 같습니다.
* **1 단계:** Azure Portal에서 **Advisor** 로 이동 하 고 **모니터링** 섹션에서 **추천 다이제스트** 를 선택 합니다. 

   ![권장 사항 다이제스트 항목-포인트](./media/digest-0.png)

* **2 단계:** 아래와 같이 위쪽 표시줄에서 **새 권장 사항 다이제스트** 를 선택 합니다.

   ![권장 사항 다이제스트 만들기](./media/digest-5.png)

* **3 단계:** **범위** 섹션에서 다이제스트에 대 한 **구독** 을 선택 합니다.

   ![권장 다이제스트 입력 제공](./media/digest-1.png)

* **4 단계:** **조건** 섹션에서 **범주**, **빈도** 및 **언어** 와 같은 구성을 선택 합니다.

   ![권장 사항 다이제스트 입력 조건 제공](./media/digest-2.png)

* **5 단계:** **작업 그룹** 섹션에서 다이제스트의 **작업 그룹** 을 선택 합니다. 자세한 내용은 [작업 그룹 만들기 및 관리](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) 를 참조 하세요.

   ![권장 사항 다이제스트 입력 작업 그룹 제공](./media/digest-3.png)

* **6 단계:** **다이제스트 세부 정보**에 대 한이 마지막 섹션에서는 권장 사항 다이제스트에 이름 및 상태를 할당할 수 있습니다. **권장 사항 다이제스트 만들기** 를 눌러 설정을 완료 합니다.
   ![권장 사항 다이제스트 만들기 완료](./media/digest-4.png)

## <a name="next-steps"></a>다음 단계

Advisor 권장 사항에 대한 자세한 내용은 다음을 참조하세요.
* [Azure Advisor 소개](advisor-overview.md)
* [Advisor 시작](advisor-get-started.md)
* [Advisor 비용 권장 사항](advisor-cost-recommendations.md)
* [Advisor 성능 권장 사항](advisor-performance-recommendations.md)
* [Advisor 보안 권장 사항](advisor-security-recommendations.md)
* [Advisor 작동의 뛰어난 권장 사항](advisor-operational-excellence-recommendations.md)
* [Advisor REST API](https://docs.microsoft.com/rest/api/advisor/)
