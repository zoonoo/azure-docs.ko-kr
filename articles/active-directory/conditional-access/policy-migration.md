---
title: 조건부 액세스 정책 마이그레이션-Azure Active Directory
description: Azure Portal에서 클래식 정책을 마이그레이션하기 위해 알아야 하는 작업을 자세히 살펴봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75d664f6e61dbbaaf0b8ab74c392596a206ff644
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74380548"
---
# <a name="what-is-a-policy-migration-in-azure-active-directory-conditional-access"></a>조건부 액세스 Azure Active Directory에서 정책 마이그레이션 이란? 

[조건부 액세스](../active-directory-conditional-access-azure-portal.md) 는 권한 있는 사용자가 클라우드 앱에 액세스 하는 방법을 제어 하는 데 사용할 수 있는 azure AD (azure Active directory)의 기능입니다. 용도는 동일 하지만, 새 Azure Portal의 릴리스는 조건부 액세스의 작동 방식에 대 한 상당한 개선 사항을 도입 했습니다.

Azure Portal에서 만들지 않은 정책을 마이그레이션하는 것을 고려해야 하는 이유는 다음과 같습니다.

- 이전에는 처리할 수 없었던 시나리오를 이제 처리할 수 있습니다.
- 통합하여 관리해야 하는 정책의 수를 줄일 수 있습니다.   
- 모든 조건부 액세스 정책을 하나의 중앙 위치에서 관리할 수 있습니다.
- Azure 클래식 포털의 사용이 중지됩니다.   

이 문서에서는 기존 조건부 액세스 정책을 새 프레임 워크로 마이그레이션하기 위해 알아야 할 사항을 설명 합니다.
 
## <a name="classic-policies"></a>클래식 정책

