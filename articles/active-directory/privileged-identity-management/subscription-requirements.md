---
title: Privileged Identity Management 사용할 라이선스 요구 사항-Azure Active Directory | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)을 사용하기 위한 라이선스 요구 사항에 대해 설명합니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: markwahl-msft
ms.assetid: 34367721-8b42-4fab-a443-a2e55cdbf33d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 01/10/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70696cdb95fffc1e5faa46ca1b5f2180633ed63a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "75932329"
---
# <a name="license-requirements-to-use-privileged-identity-management"></a>Privileged Identity Management를 사용하기 위한 라이선스 요구 사항

Azure AD(Azure Active Directory) PIM(Privileged Identity Management)을 사용하려면 디렉터리에 유효한 라이선스가 있어야 합니다. 또한 관리자 및 해당 사용자에게 라이선스를 할당해야 합니다. 이 문서에서는 Privileged Identity Management 사용 하기 위한 라이선스 요구 사항을 설명 합니다.

## <a name="valid-licenses"></a>유효한 라이선스

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

## <a name="how-many-licenses-must-you-have"></a>필요한 라이선스 수는 몇 개입니까?

다음 작업을 수행 하는 직원이 있으므로 디렉터리에 적어도 Azure AD Premium P2 라이선스가 있는지 확인 합니다.

- PIM을 사용 하 여 관리 되는 Azure AD 역할에 적격으로 할당 된 사용자
- PIM에서 활성화 요청을 승인 하거나 거부할 수 있는 사용자
- JIT(Just-in-time) 또는 직접(시간 기반) 할당을 통해 Azure 리소스 역할에 할당된 사용자  
- 액세스 검토에 할당된 사용자
- 액세스 검토를 수행하는 사용자

Azure AD Premium P2 라이선스는 다음 작업에 필요 **하지 않습니다** .

- PIM을 설정 하 고, 정책을 구성 하 고, 경고를 수신 하 고, 액세스 검토를 설정 하는 전역 관리자 또는 권한 있는 역할 관리자 역할이 있는 사용자에 게는 라이선스가 필요 하지 않습니다.

라이선스에 대 한 자세한 내용은 [Azure Active Directory 포털을 사용 하 여 라이선스 할당 또는 제거](../fundamentals/license-users-groups.md)를 참조 하세요.

## <a name="example-license-scenarios"></a>예제 라이선스 시나리오

다음은 보유 해야 하는 라이선스 수를 결정 하는 데 도움이 되는 몇 가지 예제 라이선스 시나리오입니다.

| 시나리오 | 계산 | 라이선스 수 |
| --- | --- | --- |
| Woodgrove Bank에는 PIM을 구성 하 고 관리 하는 2 개의 전역 관리자와 다른 부서에 대 한 10 개의 관리자가 있습니다. 5 명의 관리자가 적격 할 수 있습니다. | 적격 한 관리자의 라이선스 5 개 | 5 |
| 그래픽 디자인 협회에는 24 명의 관리자가 PIM을 통해 관리 됩니다. 역할 활성화에는 승인이 필요 하며, 조직에는 정품 인증을 승인할 수 있는 세 명의 사용자가 있습니다. | 적격 역할에 대해 14 개의 라이선스 + 세 개의 승인자 | 17 |
| Contoso에는 42이 PIM을 통해 관리 되는 50 관리자가 있습니다. 역할 활성화에는 승인이 필요 하 고 조직에는 정품 인증을 승인할 수 있는 5 명의 사용자가 있습니다. 또한 Contoso는 관리자 역할 및 검토자에 게 할당 된 사용자의 월간 검토를 수행 합니다. 사용자의 관리자는 PIM에서 관리 하는 관리자 역할에 속하지 않습니다. | 적격 역할의 42 라이선스 + 5 명의 승인자 + 6 명의 검토자 | 53 |

## <a name="what-happens-when-a-license-expires"></a>라이선스가 만료되면 어떻게 되나요?

Azure AD Premium P2, EMS E5 또는 평가판 라이선스가 만료 된 경우 디렉터리에서 Privileged Identity Management 기능을 더 이상 사용할 수 없습니다.

- Azure AD 역할에 대한 영구 역할 할당은 영향을 받지 않습니다.
- Azure Portal의 Privileged Identity Management 서비스 및 Privileged Identity Management의 Graph API cmdlet 및 PowerShell 인터페이스는 사용자가 더 이상 권한 있는 역할을 활성화 하거나 권한 있는 액세스를 관리 하거나 권한 있는 역할의 액세스 검토를 수행 하는 데 사용할 수 없습니다.
- 사용자가 더 이상 권한 있는 역할을 활성화할 수 없으므로 Azure AD 역할의 적격 역할 할당이 제거됩니다.
- Azure AD 역할의 진행 중인 액세스 검토가 모두 종료 되 고 Privileged Identity Management 구성 설정이 제거 됩니다.
- Privileged Identity Management는 역할 할당 변경 내용에 대 한 전자 메일을 더 이상 보내지 않습니다.

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management 배포](pim-deployment-plan.md)
- [Privileged Identity Management 사용 시작](pim-getting-started.md)
- [Privileged Identity Management에서 관리할 수 없는 역할](pim-roles.md)
