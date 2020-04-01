---
title: Azure Security Center에서 보안 경고 관리| Microsoft Docs
description: 이 문서는 Azure Security Center 기능을 사용하여 보안 경고를 관리하고 대응하는 데 도움이 됩니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: b88a8df7-6979-479b-8039-04da1b8737a7
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: 8e44ce594375deeac47f037515d96c57d15c8359
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398398"
---
# <a name="manage-and-respond-to-security-alerts-in-azure-security-center"></a>Azure Security Center에서 보안 경고 관리 및 응답

이 항목에서는 리소스를 보호하기 위해 받은 경고를 보고 처리하는 방법을 보여 줍니다. 

* 다양한 유형의 경고에 대한 자세한 내용은 [보안 경고 유형을](alerts-reference.md)참조하십시오.
* 보안 센터에서 경고를 생성하는 방법에 대한 개요는 [Azure Security Center가 위협을 감지하고 대응하는 방법을 참조하세요.](security-center-alerts-overview.md)

> [!NOTE]
> 고급 감지를 사용하도록 설정하려면 Azure Security Center 표준으로 업그레이드합니다. 평가판을 사용할 수 있습니다. 업그레이드하려면 [보안 정책](tutorial-security-policy.md)에서 가격 책정 계층을 선택합니다. 자세한 내용은 [Azure Security Center 가격 책정](security-center-pricing.md)을 참조하세요.

## <a name="what-are-security-alerts"></a>보안 경고란?
보안 센터는 방화벽 및 엔드포인트 보호 솔루션과 같은 Azure 리소스, 네트워크 및 연결된 파트너 솔루션의 로그 데이터를 자동으로 수집하고 분석하며 통합하여 실제 위협을 감지하고 가양성을 줄입니다. 우선 순위가 지정된 보안 경고의 목록은 문제를 신속하게 조사해야 하는 정보 및 공격을 해결하는 방법에 대한 권장 사항과 함께 보안 센터에 표시됩니다.

> [!NOTE]
> 보안 센터 검색 기능의 작동 방식에 대한 자세한 내용은 [Azure Security Center가 위협을 감지하고 대응하는 방법을 참조하세요.](security-center-alerts-overview.md#detect-threats)

## <a name="manage-your-security-alerts"></a>보안 알림 관리

1. 보안 센터 대시보드에서 **위협 보호** 타일에서 경고를 보고 개요를 볼 수 있습니다.

    ![보안 센터의 보안 경고 타일](./media/security-center-managing-and-responding-alerts/security-center-dashboard-alert.png)

1. 경고에 대한 자세한 내용을 보려면 타일을 클릭합니다.

   ![Security Center의 보안 경고](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. 표시된 경고를 필터링하려면 **필터**를 클릭하고 열리는 **필터** 블레이드에서 적용할 필터 옵션을 선택합니다. 선택한 필터에 따라 목록이 업데이트됩니다. 필터링은 매우 유용 할 수 있습니다. 예를 들어 시스템에서 잠재적 위반을 조사하고 있기 때문에 최근 24시간 동안 발생한 보안 경고를 해결하려고 할 수 있습니다.

    ![보안 센터에서 경고 필터링](./media/security-center-managing-and-responding-alerts/security-center-filter-alerts.png)

## <a name="respond-to-security-alerts"></a>보안 경고에 대응

1. 보안 경고 목록에서 보안 경고를 **클릭합니다.** 관련된 리소스와 공격을 수정하기 위해 수행해야 하는 단계가 표시됩니다.

    ![보안 경고에 대응](./media/security-center-managing-and-responding-alerts/security-center-alert.png)

1. 정보를 검토한 후 공격을 받은 리소스를 클릭합니다.

    ![보안 경고에 대해 수행할 작업 제안](./media/security-center-managing-and-responding-alerts/security-center-alert-remediate.png)

    **일반 정보** 섹션에서는 보안 경고를 트리거한 내용에 대한 정보를 제공할 수 있습니다. 대상 리소스, 소스 IP 주소(해당하는 경우), 경고가 여전히 활성 상태인 경우 및 해결 방법에 대한 권장 사항과 같은 정보가 표시됩니다.  

    > [!NOTE]
    >경우에 따라 원본 IP 주소를 사용할 수 없으며 일부 Windows 보안 이벤트 로그에는 IP 주소가 포함되지 않습니다.

1. Security Center에서 제안하는 수정 단계는 보안 경고에 따라 다릅니다. 각 경고에 대해 따르십시오. 

    경우에 따라 보안 경고를 완화하려면 다른 Azure 컨트롤 또는 서비스를 사용하여 권장된 수정을 구현해야 할 수 있습니다. 

## <a name="see-also"></a>참조

이 문서에서는 보안 센터에서 보안 정책을 구성하는 방법을 배웠습니다. 보안 센터에 대한 자세한 내용은 다음을 참조하세요.

- [Azure 보안 센터의 경고 기능을 사용하여 위협을 감시하고 대응하는 방법에 대한 Microsoft Learn 모듈](https://docs.microsoft.com/learn/modules/resolve-threats-with-azure-security-center/)
* [Azure 보안 센터의 보안 경고.](security-center-alerts-overview.md)
* [보안 인시던트 처리](security-center-incident.md)