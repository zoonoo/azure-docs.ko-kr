---
title: 클라우드 그룹을 사용하여 Azure Active Directory에서 역할 할당 관리 | Microsoft Docs
description: ID 관리를 위임하기 위한 사용자 지정 Azure AD 역할을 미리 봅니다. Azure Portal, PowerShell 또는 Graph API에서 Azure 역할 할당을 관리합니다.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: article
ms.date: 05/05/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 37ec1571f44be326fbfddac6d3f2d1ee2295970a
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110085824"
---
# <a name="use-cloud-groups-to-manage-role-assignments-in-azure-active-directory-preview"></a>클라우드 그룹을 사용하여 Azure Active Directory에서 역할 할당 관리(미리 보기)

Azure AD(Azure Active Directory)는 클라우드 그룹을 Azure AD 기본 제공 역할에 할당할 수 있는 퍼블릭 미리 보기를 도입합니다. 이 기능을 사용하면 전역 및 권한 있는 역할 관리자의 노력을 최소화하면서 그룹을 통해 Azure AD에서 관리자 액세스 권한을 부여할 수 있습니다.

다음 예제를 살펴봅시다. Contoso는 Azure AD 조직에서 직원에 대한 암호를 관리하고 다시 설정하기 위해 여러 지역에서 직원을 고용했습니다. 권한 있는 역할 관리자 또는 전역 관리자에게 각 사용자에 대해 개별적으로 기술 지원팀 관리자 역할을 할당하도록 요청하는 대신 Contoso_Helpdesk_Administrators 그룹을 만들어 역할에 할당할 수 있습니다. 사용자가 그룹에 가입하면 역할이 간접적으로 할당됩니다. 그러면 기존 거버넌스 워크플로는 그룹 멤버 자격에 대한 승인 프로세스 및 감사를 처리할 수 있으므로 합법적인 사용자만 그룹의 멤버가 되고 이들이 기술 지원팀 관리자 역할에 할당됩니다.

## <a name="how-this-feature-works"></a>기능의 작동 원리

‘isAssignableToRole’ 속성이 ‘True’로 설정된 새 Microsoft 365 또는 보안 그룹을 만듭니다. **Azure AD 역할을 그룹에 할당할 수 있음** 을 켜고 Azure Portal에서 그룹을 만들 때 해당 속성을 사용하도록 설정할 수도 있습니다. 어떤 방법으로든 사용자에게 역할을 할당하는 것과 동일한 방식으로 하나 이상의 Azure AD 역할에 그룹을 할당할 수 있습니다. 단일 Azure AD 조직(테넌트)에서 최대 300개의 역할 할당 가능 그룹을 만들 수 있습니다.

그룹 멤버가 역할에 대한 영구 액세스 권한을 가지지 못하도록 하려면 Azure AD Privileged Identity Management를 사용할 수 있습니다. 그룹을 Azure AD 역할의 적격 멤버로 할당합니다. 그룹의 각 멤버가 그룹이 할당된 역할에 대한 할당을 활성화할 수 있습니다. 그런 다음 고정된 기간 동안 해당 역할 할당을 활성화할 수 있습니다.

> [!Note]
> PIM을 통해 Azure AD 역할에 그룹을 할당하려면 업데이트된 버전의 Privileged Identity Management를 사용해야 합니다. Azure AD 조직에서 Privileged Identity Management API를 활용하므로 이전 버전의 PIM을 사용할 수 있습니다. 별칭 pim_preview@microsoft.com에 문의하여 조직을 이동하고 API를 업데이트하세요. [PIM에서 Azure AD 역할 및 기능](../privileged-identity-management/azure-ad-roles-features.md)에 대해 자세히 알아보세요.

## <a name="why-we-enforce-creation-of-a-special-group-for-assigning-it-to-a-role"></a>역할을 할당하기 위해 특수 그룹을 만들도록 하는 이유

그룹이 역할에 할당된 경우 그룹 멤버 자격을 관리할 수 있는 IT 관리자도 해당 역할의 멤버 자격을 간접적으로 관리할 수 있습니다. 예를 들어 그룹 Contoso_User_Administrators가 사용자 계정 관리자 역할에 할당되어 있다고 가정합니다. 그룹 멤버 자격을 수정할 수 있는 Exchange 관리자는 Contoso_User_Administrators 그룹에 자신을 추가할 수 있으며 이 과정에서 사용자 계정 관리자가 됩니다. 여기에서 볼 수 있듯이 관리자는 의도하지 않은 방식으로 권한을 승격할 수 있습니다.

