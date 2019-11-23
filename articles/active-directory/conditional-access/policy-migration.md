---
title: Migrate Conditional Access policies - Azure Active Directory
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
# <a name="what-is-a-policy-migration-in-azure-active-directory-conditional-access"></a>What is a policy migration in Azure Active Directory Conditional Access? 

[Conditional Access](../active-directory-conditional-access-azure-portal.md) is a capability of Azure Active directory (Azure AD) that enables you to control how authorized users access your cloud apps. While the purpose is still the same, the release of the new Azure portal has introduced significant improvements to how Conditional Access works.

Azure Portal에서 만들지 않은 정책을 마이그레이션하는 것을 고려해야 하는 이유는 다음과 같습니다.

- 이전에는 처리할 수 없었던 시나리오를 이제 처리할 수 있습니다.
- 통합하여 관리해야 하는 정책의 수를 줄일 수 있습니다.   
- You can manage all your Conditional Access policies in one central location.
- Azure 클래식 포털의 사용이 중지됩니다.   

This article explains what you need to know to migrate your existing Conditional Access policies to the new framework.
 
## <a name="classic-policies"></a>클래식 정책

In the [Azure portal](https://portal.azure.com), the [Conditional Access - Policies](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) page is your entry point to your Conditional Access policies. However, in your environment, you might also have Conditional Access policies you have not created using this page. 이러한 정책은 *클래식 정책*이라고 합니다. Classic policies are Conditional Access policies, you have created in:

- Azure 클래식 포털
- Intune 클래식 포털
- Intune 앱 보호 포털

On the **Conditional Access** page, you can access your classic policies by clicking [**Classic policies (preview)** ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies) in the **Manage** section. 

![Azure Active Directory](./media/policy-migration/71.png)

**클래식 정책** 보기는 다음과 같은 옵션을 제공합니다.

- 클래식 정책을 필터링합니다.
 
   ![Azure Active Directory](./media/policy-migration/72.png)

- 클래식 정책을 사용하지 않도록 설정합니다.

   ![Azure Active Directory](./media/policy-migration/73.png)
   
- Review the settings of a classic policy (and to disable it).

   ![Azure Active Directory](./media/policy-migration/74.png)

클래식 정책을 사용하지 않도록 설정한 경우 이 단계를 더 이상 되돌릴 수 없습니다. 이러한 이유로 **세부 정보** 보기를 사용하여 클래식 정책에서 그룹 멤버십을 수정할 수 있습니다. 

![Azure Active Directory](./media/policy-migration/75.png)

모든 포함된 사용자 및 그룹에 대해 정책을 사용하지 않도록 설정하기 전에 선택한 그룹을 변경하거나 특정 그룹을 제외하여 소수의 테스트 사용자에게 사용하지 않도록 설정된 클래식 정책의 효과를 테스트할 수 있습니다. 

## <a name="azure-ad-conditional-access-policies"></a>Azure AD Conditional Access policies

With Conditional Access in the Azure portal, you can manage all your policies in one central location. Because the implementation of how Conditional Access has changed, you should familiarize yourself with the basic concepts before migrating your classic policies.

다음을 참조하세요.

- [What is Conditional Access in Azure Active Directory](../active-directory-conditional-access-azure-portal.md) to learn about the basic concepts and the terminology.
- [Best practices for Conditional Access in Azure Active Directory](best-practices.md) to get some guidance on deploying Conditional Access in your organization.
- [Require MFA for specific apps with Azure Active Directory Conditional Access](app-based-mfa.md) to familiarize yourself with the user interface in the Azure portal.
 
## <a name="migration-considerations"></a>마이그레이션 고려 사항

In this article, Azure AD Conditional Access policies are also referred to as *new policies*.
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
 
**Exchange Active Sync**가 클라이언트 앱 조건으로 포함된 Office 365 Exchange Online의 [앱 기반](technical-reference.md#approved-client-app-requirement) 클래식 정책에서는 **지원되는** 디바이스 플랫폼과 **지원되지 않는**[디바이스 플랫폼](technical-reference.md#device-platform-condition)이 허용됩니다. 관련된 새 정책에서는 개별 디바이스 플랫폼을 구성할 수 없으며 [지원되는 디바이스 플랫폼](technical-reference.md#device-platform-condition)으로만 지원을 제한할 수 있습니다. 

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
