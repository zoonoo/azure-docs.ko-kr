---
title: 관리 단위 문제 해결 및 FAQ - Azure 활성 디렉터리 | 마이크로 소프트 문서
description: 관리 단위를 조사하여 Azure Active Directory에서 제한된 범위의 권한을 부여합니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 022658306d6e4d69174cc616d230cfe4892f1204
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684850"
---
# <a name="azure-ad-administrative-units-troubleshooting-and-faq"></a>Azure AD 관리 단위: 문제 해결 및 FAQ

Azure Active Directory(Azure AD)에서 보다 세부적인 관리 제어를 위해 하나 이상의 관리 단위(AUS)로 제한되는 범위로 Azure AD 역할에 사용자를 할당할 수 있습니다. 일반적인 작업에 대한 샘플 PowerShell 스크립트의 경우 [관리 단위로 작업](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)작업을 참조하십시오.

## <a name="frequently-asked-questions"></a>질문과 대답

**Q: 관리 단위를 만들 수 없는 이유는 무엇입니까?**

**A:** 전역 *관리자* 또는 *권한 있는 역할 관리자만* Azure AD에서 관리 단위를 만들 수 있습니다. 관리 단위를 만들려는 사용자에게 *전역 관리자* 또는 권한 있는 역할 *관리자* 역할이 할당되어 있는지 확인합니다.

**Q: 관리 단위에 그룹을 추가했습니다. 그룹 구성원이 여전히 나타나지 않는 이유는 무엇입니까?**

**A:** 관리 단위에 그룹을 추가해도 그룹의 모든 구성원이 그룹에 추가되지는 않습니다. 사용자는 관리 장치에 직접 할당되어야 합니다.

**Q: 관리 단위의 구성원을 방금 추가(또는 제거)했습니다. 사용자 인터페이스에 멤버가 표시되지 않거나 여전히 표시되지 않는 이유는 무엇입니까?**

**A:** 경우에 따라 관리 단위의 하나 이상의 구성원을 추가하거나 제거하는 처리하는 데 몇 **Administrative units** 분 정도 걸릴 수 있습니다. 또는 연결된 리소스의 속성으로 직접 이동하여 작업이 완료되었는지 확인할 수 있습니다. AUs의 사용자 및 그룹에 대한 자세한 내용은 [사용자에 대한 관리 단위 목록](roles-admin-units-add-manage-users.md) 및 그룹의 관리 단위 [목록](roles-admin-units-add-manage-groups.md)참조

**Q: 관리 단위에서 위임된 암호 관리자입니다. 특정 사용자의 암호를 재설정할 수 없는 이유는 무엇입니까?**

**A:** 관리 단위의 관리자는 관리 단위에 할당된 사용자에 대해서만 암호를 재설정할 수 있습니다. 암호 재설정에 실패한 사용자가 할당된 관리 장치에 속해 있는지 확인합니다. 사용자가 동일한 관리 단위에 속하지만 암호를 재설정할 수 없는 경우 사용자에게 할당된 역할을 확인합니다. 

권한 상승을 방지하기 위해 관리 단위 범위의 관리자는 조직 전체 범위가 있는 역할에 할당된 사용자의 암호를 재설정할 수 없습니다.

**Q: 관리 단위가 필요한 이유는 무엇입니까? 범위를 정의하는 방법으로 보안 그룹을 사용하지 않았을까요?**

**A:** 보안 그룹에는 기존 목적 및 권한 부여 모델이 있습니다. 예를 들어 *사용자 관리자는*Azure AD 조직의 모든 보안 그룹의 구성원 자격을 관리할 수 있습니다. 역할은 그룹을 사용하여 Salesforce와 같은 응용 프로그램에 대한 액세스를 관리할 수 있습니다. *사용자 관리자는* "리소스 그룹화" 시나리오를 지원하기 위해 보안 그룹을 확장한 경우 위임 모델 자체를 관리할 수 없습니다. Windows Server Active Directory의 조직 단위와 같은 관리 단위는 광범위한 디렉터리 개체의 범위를 정하는 방법을 제공하기 위한 것입니다. 보안 그룹 자체는 리소스 범위의 구성원이 될 수 있습니다. 보안 그룹을 사용하여 관리자가 관리할 수 있는 보안 그룹 집합을 정의하면 혼란스러울 수 있습니다.

**Q: 관리 단위에 그룹을 추가한다는 것은 무엇을 의미합니까?**

**A:** 관리 단위에 그룹을 추가하면 해당 관리 단위로 범위가 조정되는 *모든 사용자 관리자의* 관리 범위에 그룹 자체가 표시됩니다. 관리 단위의 사용자 관리자는 그룹 자체의 이름과 구성원을 관리할 수 있습니다. 관리 단위에 대 한 사용자 관리자(예: 암호를 재설정)를 관리 하도록 사용자 *관리자권한을* 부여 하지 않습니다. *사용자 관리자에게* 사용자를 관리할 수 있는 권한을 부여하려면 사용자가 관리 단위의 직접 구성원이어야 합니다.

**Q: 리소스(사용자 또는 그룹)가 두 개 이상의 관리 단위의 구성원이 될 수 있습니까?**

**A:** 예. 리소스는 두 개 이상의 관리 단위의 구성원일 수 있습니다. 리소스에 대한 권한이 있는 조직 전체 및 관리 단위 범위의 모든 관리자가 리소스를 관리할 수 있습니다.

**Q: B2C 조직에서 관리 단위를 사용할 수 있습니까?**

**A:** 아니요, B2C 조직에서는 관리 단위를 사용할 수 없습니다.

**Q: 중첩된 관리 단위가 지원되나요?**

**A:** 아니요, 중첩된 관리 단위는 지원되지 않습니다.

**Q: PowerShell 및 그래프 API에서 관리 단위가 지원되나요?**

**A:** 예. [PowerShell cmdlet 설명서](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0-preview) 및 샘플 스크립트에서 관리 장치에 대한 지원을 찾을 [수 있습니다.](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0-preview) 

Microsoft 그래프에서 [관리Unit 리소스 유형에](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/administrativeunit) 대한 지원을 찾습니다.

## <a name="next-steps"></a>다음 단계

- [관리 단위를 사용하여 역할 범위 제한](directory-administrative-units.md)
- [관리 단위 관리](roles-admin-units-manage.md)