Azure AD를 사용하면 그룹에 대해 isAssignableToRole이라는 새 속성을 사용하여 역할에 할당된 그룹을 보호할 수 있습니다. 만든 시간에 isAssignableToRole 속성이 ‘True’로 설정된 클라우드 그룹만 역할에 할당할 수 있습니다. 해당 속성은 변경할 수 없습니다. 해당 속성을 ‘True’로 설정하여 그룹을 만든 후에는 이를 변경할 수 없습니다. 기존 그룹에는 속성을 설정할 수 없습니다. 잠재적 위반이 발생하지 않도록 하기 위해 그룹을 역할에 할당하는 방법을 고안했습니다.

- 전역 관리자 및 권한 있는 역할 관리자만 역할 할당 가능 그룹을 만들 수 있습니다(“isAssignableToRole” 속성을 사용하도록 설정한 경우).
- Azure AD 동적 그룹이 될 수 없습니다. 즉, “할당됨”이라는 멤버 자격 형식이 있어야 합니다. 동적 그룹의 자동화된 채우기로 인해 원치 않는 계정이 그룹에 추가되어 역할이 할당될 수 있습니다.
- 기본적으로 전역 관리자 및 권한 있는 역할 관리자만 역할 할당 그룹의 멤버 자격을 관리할 수 있지만 그룹 소유자를 추가하여 역할 할당 그룹의 관리를 위임할 수 있습니다.
- 권한 상승이 발생하지 않도록 하려면 권한 있는 인증 관리자나 전역 관리자만 역할 할당 가능 그룹의 멤버 및 소유자 자격 증명을 변경할 수 있도록 합니다.
- 중첩되지 않아야 합니다. 그룹을 역할 할당 가능 그룹의 멤버로 추가할 수 없습니다.

## <a name="limitations"></a>제한 사항

다음 시나리오는 현재 지원되지 않습니다.  

- 온-프레미스 그룹을 Azure AD 역할에 할당(기본 제공 또는 사용자 지정)

## <a name="known-issues"></a>알려진 문제

- Azure AD P2 사용이 허가된 고객만 해당: Azure AD 및 PIM(Privileged Identity Management)을 통해 그룹을 활성으로 역할에 할당하지 않습니다. 특히 생성 중인 역할 할당 가능 그룹에 역할을 할당하지 ‘않고’ 나중에 PIM을 사용하여 그룹에 역할을 할당합니다. 이렇게 하면 사용자가 PIM에서 활성 역할 할당을 볼 수 없는 문제가 발생하고 해당 PIM 할당을 제거할 수 없게 됩니다. 적격 할당은 해당 시나리오에서 영향을 받지 않습니다. 해당 할당을 수행하려고 하면 다음과 같은 예기치 않은 동작이 발생할 수 있습니다.
  - 역할 할당의 종료 시간이 잘못 표시될 수 있습니다.
  - PIM 포털에서 **내 역할** 은 하나 이상의 그룹을 통해 직접 할당을 부여하는 방법이 몇 가지이든 간에 하나의 역할 할당만 표시할 수 있습니다.
- Azure AD P2 사용이 허가된 고객만 해당 그룹을 삭제한 후에도 여전히 PIM UI에 역할의 적격 멤버가 표시됩니다. 기능적으로 문제는 없지만 단지 Azure Portal의 캐시 문제입니다.  
- 그룹 멤버 자격을 통해 역할 할당에 새 [Exchange 관리 센터](https://admin.exchange.microsoft.com/)를 사용합니다. 이전 Exchange 관리 센터는 아직 해당 기능을 지원하지 않습니다. Exchange PowerShell cmdlet은 예상대로 작동합니다.
- Azure Information Protection 포털(클래식 포털)은 아직 그룹을 통해 역할 멤버 자격을 인식하지 못합니다. [통합 민감도 레이블 플랫폼으로 마이그레이션](/azure/information-protection/configure-policy-migrate-labels)한 다음 Office 365 Security & Compliance 센터를 사용하여 그룹 할당을 통해 역할을 관리할 수 있습니다.
- [앱 관리 센터](https://config.office.com/)에서 아직 해당 기능을 지원하지 않습니다. 사용자를 Office 앱 관리자 역할에 직접 할당합니다.
- [Microsoft 365 규정 준수 센터](https://compliance.microsoft.com/)는 아직 이 기능을 지원하지 않습니다. 이 포털을 사용할 적절한 Azure AD 역할에 사용자를 직접 할당합니다.

이러한 문제를 해결하고 있습니다.

## <a name="license-requirements"></a>라이선스 요구 사항

이 기능을 사용하려면 Azure AD Premium P1 라이선스가 필요합니다. Just-In-Time 역할 활성화를 위해 Privileged Identity Management를 사용하려면 Azure AD Premium P2 라이선스가 필요합니다. 요구 사항에 적합한 라이선스를 찾으려면 [체험판 및 프리미엄 버전의 일반적으로 사용할 수 있는 기능 비교](https://azure.microsoft.com/pricing/details/active-directory/)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [역할 할당 가능 그룹 만들기](groups-create-eligible.md)
- [역할 할당 가능 그룹에 역할 할당](groups-assign-role.md)
