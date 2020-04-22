---
title: Azure RBAC에 대한 모범 사례
description: Azure 역할 기반 액세스 제어(Azure RBAC)를 사용하는 모범 사례입니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/17/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: dc86dd488ff9e8649ae80f4768941791dd37fce6
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726775"
---
# <a name="best-practices-for-azure-rbac"></a>Azure RBAC에 대한 모범 사례

이 문서에서는 Azure 역할 기반 액세스 제어(Azure RBAC)를 사용하기 위한 몇 가지 모범 사례에 대해 설명합니다. 이러한 모범 사례는 Azure RBAC에 대한 경험과 자신과 같은 고객의 경험에서 비롯됩니다.

## <a name="only-grant-the-access-users-need"></a>액세스 사용자에게만 필요한 권한 부여

Azure RBAC를 사용하면 팀 내에서 업무를 분리하고 작업을 수행하는 데 필요한 사용자에게 만 액세스 권한을 부여할 수 있습니다. Azure 구독 또는 리소스에서 모든 사람에게 무제한 권한을 제공하는 대신, 특정 범위에서 특정 작업만 허용할 수 있습니다.

액세스 제어 전략을 계획할 때 작업을 완료하는 데 필요한 최소한의 권한만 사용자에게 부여하는 것이 가장 좋습니다. 다음 다이어그램은 RBAC에 대한 권장 패턴을 보여줍니다.

![RBAC 및 최소 권한](./media/best-practices/rbac-least-privilege.png)

역할 할당을 추가하는 방법에 대한 자세한 내용은 [역할 할당 추가 또는 제거를](role-assignments-portal.md)참조하십시오.

## <a name="limit-the-number-of-subscription-owners"></a>구독 소유자 수 제한

손상된 소유자에 의한 위반 가능성을 줄이려면 최대 3명의 구독 소유자가 있어야 합니다. 이 권장 사항은 Azure 보안 센터에서 모니터링할 수 있습니다. 보안 센터의 다른 ID 및 액세스 권장 사항은 [보안 권장 사항- 참조 가이드를](../security-center/recommendations-reference.md)참조하십시오.

## <a name="use-azure-ad-privileged-identity-management"></a>Azure AD 권한 ID 관리 사용

권한 있는 계정을 악의적인 사이버 공격으로부터 보호하기 위해 Azure Active Directory PIM(Privileged Identity Management)를 사용하여 권한 노출 시간을 줄이고 보고서 및 경고를 통해 해당 사용에 대한 가시성을 증가시킬 수 있습니다. PIM은 Azure AD 및 Azure 리소스에 대한 적시 권한 있는 액세스를 제공하여 권한 있는 계정을 보호하는 데 도움이 됩니다. 액세스는 권한이 자동으로 해지된 후 시간 바인딩될 수 있습니다. 

자세한 내용은 [Azure AD Privileged Identity Management란?](../active-directory/privileged-identity-management/pim-configure.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure RBAC 문제 해결](troubleshooting.md)