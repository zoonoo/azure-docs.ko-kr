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
ms.date: 03/16/2020
ms.author: memildin
ms.openlocfilehash: 152168bbadd5815659bc5f70c91bd2a28f5e049d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481962"
---
# <a name="monitor-identity-and-access"></a>ID 및 액세스 모니터링

> [!TIP]
> 2020년 3월부터 Azure Security Center의 ID 및 액세스 권장 사항은 무료 가격 책정 계층의 모든 구독에 포함됩니다. 프리 티어에 구독이 있는 경우 보안 점수는 이전에 ID 및 액세스 보안에 대해 평가되지 않았기 때문에 영향을 받습니다. 

Security Center에서 잠재적인 보안 취약점을 식별하는 경우 리소스를 보호하고 강화하는 데 필요한 컨트롤을 구성하는 과정을 안내하는 권장 사항을 만듭니다.

보안 경계가 네트워크 경계에서 ID 경계로 발전되었습니다. 보안 수준이 네트워크 보호에 대해서는 낮아지고 데이터를 보호하고 앱과 사용자의 보안을 관리하는 방법에 대해서는 높아집니다. 오늘날에는 추가 데이터와 앱을 클라우드로 이동하면 ID는 새 경계가 됩니다.

ID 활동을 모니터링하여 인시던트 발생 전에 사전 조치를 취하거나 공격 시도를 중지하는 사후 조치를 취할 수 있습니다. 예를 들어 보안 센터는 제거를 위해 더 이상 사용되지 않는 계정(더 이상 필요하지 않고 Azure Active Directory에서 로그인하지 못하도록 차단된 계정)에 플래그를 지정할 수 있습니다. 

Azure 보안 센터의 ID **및 액세스** 리소스 보안 섹션에 표시 될 수 있는 권장 사항의 예는 다음과 같습니다.

- 구독에서 소유자 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.
- 구독에 최대 3명의 소유자를 지정해야 합니다.
- 읽기 권한이 있는 외부 계정을 구독에서 제거해야 합니다.
- 더 이상 사용되지 않는 계정은 구독에서 제거해야 합니다.

이러한 권장 사항및 여기에 표시되는 권장 사항의 전체 목록에 대한 자세한 내용은 [ID 및 액세스 권장 사항을](recommendations-reference.md#recs-identity)참조하십시오.

> [!NOTE]
> 구독에 600개 초과의 계정이 있는 경우 Security Center는 사용자 구독에 대해 ID 권장 사항을 실행할 수 없습니다. 실행되지 않는 권장 사항은 아래 "사용할 수 없는 평가"에 나열됩니다.
Security Center는 CSP(클라우드 솔루션 공급자) 파트너의 관리 에이전트에 대해 ID 권장 사항을 실행할 수 없습니다.
>


모든 ID 및 액세스 권장 사항은 **권장 사항** 페이지에서 두 가지 보안 컨트롤 내에서 사용할 수 있습니다.

- 액세스 및 권한 관리 
- MFA 사용

![ID 및 액세스와 관련된 권장 사항이 있는 두 보안 제어](media/security-center-identity-access/two-security-controls-for-identity-and-access.png)


## <a name="enable-multi-factor-authentication-mfa"></a>MFA(다단계 인증) 사용

MFA를 사용하도록 설정하려면 [AD(Azure Active Directory) 테넌트 권한이 필요합니다.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) 

- 프리미엄 버전의 AD가 있는 경우 [조건부 액세스를](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)사용하여 MFA를 사용하도록 설정합니다.

- AD 무료 버전 사용자는 [AD 설명서에](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) 설명된 대로 Azure Active Directory에서 **보안 기본값을** 활성화할 수 있지만 MFA를 사용하도록 설정하는 보안 센터 권장 사항은 계속 표시됩니다.


## <a name="next-steps"></a>다음 단계
다른 Azure 리소스 유형에 적용되는 권장 사항에 대해 자세히 알아보려면 다음 문서를 참조하세요.

- [Azure Security Center에서 머신 및 애플리케이션 보호](security-center-virtual-machine-protection.md)
- [Azure Security Center에서 네트워크 보호](security-center-network-recommendations.md)
- [Azure Security Center에서 Azure SQL 서비스 및 데이터 보호](security-center-sql-service-recommendations.md)