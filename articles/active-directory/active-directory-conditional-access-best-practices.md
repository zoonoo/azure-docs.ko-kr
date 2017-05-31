---
title: "Azure Active Directory의 조건부 액세스 모범 사례 | Microsoft Docs"
description: "조건부 액세스 정책을 구성할 때 알아야 할 사항과 수행하지 않아야 할 사항에 대해 자세히 알아봅니다."
services: active-directory
keywords: "앱에 조건부 액세스, Azure AD로 조건부 액세스, 회사 리소스에 대한 액세스 보호, 조건부 액세스 정책"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/11/2017
ms.author: markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 8564f75f4e90aa7c3b4f93823b5202354c8bba3a
ms.contentlocale: ko-kr
ms.lasthandoff: 05/11/2017


---
# <a name="best-practices-for-conditional-access-in-azure-active-directory"></a>Azure Active Directory의 조건부 액세스 모범 사례

이 항목에서는 조건부 액세스 정책을 구성할 때 알아야 할 사항과 수행하지 않아야 할 사항에 대한 정보를 제공합니다. 이 항목을 읽기 전에 [Azure Active Directory 조건부 액세스](active-directory-conditional-access-azure-portal.md)에 설명된 개념 및 용어에 익숙해져야 합니다.

## <a name="what-you-should-know"></a>알아야 할 사항

### <a name="do-i-need-to-assign-a-user-to-my-policy"></a>내 정책에 사용자를 할당해야 합니까?

조건부 액세스 정책을 구성할 때는 적어도 하나의 그룹을 할당해야 합니다. 할당된 사용자와 그룹이 없는 조건부 액세스 정책은 트리거되지 않습니다.

여러 사용자와 그룹을 정책에 할당하려는 경우 하나의 사용자 또는 그룹을 할당하여 소규모로 시작한 후에 구성을 테스트해야 합니다. 그런 다음 정책이 예상대로 작동하면 해당 정책에 추가로 할당할 수 있습니다.  


### <a name="how-are-assignments-evaluated"></a>할당은 어떻게 평가됩니까?

모든 할당은 논리적으로 **and**가 적용됩니다. 할당을 둘 이상 구성한 경우 모든 할당이 충족되어야 정책을 트리거할 수 있습니다.  

조직의 네트워크 외부에서 수행되는 모든 연결에 적용되는 위치 조건을 구성해야 하는 경우 다음을 수행할 수 있습니다.

- **모든 위치** 포함
- **모든 신뢰할 수 있는 IP** 제외

### <a name="what-happens-if-you-have-policies-in-the-azure-classic-portal-and-azure-portal-configured"></a>Azure 클래식 포털과 Azure Portal에서 정책을 구성하면 어떻게 됩니까?  
두 정책은 모두 Azure Active Directory에서 적용되며 모든 요구 사항을 충족하는 경우에만 사용자가 액세스 권한을 획득할 수 있습니다.

### <a name="what-happens-if-you-have-policies-in-the-intune-silverlight-portal-and-the-azure-portal"></a>Intune Silverlight 포털과 Azure Portal에 정책이 있으면 어떻게 됩니까?
두 정책은 모두 Azure Active Directory에서 적용되며 모든 요구 사항을 충족하는 경우에만 사용자가 액세스 권한을 획득할 수 있습니다.

### <a name="what-happens-if-i-have-multiple-policies-for-the-same-user-configured"></a>동일한 사용자에 대해 여러 정책을 구성하면 어떻게 됩니까?  
Azure Active Directory는 모든 로그인에 대해 모든 정책을 평가하고, 사용자에게 액세스 권한을 부여하기 전에 모든 요구 사항이 충족되는지 확인합니다.


### <a name="does-conditional-access-work-with-exchange-activesync"></a>조건부 액세스가 Exchange ActiveSync에서 작동합니까?

예, 조건부 액세스 정책에서 Exchange ActiveSync를 사용할 수 있습니다.


### <a name="what-happens-if-i-require-multi-factor-authentication-or-a-compliant-device"></a>다단계 인증이나 준수 장치가 필요하면 어떻게 되나요?

현재, 장치에 관계 없이 다단계 인증을 수행하도록 요구됩니다.


