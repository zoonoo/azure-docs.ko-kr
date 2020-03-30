---
title: Azure 어드바이저에 대한 권장 다이제스트
description: 활성 권장 사항에 대한 정기적인 요약 받기
ms.topic: article
ms.date: 03/16/2020
ms.author: sagupt
ms.openlocfilehash: 98f1bfd42a486e005cd1e777a83f5b615a7c8304
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502465"
---
# <a name="configure-periodic-summary-for-recommendations"></a>권장 사항에 대한 정기 요약 구성

어드바이저 **추천 다이제스트는** 다양한 카테고리에서 능동적인 권장 사항을 파악할 수 있는 쉽고 사전 예방적인 방법을 제공합니다. 이 기능은 다양한 범주에서 모든 활성 권장 사항의 요약에 대한 정기적인 알림을 구성하는 기능을 제공합니다. 액션 그룹을 사용하여 이메일, SMS 또는 다른 사람과 같은 알림에 대해 원하는 채널을 선택할 수 있습니다. 이 문서에서는 어드바이저 추천에 대한 **권장 사항 다이제스트를** 설정하는 방법을 보여 줍니다.


## <a name="setting-up-your-recommendation-digest"></a>추천 다이제스트 설정 

**권장 다이제스트** 만들기 환경을 사용하면 요약을 구성하는 데 도움이 됩니다. 구성에 대해 아래 매개 변수를 선택할 수 있습니다.
1. 카테고리: 비용, 고가용성, 성능 및 운영 우수성과 같은 권장 범주가 있습니다. 이 기능은 아직 보안 권장 사항에 사용할 수 없습니다.
2. 다이제스트 빈도: 요약 알림의 빈도는 매주, 격주로, 매월 될 수 있습니다.
3. 작업 그룹: 기존 작업 그룹을 선택하거나 새 작업 그룹을 만들 수 있습니다. 작업 그룹에 대해 자세히 알아보려면 [작업 그룹 만들기 및 관리를](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)참조하세요.
4. 다이제스트를 위한 언어
5. 권장 사항 다이제스트 이름: 사용자 친화적인 문자열을 사용하여 다이제스트를 더 잘 추적하고 모니터링할 수 있습니다.

## <a name="steps-to-create-recommendation-digest-in-azure-portal"></a>Azure 포털에서 권장 다이제스트를 만드는 단계

**권장 다이제스트를** 만드는 단계는 다음과 같습니다.
* **1단계:** Azure 포털에서 **관리자및** **모니터링** 섹션에서 권장 **사항 다이제스트를** 선택합니다. 

   ![추천 다이제스트 진입점](./media/digest-0.png)

* **2단계:** 아래와 같이 상단 표시줄에서 **새 추천 다이제스트를** 선택합니다.

   ![추천 다이제스트 만들기](./media/digest-5.png)

* **3단계:** **범위** 섹션에서 다이제스트에 대한 **구독을** 선택합니다.

   ![추천 다이제스트 입력 제공](./media/digest-1.png)

* **4단계:** **조건** 섹션에서 **범주,** **빈도** 및 **언어와** 같은 구성을 선택합니다.

   ![권장 다이제스트 입력 조건 제공](./media/digest-2.png)

* **5단계:** 작업 **그룹** 섹션에서 다이제스트에 대한 **작업 그룹을** 선택합니다. 여기에서 자세히 알아볼 수 있습니다 - [작업 그룹 만들기 및 관리](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)

   ![추천 다이제스트 입력 작업 그룹 제공](./media/digest-3.png)

* **6단계:** 이 마지막 **섹션에서는 다이제스트 세부 정보에**대한 권장 다이제스트에 이름과 상태를 할당할 수 있습니다. **추천 다이제스트 만들기를** 눌러 설정을 완료합니다.
   ![완전한 권장 다이제스트 생성](./media/digest-4.png)

## <a name="next-steps"></a>다음 단계

Advisor 권장 사항에 대한 자세한 내용은 다음을 참조하세요.
* [Azure Advisor 소개](advisor-overview.md)
* [Advisor 시작](advisor-get-started.md)
* [Advisor 비용 권장 사항](advisor-cost-recommendations.md)
* [Advisor 성능 권장 사항](advisor-performance-recommendations.md)
* [Advisor 보안 권장 사항](advisor-security-recommendations.md)
* [고문 운영 우수성 권장 사항](advisor-operational-excellence-recommendations.md)
* [어드바이저 레스트 API](https://docs.microsoft.com/rest/api/advisor/)
