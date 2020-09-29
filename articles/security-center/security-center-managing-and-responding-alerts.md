---
title: Azure Security Center에서 보안 경고 관리| Microsoft Docs
description: 이 문서는 Azure Security Center 기능을 사용하여 보안 경고를 관리하고 대응하는 데 도움이 됩니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: b88a8df7-6979-479b-8039-04da1b8737a7
ms.service: security-center
ms.topic: how-to
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: e17c7d6618d67d66c89875696c5c529af3a85ea9
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91440564"
---
# <a name="manage-and-respond-to-security-alerts-in-azure-security-center"></a>Azure Security Center에서 보안 경고 관리 및 응답

이 항목에서는 리소스를 보호 하기 위해 받은 경고를 확인 하 고 처리 하는 방법을 보여 줍니다. 

* 여러 유형의 경고에 대 한 자세한 내용은 [보안 경고 유형](alerts-reference.md)을 참조 하세요.
* Security Center에서 경고를 생성 하는 방법에 대 한 개요는 [Azure Security Center 검색 하 고 위협에 대응 하는 방법](security-center-alerts-overview.md)을 참조 하세요.

> [!NOTE]
> 고급 검색을 사용 하도록 설정 하려면 Azure Defender를 사용 하도록 설정 합니다. 평가판을 사용할 수 있습니다. 업그레이드하려면 [보안 정책](tutorial-security-policy.md)에서 가격 책정 계층을 선택합니다. 자세한 내용은 [Azure Security Center 가격 책정](security-center-pricing.md)을 참조하세요.

## <a name="what-are-security-alerts"></a>보안 경고란?
보안 센터는 방화벽 및 엔드포인트 보호 솔루션과 같은 Azure 리소스, 네트워크 및 연결된 파트너 솔루션의 로그 데이터를 자동으로 수집하고 분석하며 통합하여 실제 위협을 감지하고 가양성을 줄입니다. 우선 순위가 지정된 보안 경고의 목록은 문제를 신속하게 조사해야 하는 정보 및 공격을 해결하는 방법에 대한 권장 사항과 함께 보안 센터에 표시됩니다.

> [!NOTE]
> Security Center 검색 기능이 작동 하는 방식에 대 한 자세한 내용은 [Azure Security Center 검색 하 고 위협에 대응 하는 방법](security-center-alerts-overview.md#detect-threats)을 참조 하세요.

## <a name="manage-your-security-alerts"></a>보안 경고 관리

1. Security Center 대시보드에서  **위협 방지** 타일을 참조 하 여 경고를 확인 하 고 개요를 확인 합니다.

    ![보안 센터의 보안 경고 타일](./media/security-center-managing-and-responding-alerts/security-center-dashboard-alert.png)

1. 경고에 대 한 자세한 내용을 보려면 타일을 클릭 합니다.

   ![Security Center의 보안 경고](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. 표시 되는 경고를 필터링 하려면 **필터**를 클릭 하 고, 열리는 **필터** 블레이드에서 적용 하려는 필터 옵션을 선택 합니다. 목록은 선택한 필터에 따라 업데이트 됩니다. 필터링은 매우 유용할 수 있습니다. 예를 들어 시스템에서 잠재적 위반을 조사하고 있기 때문에 최근 24시간 동안 발생한 보안 경고를 해결하려고 할 수 있습니다.

    ![보안 센터에서 경고 필터링](./media/security-center-managing-and-responding-alerts/security-center-filter-alerts.png)

## <a name="respond-to-security-alerts"></a>보안 경고에 대응

1. **보안 경고** 목록에서 보안 경고를 클릭 합니다. 공격을 해결 하기 위해 수행 해야 하는 단계와 관련 리소스가 나와 있습니다.

    ![보안 경고에 대응](./media/security-center-managing-and-responding-alerts/security-center-alert.png)

1. 정보를 검토 한 후 공격을 받은 리소스를 클릭 합니다.

    보안 경고 페이지의 왼쪽 창에는 보안 경고의 제목, 심각도, 상태, 작업 시간, 의심 스러운 활동 설명 및 영향을 받는 리소스에 대 한 높은 수준의 정보가 표시 됩니다. 영향을 받는 리소스와 함께 리소스와 관련 된 Azure 태그가 있습니다. 이를 사용 하 여 경고를 조사할 때 리소스의 조직 컨텍스트를 유추 합니다.

    오른쪽 창에는 문제를 조사 하는 데 도움이 되는 경고에 대 한 자세한 정보를 포함 하는 **경고 정보** 탭 (IP 주소, 파일, 프로세스 등)이 포함 되어 있습니다.
     
    ![보안 경고에 대해 수행할 작업에 대 한 제안](./media/security-center-managing-and-responding-alerts/security-center-alert-remediate.png)

    오른쪽 창에는 **작업 수행** 탭도 있습니다. 이 탭을 사용 하 여 보안 경고와 관련 된 추가 작업을 수행할 수 있습니다. 작업은 다음과 같습니다.
    - *위협 완화* -이 보안 경고에 대 한 수동 수정 단계를 제공 합니다.
    - *향후 공격 방지* -공격 노출 영역을 줄이고 보안 상태를 높이고 이후 공격을 방지 하는 데 도움이 되는 보안 권장 사항을 제공 합니다.
    - *자동 응답 트리거* -이 보안 경고에 대 한 응답으로 논리 앱을 트리거하는 옵션을 제공 합니다.
    - *유사한 경고 표시 안 함* -경고가 조직과 관련이 없는 경우 유사한 특성을 가진 이후 경고를 표시 하지 않는 옵션을 제공 합니다.

    ![작업 탭 사용](./media/security-center-managing-and-responding-alerts/alert-take-action.png)




## <a name="see-also"></a>참고 항목

이 문서에서는 보안 경고를 보는 방법에 대해 알아보았습니다. 관련 자료는 다음 페이지를 참조 하세요.

- [경고 억제 규칙 구성](alerts-suppression-rules.md)
- [워크플로 자동화를 사용 하 여 경고 및 권장 사항에 대 한 응답 자동화](workflow-automation.md)
