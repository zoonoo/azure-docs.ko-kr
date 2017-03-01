---

title: "Azure Active Directory의 그룹 기반 라이선스란? | Microsoft Docs"
description: "Azure Active Directory 그룹 기반 라이선스, 작동 방법, 시작하는 방법 및 모범 사례에 대한 설명"
services: active-directory
keywords: "Azure AD 라이선스"
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/21/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 6a9cebafd1ad8f513bfab897970241f7b82b2a53
ms.openlocfilehash: 1f4e0904c9025ce0b21d904acc7b959e823039ef
ms.lasthandoff: 02/22/2017


---

# <a name="what-is-group-based-licensing-in-azure-active-directory"></a>Azure Active Directory의 그룹 기반 라이선스란?

Office 365, Enterprise Mobility + Security, Dynamics CRM과 같은 Microsoft Clouds Services와 기타 유사한 제품을 사용하려면 이러한 서비스에 액세스해야 하는 각 사용자에게 라이선스가 할당되어야 합니다. 라이선스 관리는 관리 포털(Office, Azure) 및 PowerShell cmdlet 중 하나를 통해 관리자에게 노출됩니다. 라이선스 할당 상태는 모든 Microsoft Clouds Services에 대한 ID 관리를 지원하는 기본 인프라인 Azure Active Directory에 저장됩니다.

지금까지 라이선스는 개별 사용자 수준에서만 할당될 수 있으며 이 경우 고객에 대한 대규모 관리 문제가 발생할 수 있습니다. 예를 들어 사용자가 조직이나 부서에 들어오거나 나가는 경우와 같은 조직 변경 내용에 따라 사용자 라이선스를 추가하거나 제거하려면 관리자는 종종 클라우드 서비스를 개별적으로 호출하기 위한 복잡한 PowerShell 스크립트를 작성해야 합니다.

이러한 문제를 해결하기 위해 Azure AD 라이선스 관리 시스템에서는 새로운 기능인 그룹 기반 라이선스를 도입했습니다. 이제 그룹에 하나 이상의 제품 라이선스를 할당할 수 있게 되었습니다. Azure AD는 그룹의 모든 멤버에게 라이선스가 할당되도록 합니다. 그룹에 참여하는 새 멤버에게는 해당 라이선스가 할당되며 그룹을 떠나면 라이선스가 제거됩니다. 이렇게 하면 사용자 기준으로 조직 및 부서 구조에 변경 내용을 반영하기 위해 PowerShell을 통해 라이선스 관리를 자동화할 필요가 없습니다.

## <a name="features"></a>기능

다음은 그룹 기반 라이선스 기능의 주요 특징입니다.

- Azure AD의 보안 그룹에 라이선스를 할당할 수 있습니다. 보안 그룹은 Azure AD Connect를 사용하여 온-프레미스에서 동기화되거나, Azure AD에서 직접 만들어지거나(클라우드 전용 그룹이라고도 함), Azure AD 동적 그룹 기능을 통해 자동으로 만들어질 수 있습니다.

- 그룹에 제품 라이선스를 할당하면 관리자는 제품에서 하나 이상의 서비스 계획을 사용하지 않도록 설정할 수 있습니다. 일반적으로 이러한 작업은 조직이 제품에 포함된 서비스를 사용할 준비가 되지 않을 때, 예를 들어 관리자가 부서에 Office 365 E3 제품을 할당하려고 하지만 Yammer Enterprise 서비스를 일시적으로 사용하지 않도록 설정하려는 경우에 수행됩니다.

- 사용자 수준 라이선스를 필요로 하는 모든 Microsoft Clouds Services는 지원됩니다. 여기에는 모든 Office 365 제품, Enterprise Mobility + Security, Dynamics CRM 등이 포함됩니다.

- 그룹 기반 라이선스는 현재 [Azure Portal](https://portal.azure.com)을 통해서만 사용할 수 있습니다. 주로 사용자 및 그룹 관리를 위해 다른 관리 포털(예: Office 365 포털)을 사용하는 고객은 이 작업을 계속 수행할 수 있지만 그룹 수준에서 라이선스를 관리하려면 Azure Portal을 사용해야 합니다.

- Azure AD는 그룹 멤버 자격 변경으로 인해 발생하는 라이선스 수정 내용을 자동으로 관리합니다. 일반적으로 그룹에 들어오거나 그룹에서 나가는 사용자의 라이선스는 멤버 자격이 변경되면 즉시 수정됩니다.

- 사용자는 라이선스 정책이 지정된 여러 그룹의 구성원일 수 있습니다. 이러한 사용자에게는 그룹 외부의 사용자에게 직접 할당된 일부 라이선스도 있을 수 있습니다. 결과 사용자 상태는 모든 할당된 제품 및 서비스 라이선스의 조합입니다.

- 일부 경우에 사용자에게 라이선스를 할당할 수 없습니다. 예를 들어 테넌트에 라이선스 수가 부족하거나 충돌하는 서비스가 동시에 할당된 경우가 여기에 해당합니다. 관리자는 Azure AD가 그룹 라이선스를 완전하게 처리할 수 없는 사용자에 대한 정보에 액세스할 수 있으며 해당 정보를 기준으로 수정 조치를 취할 수 있습니다.

- 공개 미리 보기 동안 그룹 기반의 라이선스 관리를 사용하려면 Azure AD Basic 이상에 대한 유료 또는 평가판 구독이 테넌트에 필요합니다. 또한 그룹에서 라이선스를 상속하는 모든 사용자는 유료 Azure AD 버전 라이선스가 할당되어야 합니다.

## <a name="next-steps"></a>다음 단계

그룹 기반 라이선스를 통한 라이선스 관리에 대한 기타 시나리오에 대해 자세히 알아보려면 이 문서를 읽어 보세요.

* [Azure Active Directory에서 그룹에 라이선스 할당](active-directory-licensing-group-assignment-azure-portal.md)
* [Azure Active Directory에서 그룹에 대한 라이선스 문제 식별 및 해결](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Azure Active Directory에서 개별 라이선스 사용자를 그룹 기반 라이선스로 마이그레이션하는 방법](active-directory-licensing-group-migration-azure-portal.md)
* [Azure Active Directory 그룹 기반 라이선스 추가 시나리오](active-directory-licensing-group-advanced.md)

