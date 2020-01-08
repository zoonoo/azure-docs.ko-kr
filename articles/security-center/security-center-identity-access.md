---
title: Azure Security Center에서 ID 및 액세스 모니터링 | Microsoft Docs
description: Azure Security Center의 ID 및 액세스 기능을 사용하여 사용자의 액세스 작업과 ID 관련 문제를 모니터링하는 방법을 알아봅니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2019
ms.author: memildin
ms.openlocfilehash: 6b262baddd10c9d0dff4b196b733972b97d99872
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552987"
---
# <a name="monitor-identity-and-access-preview"></a>Id 및 액세스 모니터링 (미리 보기)
Security Center에서 잠재적인 보안 취약점을 식별하는 경우 리소스를 보호하고 강화하는 데 필요한 컨트롤을 구성하는 과정을 안내하는 권장 사항을 만듭니다.

이 문서에서는 Azure Security Center 리소스 보안 섹션의 **id 및 액세스** 페이지에 대해 설명 합니다.

이 페이지에 표시 될 수 있는 권장 사항의 전체 목록은 [id 및 액세스 권장 사항](recommendations-reference.md#recs-identity)을 참조 하세요.

> [!NOTE]
> ID 및 액세스 모니터링은 미리 보기로 제공되며 Security Center의 표준 계층에서만 사용할 수 있습니다. Security Center의 가격 책정 계층에 대해 자세히 알아보려면 [가격 책정](security-center-pricing.md)을 참조하세요.
>

Id는 엔터프라이즈의 제어 평면 이어야 하 고 id를 보호 하는 것이 가장 중요 합니다. 보안 경계가 네트워크 경계에서 ID 경계로 발전되었습니다. 보안 수준이 네트워크 보호에 대해서는 낮아지고 데이터를 보호하고 앱과 사용자의 보안을 관리하는 방법에 대해서는 높아집니다. 오늘날에는 추가 데이터와 앱을 클라우드로 이동하면 ID는 새 경계가 됩니다.

ID 작업을 모니터링하여 문제가 발생하기 전에 사전 대응 조치를 취하거나 사후 대응을 통해 공격 시도를 중단할 수 있습니다. ID 및 액세스 대시보드는 다음과 같은 권장 사항을 제공합니다.

- 구독에 대한 권한이 있는 계정에 대해 MFA 사용
- 구독에서 쓰기 권한이 있는 외부 계정 제거
- 구독에서 권한 있는 외부 계정 제거

> [!NOTE]
> 구독에 600개 초과의 계정이 있는 경우 Security Center는 사용자 구독에 대해 ID 권장 사항을 실행할 수 없습니다. 실행 되지 않는 권장 사항은 아래의 "사용할 수 없는 평가" 아래에 나열 됩니다.
Security Center는 CSP(클라우드 솔루션 공급자) 파트너의 관리 에이전트에 대해 ID 권장 사항을 실행할 수 없습니다.
>

## <a name="monitor-identity-and-access"></a>ID 및 액세스 모니터링

Security Center 사이드바 ( **리소스**아래) 또는 개요 페이지에서 **id & 액세스** 를 선택 하 여 식별 된 id 및 액세스 문제 목록을 엽니다. 

**ID 및 액세스**에는 두 개의 탭이 있습니다.

- **개요**: Security Center에 의해 식별된 권장 사항입니다.
- **구독**: 구독의 목록 및 각각의 현재 보안 상태입니다.

[![Id & 액세스](./media/security-center-identity-access/identity-dashboard.png)](./media/security-center-identity-access/identity-dashboard.png#lightbox)

### <a name="overview-section"></a>개요 섹션
**개요**에 권장 사항의 목록이 있습니다. 첫 번째 열에는 권장 사항이 나열되고, 두 번째 열에는 해당 권장 사항의 영향을 받은 구독의 총 수가 표시됩니다. 세 번째 열은 문제의 심각도를 보여 줍니다.

1. 권장 사항을 선택합니다. 권장 구성 창이 열리고 다음을 표시 합니다.

   - 권장 사항의 설명
   - 비정상 및 정상 상태의 구독 목록
   - 실패한 평가로 인해 검색되지 않는 리소스 또는 무료 계층에서 실행되는 구독에 있는 리소스와 평가되지 않은 리소스의 목록

    [![권장 사항 창](./media/security-center-identity-access/select-subscription.png)](./media/security-center-identity-access/select-subscription.png#lightbox)

1. 추가 세부 정보는 목록에서 구독을 선택합니다.

### <a name="subscriptions-section"></a>구독 섹션
**구독**에는 구독의 목록이 있습니다. 첫 번째 열에는 구독이 나열되고, 두 번째 열에는 각 구독에 대한 권장 사항의 총 수를 보여 줍니다. 세 번째 열은 문제의 심각도를 보여 줍니다.

[![구독 탭](./media/security-center-identity-access/subscriptions.png)](./media/security-center-identity-access/subscriptions.png#lightbox)

1. 구독을 선택합니다. 요약 보기는 다음과 같은 세 개의 탭으로 열립니다.

   - **권장 사항**: 실패한 Security Center에서 수행된 평가를 기반으로 합니다.
   - **통과된 평가**: 전달된 Security Center에서 수행된 평가의 목록입니다.
   - **사용할 수 없는 평가**: 오류로 인해 또는 구독에 600개 초과의 계정이 있어서 실행에 실패한 평가 목록입니다.

   **권장 사항**에는 선택한 구독에 대한 권장 사항 목록과 각 권장 사항의 심각도가 표시됩니다.

   [구독 선택에 대 한 ![권장 사항](./media/security-center-identity-access/recommendations.png)](./media/security-center-identity-access/recommendations.png#lightbox)

1. 권장 사항의 설명, 비정상 및 정상 구독의 목록 및 검사되지 않은 리소스의 목록을 보려면 권장 사항을 선택합니다.

   [권장 사항에 대 한 ![설명](./media/security-center-identity-access/designate.png)](./media/security-center-identity-access/designate.png#lightbox)

   **통과된 평가**에는 통과된 평가의 목록이 나와 있습니다.  이러한 평가의 심각도는 항상 녹색입니다.

   [![통과 평가](./media/security-center-identity-access/passed-assessments.png)](./media/security-center-identity-access/passed-assessments.png#lightbox)

1. 평가의 설명 및 정상 구독 목록을 보려면 목록에서 통과된 평가를 선택합니다. 실패한 모든 구독을 나열하는 비정상 구독에 대한 탭이 있습니다.

   [![통과 평가](./media/security-center-identity-access/remove.png)](./media/security-center-identity-access/remove.png#lightbox)

> [!NOTE]
> MFA를 필요 하지만 제외가 설정 된 조건부 액세스 정책을 만든 경우 Security Center MFA 권장 평가는 정책을 준수 하지 않는 것으로 간주 하므로 일부 사용자는 MFA 없이 Azure에 로그인 할 수 있습니다.

## <a name="next-steps"></a>다음 단계
다른 Azure 리소스 유형에 적용 되는 권장 사항에 대해 자세히 알아보려면 다음 문서를 참조 하세요.

- [Azure Security Center에서 머신 및 애플리케이션 보호](security-center-virtual-machine-protection.md)
- [Azure Security Center에서 네트워크 보호](security-center-network-recommendations.md)
- [Azure Security Center에서 Azure SQL 서비스 및 데이터 보호](security-center-sql-service-recommendations.md)