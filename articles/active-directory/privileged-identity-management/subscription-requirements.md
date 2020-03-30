---
title: 권한 있는 ID 관리를 사용하는 라이센스 요구 사항 - Azure Active Directory | 마이크로 소프트 문서
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75932329"
---
# <a name="license-requirements-to-use-privileged-identity-management"></a>Privileged Identity Management를 사용하기 위한 라이선스 요구 사항

Azure AD(Azure Active Directory) PIM(Privileged Identity Management)을 사용하려면 디렉터리에 유효한 라이선스가 있어야 합니다. 또한 관리자 및 해당 사용자에게 라이선스를 할당해야 합니다. 이 문서에서는 권한 있는 ID 관리를 사용 하 여 라이센스 요구 사항을 설명 합니다.

## <a name="valid-licenses"></a>유효한 라이센스

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

## <a name="how-many-licenses-must-you-have"></a>얼마나 많은 라이선스가 있어야 합니까?

디렉터리에 다음 작업을 수행할 직원이 있는 만큼 Azure AD Premium P2 라이선스가 있는지 확인합니다.

- PIM을 사용하여 관리되는 Azure AD 역할에 할당된 사용자
- 사용자가 PIM에서 활성화 요청을 승인하거나 거부할 수 있습니다.
- JIT(Just-in-time) 또는 직접(시간 기반) 할당을 통해 Azure 리소스 역할에 할당된 사용자  
- 액세스 검토에 할당된 사용자
- 액세스 검토를 수행하는 사용자

Azure AD 프리미엄 P2 라이선스는 다음 작업에 필요하지 **않습니다.**

- PIM을 설정하고, 정책을 구성하고, 경고를 받고, 액세스 검토를 설정하는 전역 관리자 또는 권한 있는 역할 관리자 역할을 가진 사용자에게는 라이선스가 필요하지 않습니다.

라이선스에 대한 자세한 내용은 [Azure Active Directory 포털을 사용하여 라이선스 할당 또는 제거를](../fundamentals/license-users-groups.md)참조하세요.

## <a name="example-license-scenarios"></a>라이센스 시나리오 예제

다음은 필요한 라이선스 수를 결정하는 데 도움이 되는 몇 가지 예제 라이센스 시나리오입니다.

| 시나리오 | 계산 | 라이센스 수 |
| --- | --- | --- |
| Woodgrove Bank는 서로 다른 부서의 관리자 10명과 PIM을 구성하고 관리하는 2명의 글로벌 관리자를 보유하고 있습니다. 관리자 5명자격을 갖추게 됩니다. | 자격을 갖춘 관리자를 위한 5개의 라이선스 | 5 |
| 그래픽 디자인 연구소에는 25명의 관리자가 있으며 그 중 14명은 PIM을 통해 관리됩니다. 역할 활성화에는 승인이 필요하며 조직에 활성화를 승인할 수 있는 세 명의 다른 사용자가 있습니다. | 적격 역할에 대한 라이선스 14개 + 승인자 3명 | 17 |
| Contoso에는 50명의 관리자가 있으며 그 중 42명은 PIM을 통해 관리됩니다. 역할 활성화에는 승인이 필요하며 조직에 는 활성화를 승인할 수 있는 5명의 다른 사용자가 있습니다. Contoso는 또한 관리자 역할에 할당된 사용자에 대한 월별 검토를 수행하며 검토자는 PIM에서 관리하는 관리자 역할에 6명이 없는 사용자 관리자입니다. | 적격 역할에 대한 42개의 라이선스 + 5명의 승인자 + 6명의 검토자 | 53 |

## <a name="what-happens-when-a-license-expires"></a>라이선스가 만료되면 어떻게 되나요?

Azure AD 프리미엄 P2, EMS E5 또는 평가판 라이센스가 만료되면 권한 있는 ID 관리 기능은 디렉터리에서 더 이상 사용할 수 없습니다.

- Azure AD 역할에 대한 영구 역할 할당은 영향을 받지 않습니다.
- Azure 포털의 권한 있는 ID 관리 서비스와 권한 있는 ID 관리의 그래프 API cmdlet 및 PowerShell 인터페이스는 사용자가 권한 있는 역할을 활성화하거나 권한 있는 액세스를 관리하거나 수행할 수 없습니다. 권한 있는 역할에 대한 검토에 액세스할 수 있습니다.
- 사용자가 더 이상 권한 있는 역할을 활성화할 수 없으므로 Azure AD 역할의 적격 역할 할당이 제거됩니다.
- Azure AD 역할에 대한 지속적인 액세스 검토가 종료되고 권한 있는 ID 관리 구성 설정이 제거됩니다.
- 권한 있는 ID 관리는 더 이상 역할 할당 변경시 전자 메일을 보내지 않습니다.

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management 배포](pim-deployment-plan.md)
- [Privileged Identity Management 사용 시작](pim-getting-started.md)
- [권한 있는 ID 관리에서 관리할 수 없는 역할](pim-roles.md)
