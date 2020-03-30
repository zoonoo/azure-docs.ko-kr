---
title: Azure 보안 센터에서 보안 인시던트 관리 | 마이크로 소프트 문서
description: 이 문서는 Azure 보안 센터를 사용하여 보안 인시던트를 관리하는 데 도움이 됩니다.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: 98fc339e473ffb2bf54e7119634e93046cca1ef3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79415657"
---
# <a name="manage-security-incidents-in-azure-security-center"></a>Azure 보안 센터에서 보안 인시던트 관리

가장 숙련된 보안 분석가조차도 보안 경고를 분류하고 조사하는 데 시간이 많이 걸릴 수 있으며, 대부분의 경우 어디서부터 시작해야 할지 알기조차 어렵습니다. 보안 센터에서는 고유한 [보안 경고](security-center-managing-and-responding-alerts.md) 간에 정보를 연결하기 위해 [분석](security-center-detection-capabilities.md)을 사용하여 공격 캠페인의 단일 보기 및 모든 관련된 경고를 제공할 수 있습니다. 공격자가 사용한 작업 및 영향을 받는 리소스를 신속하게 이해할 수 있습니다.

이 항목에서는 보안 센터의 인시던트및 경고를 수정하는 방법을 설명합니다.

## <a name="what-is-a-security-incident"></a>보안 인시던트란?

보안 센터에서 보안 인시던트는 [kill 체인](alerts-reference.md#intentions) 패턴과 일치하는 리소스에 대한 모든 경고의 집계입니다. 인시던트는 [보안 경고](security-center-managing-and-responding-alerts.md) 목록에 나타납니다. 인시던트를 클릭하여 각 발생에 대한 자세한 정보를 얻을 수 있는 관련 경고를 볼 수 있습니다.

## <a name="managing-security-incidents"></a>보안 인시던트 관리

1. 보안 센터 대시보드에서 보안 경고 타일을 **클릭합니다.** 인시던트 및 경고가 나열됩니다. 보안 인시던트 설명에 다른 경고와 다른 아이콘이 지정되어 있습니다.

    ![보안 인시던트 보기](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. 세부 정보를 보려면 인시던트를 클릭합니다. **보안 인시던트 감지** 블레이드에 추가 세부 정보가 표시됩니다. **일반 정보** 섹션에서는 보안 경고를 트리거한 내용에 대한 정보를 제공할 수 있습니다. 대상 리소스, 소스 IP 주소(해당하는 경우), 경고가 여전히 활성 상태인 경우 및 해결 방법에 대한 권장 사항과 같은 정보가 표시됩니다.  

    ![Azure 보안 센터의 보안 인시던트에 대응](./media/security-center-managing-and-responding-alerts/security-center-alert-incident.png)

1. 각 경고에 대한 자세한 정보를 얻으려면 경고를 클릭합니다. Security Center에서 제안하는 수정은 보안 경고에 따라 다릅니다.

   > [!NOTE]
   > 동일한 경고가 인시던트의 일부로 존재할 수 있을 뿐만 아니라 독립 실행형 경고로 표시될 수 있습니다.

    ![경고 세부 정보](./media/security-center-incident/security-center-incident-alert.png)

1. 각 경고에 대해 제공된 수정 단계를 따릅니다.


## <a name="see-also"></a>참조
이 문서에서는 보안 센터에서 보안 인시던트 기능을 사용하는 방법을 살펴보았습니다. 관련 내용은 다음을 참조하세요.

* [Azure 보안 센터의 위협 보호](threat-protection.md)
* [Azure Security Center의 보안 경고](security-center-alerts-overview.md)
* [보안 경고 관리](security-center-managing-and-responding-alerts.md)