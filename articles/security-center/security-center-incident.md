---
title: Azure Security Center에서 보안 인시던트 관리 Microsoft Docs
description: 이 문서에서는 Azure Security Center를 사용 하 여 보안 인시던트를 관리 하는 방법을 안내 합니다.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 06/15/2020
ms.author: memildin
ms.openlocfilehash: c31d3136ec4bcdf6da7680d41de078f8bd03d256
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91447100"
---
# <a name="manage-security-incidents-in-azure-security-center"></a>Azure Security Center에서 보안 인시던트 관리

보안 경고를 심사 하 고 조사 하는 것이 가장 숙련 된 보안 분석가의 경우에도 시간이 많이 걸릴 수 있습니다. 무엇 보다도 어디서부터 시작 해야 합니다. 

Security Center는 [분석](security-center-detection-capabilities.md) 을 사용 하 여 고유한 [보안 경고](security-center-managing-and-responding-alerts.md)간에 정보를 연결 합니다. 이러한 연결을 사용 하 여 공격자의 작업 및 영향을 받는 리소스를 이해 하는 데 도움을 주는 공격 캠페인 및 관련 경고에 대 한 단일 보기를 제공할 수 Security Center.

이 페이지에서는 Security Center의 인시던트에 대해 간략하게 설명 합니다.

## <a name="what-is-a-security-incident"></a>보안 인시던트란?

보안 센터에서 보안 인시던트는 [kill 체인](alerts-reference.md#intentions) 패턴과 일치하는 리소스에 대한 모든 경고의 집계입니다. 인시던트는 [보안 경고](security-center-managing-and-responding-alerts.md) 페이지에 표시 됩니다. 인시던트를 선택 하 여 관련 경고를 확인 하 고 자세한 정보를 확인 하세요.

## <a name="managing-security-incidents"></a>보안 인시던트 관리

1. Security Center 개요 페이지에서 **보안 경고** 타일을 선택 합니다. 인시던트 및 경고가 나열 됩니다. 보안 인시던트에는 보안 경고와 다른 아이콘이 있습니다.

    ![보안 인시던트 보기](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. 세부 정보를 보려면 인시던트를 선택 합니다. **보안 인시던트** 페이지에 자세한 정보가 표시 됩니다. 

    [![Azure Security Center의 보안 인시던트에 대응](media/security-center-incident/incident-details.png)](media/security-center-incident/incident-details.png#lightbox)

    보안 인시던트 페이지의 왼쪽 창에는 보안 인시던트에 대 한 개략적인 정보 (제목, 심각도, 상태, 작업 시간, 설명 및 영향을 받는 리소스)가 표시 됩니다. 영향을 받는 리소스 옆에 관련 Azure 태그를 볼 수 있습니다. 이러한 태그를 사용 하 여 경고를 조사할 때 리소스의 조직 컨텍스트를 유추 합니다.

    오른쪽 창에는이 인시던트의 일부로 상관 관계가 지정 된 보안 경고와 함께 **경고** 탭이 있습니다. 

    >[!TIP]
    > 특정 경고에 대 한 자세한 내용을 보려면 해당 경고를 선택 합니다. 

    [![인시던트의 작업 수행 탭](media/security-center-incident/incident-take-action-tab.png)](media/security-center-incident/incident-take-action-tab.png#lightbox)

    **작업 수행** 탭으로 전환 하려면 오른쪽 창 아래에 있는 탭 이나 단추를 선택 합니다. 이 탭을 사용 하 여 다음과 같은 추가 작업을 수행할 수 있습니다.
    - *위협 완화* -이 보안 인시던트에 대 한 수동 수정 단계를 제공 합니다.
    - *향후 공격 방지* -공격 노출 영역을 줄이고, 보안 상태를 높이고, 향후 공격을 방지 하는 데 도움이 되는 보안 권장 사항을 제공 합니다.
    - *자동 응답 트리거* -이 보안 인시던트에 대 한 응답으로 논리 앱을 트리거하는 옵션을 제공 합니다.
    - *유사한 경고 표시 안 함* -경고가 조직과 관련이 없는 경우 유사한 특성을 가진 이후 경고를 표시 하지 않는 옵션을 제공 합니다. 

   > [!NOTE]
   > 동일한 경고가 인시던트의 일부로 있을 수 있을 뿐만 아니라 독립 실행형 경고로 표시 될 수도 있습니다.

1. 인시던트의 위협을 해결 하려면 각 경고와 함께 제공 되는 수정 단계를 따릅니다.


## <a name="next-steps"></a>다음 단계

이 페이지에서는 Security Center의 보안 인시던트 기능에 대해 설명 했습니다. 관련 정보는 다음 페이지를 참조 하세요.

- [Security Center의 보안 경고](security-center-alerts-overview.md)
- [보안 경고 관리 및 응답](security-center-managing-and-responding-alerts.md)