[Azure Portal](https://portal.azure.com) [에서 조건부 액세스 정책 페이지는](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) 조건부 액세스 정책에 대 한 진입점입니다. 그러나 사용자 환경에서이 페이지를 사용 하 여 만든 조건부 액세스 정책도 있을 수 있습니다. 이러한 정책은 *클래식 정책*이라고 합니다. 클래식 정책은 다음에서 만든 조건부 액세스 정책입니다.

- Azure 클래식 포털
- Intune 클래식 포털
- Intune 앱 보호 포털

**조건부 액세스** 페이지의 **관리** 섹션에서 [**클래식 정책 (미리 보기)** ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies) 을 클릭 하 여 클래식 정책에 액세스할 수 있습니다. 

![Azure Active Directory](./media/policy-migration/71.png)

**클래식 정책** 보기는 다음과 같은 옵션을 제공합니다.

- 클래식 정책을 필터링합니다.
 
   ![Azure Active Directory](./media/policy-migration/72.png)

- 클래식 정책을 사용하지 않도록 설정합니다.

   ![Azure Active Directory](./media/policy-migration/73.png)
   
- 클래식 정책의 설정을 검토 하 고 사용 하지 않도록 설정 합니다.

   ![Azure Active Directory](./media/policy-migration/74.png)

클래식 정책을 사용하지 않도록 설정한 경우 이 단계를 더 이상 되돌릴 수 없습니다. 이러한 이유로 **세부 정보** 보기를 사용하여 클래식 정책에서 그룹 멤버십을 수정할 수 있습니다. 

![Azure Active Directory](./media/policy-migration/75.png)

모든 포함된 사용자 및 그룹에 대해 정책을 사용하지 않도록 설정하기 전에 선택한 그룹을 변경하거나 특정 그룹을 제외하여 소수의 테스트 사용자에게 사용하지 않도록 설정된 클래식 정책의 효과를 테스트할 수 있습니다. 

## <a name="azure-ad-conditional-access-policies"></a>Azure AD 조건부 액세스 정책

Azure Portal의 조건부 액세스를 사용 하 여 하나의 중앙 위치에서 모든 정책을 관리할 수 있습니다. 조건부 액세스를 변경 하는 방법의 구현이 있으므로 클래식 정책을 마이그레이션하기 전에 기본 개념을 숙지 해야 합니다.

참조

- 기본 개념과 용어에 대 한 자세한 내용은 [Azure Active Directory의 조건부 액세스 기능을 사용](../active-directory-conditional-access-azure-portal.md) 합니다.
- 조직에서 조건부 액세스를 배포 하는 방법에 대 한 몇 가지 지침을 얻기 위해 [Azure Active Directory의 조건부 액세스에 대 한 모범 사례](best-practices.md) 입니다.
- [Azure Active Directory 조건부 액세스를 사용 하 여 특정 앱에 대해 MFA를 요구](app-based-mfa.md) 하 여 Azure Portal 사용자 인터페이스를 숙지 합니다.
 
## <a name="migration-considerations"></a>마이그레이션 고려 사항

이 문서에서는 Azure AD 조건부 액세스 정책을 *새 정책이*라고도 합니다.
클래식 정책도 사용하지 않도록 설정하거나 제거할 때까지 새 정책과 함께 계속 작동합니다. 

정책 통합 컨텍스트에서 중요한 사항은 다음과 같습니다.

- 클래식 정책의 경우 특정 클라우드 앱과 연결되어 있지만 새 정책에서는 필요한 만큼 많은 클라우드 앱을 선택할 수 있습니다.
- 클라우드 앱에 대해 클래식 정책 및 새 정책을 제어하는 경우 모든 제어(*AND*)가 이행되어야 합니다. 
- 새 정책에서는 다음을 수행할 수 있습니다.
   - 시나리오에 필요한 경우 여러 조건을 결합합니다. 
   - 여러 권한 부여 요구 사항을 액세스 제어로 선택하고 논리 *OR*(선택한 제어 중 하나가 필요함) 또는 논리 *AND*(선택한 모든 제어가 필요함)로 결합합니다.

   ![Azure Active Directory](./media/policy-migration/25.png)

### <a name="office-365-exchange-online"></a>Office 365 Exchange Online

**Exchange Active Sync**가 클라이언트 앱 조건으로 포함된 **Office 365 Exchange Online** 클래식 정책을 마이그레이션하는 경우 하나의 새 정책으로 통합하지 못할 수 있습니다. 

예를 들어 모든 클라이언트 앱 유형을 지원하려는 경우에 해당합니다. **Exchange Active Sync**가 클라이언트 앱 조건으로 있는 새 정책에서는 다른 클라이언트 앱을 선택할 수 없습니다.

![Azure Active Directory](./media/policy-migration/64.png)

클래식 정책에 여러 조건이 포함되어 있는 경우에도 하나의 새 정책으로 통합할 수 없습니다. **Exchange Active Sync**가 클라이언트 앱 조건으로 구성되어 있는 새 정책은 다른 조건을 지원하지 않습니다.   

![Azure Active Directory](./media/policy-migration/08.png)

**Exchange Active Sync**가 클라이언트 앱 조건으로 구성된 새 정책이 있는 경우 다른 모든 조건이 구성되지 않도록 해야 합니다. 

![Azure Active Directory](./media/policy-migration/16.png)
 
[Exchange Active Sync](technical-reference.md#approved-client-app-requirement)가 클라이언트 앱 조건으로 포함된 Office 365 Exchange Online의 **앱 기반** 클래식 정책에서는 **지원되는** 디바이스 플랫폼과 **지원되지 않는**[디바이스 플랫폼](technical-reference.md#device-platform-condition)이 허용됩니다. 관련된 새 정책에서는 개별 디바이스 플랫폼을 구성할 수 없으며 [지원되는 디바이스 플랫폼](technical-reference.md#device-platform-condition)으로만 지원을 제한할 수 있습니다. 

![Azure Active Directory](./media/policy-migration/65.png)

다음과 같은 경우에는 **Exchange Active Sync**가 클라이언트 앱 조건으로 포함된 여러 클래식 정책을 통합할 수 있습니다.

- **Exchange Active Sync**만 조건으로 포함된 경우 
- 여러 액세스 권한 부여 조건이 구성되어 있는 경우

한 가지 일반적인 시나리오는 다음과 같은 통합입니다.

- Azure 클래식 포털에서 디바이스 기반 클래식 정책 통합 
- Intune 앱 보호 포털에서 앱 기반 클래식 정책 통합 
 
이 경우 두 요구 사항이 모두 선택된 하나의 새 정책으로 클래식 정책을 통합할 수 있습니다.

![Azure Active Directory](./media/policy-migration/62.png)

### <a name="device-platforms"></a>디바이스 플랫폼

[앱 기반 제어](technical-reference.md#approved-client-app-requirement)를 사용하는 클래식 정책은 iOS 및 Android에 [디바이스 플랫폼 조건](technical-reference.md#device-platform-condition)으로 미리 구성되어 있습니다. 

새 정책에서는 개별적으로 지원할 [디바이스 플랫폼](technical-reference.md#device-platform-condition)을 선택해야 합니다.

![Azure Active Directory](./media/policy-migration/41.png)

## <a name="next-steps"></a>다음 단계

- 조건부 액세스 정책을 구성하는 방법을 알아보려면 [Azure Active Directory 조건부 액세스를 사용하는 특정 앱에 MFA 요구](app-based-mfa.md)를 참조하세요.
- 사용자 환경에 대한 조건부 액세스 정책을 구성할 준비가 완료된 경우 Azure Active Directory의 [조건부 액세스 모범 사례](best-practices.md)를 참조하세요. 
