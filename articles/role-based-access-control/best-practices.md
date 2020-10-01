---
title: Azure RBAC에 대 한 모범 사례
description: Azure RBAC (역할 기반 액세스 제어) 사용에 대 한 모범 사례입니다.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: conceptual
ms.workload: identity
ms.date: 09/30/2020
ms.author: rolyon
ms.openlocfilehash: 4cb3e1fe0275c676e2ce54ff9201502fc3595937
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91595487"
---
# <a name="best-practices-for-azure-rbac"></a>Azure RBAC에 대 한 모범 사례

이 문서에서는 azure RBAC (역할 기반 액세스 제어)를 사용 하는 몇 가지 모범 사례를 설명 합니다. 이러한 모범 사례는 Azure RBAC에 대 한 microsoft의 경험 및 사용자와 같은 고객의 경험에서 얻은 것입니다.

## <a name="only-grant-the-access-users-need"></a>사용자에 게 필요한 액세스 권한만 부여

Azure RBAC를 사용하면 팀 내에서 업무를 분리하고 사용자에게 해당 작업을 수행하는 데 필요한 만큼의 권한만 부여할 수 있습니다. Azure 구독 또는 리소스에서 모든 사람에게 무제한 권한을 제공하는 대신, 특정 범위에서 특정 작업만 허용할 수 있습니다.

액세스 제어 전략을 계획할 때 작업을 완료하는 데 필요한 최소한의 권한만 사용자에게 부여하는 것이 가장 좋습니다. 처음에는 더 넓은 범위에서 더 넓은 역할을 할당하는 것이 더 편리하게 보이겠지만, 이렇게 하지 마세요. 역할 및 범위를 제한 하 여 보안 주체의 보안이 손상 되는 경우 어떤 리소스를 위험으로 제한할 수 있습니다.

다음 다이어그램에서는 Azure RBAC 사용에 대 한 제안 된 패턴을 보여 줍니다.

![Azure RBAC 및 최소 권한](./media/best-practices/rbac-least-privilege.png)

역할 할당을 추가 하는 방법에 대 한 자세한 내용은 [Azure Portal를 사용 하 여 Azure 역할 할당 추가 또는 제거](role-assignments-portal.md)를 참조 하세요.

## <a name="limit-the-number-of-subscription-owners"></a>구독 소유자 수 제한

손상 된 소유자가 위반 가능성을 줄이려면 최대 3 개의 구독 소유자가 있어야 합니다. 이 권장 사항은 Azure Security Center 모니터링할 수 있습니다. Security Center의 기타 id 및 액세스 권장 사항은 [보안 권장 사항-참조 가이드](../security-center/recommendations-reference.md)를 참조 하세요.

## <a name="use-azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management 사용

권한 있는 계정을 악의적인 사이버 공격으로부터 보호하기 위해 Azure Active Directory PIM(Privileged Identity Management)를 사용하여 권한 노출 시간을 줄이고 보고서 및 경고를 통해 해당 사용에 대한 가시성을 증가시킬 수 있습니다. PIM은 Azure AD 및 Azure 리소스에 대 한 just-in-time 권한 액세스를 제공 하 여 권한 있는 계정을 보호 합니다. 액세스는 자동으로 취소 된 후에 시간이 바인딩될 수 있습니다. 

자세한 내용은 [Azure AD Privileged Identity Management란?](../active-directory/privileged-identity-management/pim-configure.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure RBAC 문제 해결](troubleshooting.md)