## <a name="what-you-should-avoid-doing"></a>금지해야 할 기능

조건부 액세스 프레임 워크는 훌륭한 구성 유연성을 제공합니다. 그러나 훌륭한 유연성은 잘못된 결과를 방지하기 위해 해제하기 전에 각 구성 정책을 신중하게 검토해야 한다는 것을 의미하기도 합니다. 이 컨텍스트에서 **모든 사용자 / 그룹 / 응용 프로그램**등 완전한 집합에 영향을 미치는 할당에 특별한 주의를 기울여야 합니다.

사용자 환경에서 다음과 같은 구성을 피해야 합니다.


**모든 사용자에 대한 모든 클라우드 앱:**

- **액세스 차단** - 이 구성은 사용자 전체 조직을 차단하므로 사용하지 않는 것이 좋습니다.

- **호환 장치가 필요** - 장치를 아직 등록하지 않은 사용자의 경우 이 정책은 Intune 포털에 대한 액세스를 비롯한 모든 액세스를 차단합니다. 등록된 장치가 없는 관리자의 경우 이 정책은 정책을 변경하기 위해 Azure Portal로 다시 돌아가지 않도록 차단합니다.

- **도메인 가입 필요** - 이 정책 차단 액세스에도 도메인에 가입된 장치가 아직 없는 경우에 조직의 모든 사용자에 대한 액세스를 차단할 가능성이 있습니다.


**모든 사용자 경우 모든 클라우드 앱, 모든 장치 플랫폼은 다음과 같습니다.**

- **액세스 차단** - 이 구성은 사용자 전체 조직을 차단하므로 사용하지 않는 것이 좋습니다.


## <a name="common-scenarios"></a>일반적인 시나리오

### <a name="requiring-multi-factor-authentication-for-apps"></a>앱에 다단계 인증 필요

대다수 사용 환경에서는 다른 앱보다 특히 높은 수준의 보호를 요구하는 앱이 있습니다.
예를 들어 중요한 데이터에 액세스할 수 있는 앱의 경우가 여기에 해당합니다.
이러한 앱에 또 다른 보호 계층을 추가하려는 경우 사용자가 이러한 앱에 액세스할 때 다단계 인증을 요구하는 조건부 액세스 정책을 구성할 수 있습니다.


### <a name="requiring-multi-factor-authentication-for-access-from-networks-that-are-not-trusted"></a>신뢰할 수 없는 네트워크에서 액세스하기 위한 다단계 인증 필요

이 시나리오는 다단계 인증에 대한 요구 사항을 추가하기 때문에 이전 시나리오와 비슷합니다.
그러나 주요 차이점은 이 요구 사항에 대한 조건에 있습니다.  
이전 시나리오는 중요한 데이터에 액세스할 수 있는 앱에 초점을 맞추었지만, 이 시나리오는 신뢰할 수 있는 위치에 초점을 맞추고 있습니다.  
즉 사용자가 신뢰할 수 없는 네트워크에서 앱에 액세스하는 경우 다단계 인증에 대한 요구 사항이 있을 수 있습니다.


### <a name="only-trusted-devices-can-access-office-365-services"></a>신뢰할 수 있는 장치만 Office 365 서비스에 액세스 가능

사용자 환경에서 Intune을 사용하는 경우 Azure 콘솔에서 조건부 액세스 정책 인터페이스를 즉시 사용할 수 있습니다.

많은 Intune 고객이 조건부 액세스를 사용하여 신뢰할 수 있는 장치만 Office 365 서비스에 액세스할 수 있도록 합니다. 즉 모바일 장치가 Intune에 등록되고 준수 정책 요구 사항을 충족하며 Windows PC가 온-프레미스 도메인에 가입되어 있음을 의미합니다. 주요 개선 사항은 각 Office 365 서비스마다 동일한 정책을 설정할 필요가 없다는 것입니다.  즉, 새 정책을 만들 때 조건부 액세스로 보호하려는 각 Office 365 앱을 포함하도록 클라우드 앱을 구성합니다.

## <a name="next-steps"></a>다음 단계

조건부 액세스 정책을 구성하는 방법을 알아보려면 [Azure Active Directory에서 조건부 액세스 시작](active-directory-conditional-access-azure-portal-get-started.md)을 참조하세요.

