---
title: 관리 단위 및 FAQ 문제 해결 - Azure Active Directory | 마이크로 소프트 문서
description: Azure Active Directory에서 제한된 범위가 있는 권한 위임에 대한 관리 단위 조사
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
ms.openlocfilehash: 5c9770b60edad6f956ae99c91b94a232cc48bbbd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428148"
---
# <a name="troubleshooting-and-faq-for-administrative-units-in-azure-active-directory"></a>Azure Active Directory의 관리 단위에 대한 문제 해결 및 FAQ

Azure Active Directory(Azure AD)에서 보다 세부적인 관리 제어를 위해 하나 이상의 관리 단위(AUS)로 제한된 범위의 Azure AD 역할에 사용자를 할당할 수 있습니다. 에서 일반적인 작업에 대한 샘플 PowerShell https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0스크립트를 찾을 수 있습니다.

## <a name="frequently-asked-questions"></a>질문과 대답

**Q: 관리 단위를 만들 수 없습니다.**

**A:** 전역 관리자 또는 권한 있는 역할 관리자만 Azure AD에서 관리 단위를 만들 수 있습니다. 관리 단위를 만들려는 사용자에게 전역 관리자 또는 권한 있는 역할 관리자 역할이 할당되어 있는지 확인합니다.

**Q: 관리 단위에 그룹을 추가했지만 그룹의 구성원이 여전히 관리 단위에 표시되지 않습니다.**

**A:** 관리 단위에 그룹을 추가하면 그룹의 모든 구성원이 관리 단위에 추가되지 않습니다. 사용자는 관리 장치에 직접 할당되어야 합니다.

**Q: 방금 관리 단위의 구성원을 추가/제거했으며 UI에 계속 표시됩니다.**

**A:** 관리 단위 **페이지에서** 하나 이상의 구성원을 추가하거나 제거하는 데 몇 분 정도 걸릴 수 있습니다. 관리 단위 아래에 반영될 때까지 몇 분 동안 기다릴 수 있습니다. 또는 연결된 리소스의 속성으로 직접 이동하여 작업이 완료되었는지 확인할 수 있습니다. AUs의 사용자 및 그룹에 대한 자세한 내용은 [사용자에 대한 관리 단위 목록](roles-admin-units-add-manage-users.md) 및 그룹의 관리 단위 [목록](roles-admin-units-add-manage-groups.md)참조

**Q: 관리 단위의 위임된 암호 관리자로서 특정 사용자의 암호를 재설정할 수 없습니다.**

**A:** 관리 단위에 할당된 관리자는 관리 장치에 할당된 사용자에 대해서만 암호를 재설정할 수 있습니다. 암호 재설정이 실패하는 사용자가 역할이 할당된 관리 단위에 속하는지 확인합니다. 사용자가 동일한 관리 장치에 속해 있고 여전히 사용자의 암호를 재설정할 수 없는 경우 사용자에게 할당된 역할을 확인합니다. 권한 상승을 방지하기 위해 관리 단위 범위의 관리자는 조직 전체 범위가 있는 역할에 할당된 사용자의 암호를 재설정할 수 없습니다.

**Q: 관리 단위가 필요한 이유는 무엇입니까? 범위를 정의하는 방법으로 보안 그룹을 사용하지 않았을까요?**

**A:** 보안 그룹에는 기존 목적 및 권한 부여 모델이 있습니다. 예를 들어 사용자 관리자는 Salesforce와 같은 응용 프로그램에 대한 액세스를 관리하기 위해 그룹을 사용하는 것과 같이 Azure AD 조직의 모든 보안 그룹의 구성원 자격을 관리할 수 있습니다. 사용자 관리자는 위임 모델 자체를 관리할 수 있는 권한이 없어야 하며, 이는 "리소스 그룹화" 시나리오를 지원하기 위해 보안 그룹을 확장한 경우 발생할 수 있습니다. Windows 서버 Active Directory의 조직 단위와 같은 관리 단위는 광범위한 디렉터리 개체의 범위를 조정하는 방법을 제공하기 위한 것입니다. 보안 그룹 자체는 리소스 범위의 구성원이 될 수 있습니다. 보안 그룹을 사용하여 관리자가 관리할 수 있는 보안 그룹 집합을 정의하면 혼란스러울 수 있습니다.

**Q: 관리 단위에 그룹을 추가한다는 것은 무엇을 의미합니까?**

**A:** 관리 단위에 그룹을 추가하면 해당 관리 단위로 범위가 조정되는 모든 사용자 관리자의 관리 범위에 그룹 자체가 표시됩니다. 관리 단위의 사용자 관리자는 그룹 자체의 이름과 구성원을 관리할 수 있습니다. 관리 단위에 대한 사용자 관리자는 그룹의 사용자를 관리할 수 있는 권한(예: 암호 재설정)을 부여하지 않습니다. 사용자 관리자에게 사용자를 관리할 수 있는 권한을 부여하려면 사용자가 관리 단위의 직접 구성원이어야 합니다.

**Q: 리소스(사용자 또는 그룹)가 두 개 이상의 관리 단위의 구성원이 될 수 있습니까?**

**A:** 예. 리소스는 두 개 이상의 관리 단위의 구성원일 수 있습니다. 리소스에 대한 권한이 있는 조직 전체 및 관리 단위 범위의 모든 관리자가 리소스를 관리할 수 있습니다.

**Q: B2C 조직에서 관리 단위를 사용할 수 있습니까?**

**A:** 아니요, B2C 조직에서는 관리 단위를 사용할 수 없습니다.

**Q: 중첩된 관리 단위가 지원되나요?**

**A:** 중첩된 관리 단위는 지원되지 않습니다.

**Q: PowerShell 및 그래프 API에서 관리 단위가 지원되나요?**

**A:** 예. 관리 단위에 대 한 [지원은 PowerShell cmdlet 설명서](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0-preview) 및 [샘플 스크립트에](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0-preview)존재 하 고 지원은 [관리단위 리소스 유형에](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/administrativeunit)대 한 Microsoft 그래프에 있습니다.

## <a name="next-steps"></a>다음 단계

- [역할 개요에 대한 범위를 제한하는 관리 단위](directory-administrative-units.md)
- [관리 단위 관리](roles-admin-units-manage.md)