---
title: "Azure Active Directory 조건부 액세스 | Microsoft Docs"
description: "Azure Active Directory에서 조건부 액세스 제어를 사용하여 응용 프로그램에 대한 액세스를 인증할 때 특정 조건을 확인합니다."
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
ms.openlocfilehash: 5a1ce66e02943caedd52976c5dcb3cf75c23bd49
ms.contentlocale: ko-kr
ms.lasthandoff: 05/11/2017


---
# <a name="conditional-access-in-azure-active-directory"></a>Azure Active Directory 조건부 액세스

> [!div class="op_single_selector"]
> * [Azure 포털](active-directory-conditional-access-azure-portal.md)
> * [Azure 클래식 포털](active-directory-conditional-access.md)

모바일 우선, 클라우드 우선 세계에서 Azure Active Directory는 어디에서나 장치, 앱 및 서비스에 대한 Single Sign-On을 가능하게 합니다. 장치(BYOD 포함), 기업 네트워크 외 근무 및 타사 SaaS 앱의 확산에 따라 IT 전문가는 다음 두 가지 대립되는 목표에 직면하고 있습니다.

- 최종 사용자가 언제 어디서나 생산성을 높일 수 있도록 지원
- 언제든지 회사 자산 보호

Azure Active Directory는 생산성을 높이기 위해 회사 자산에 액세스할 수 있는 광범위한 옵션을 사용자에게 제공합니다. 응용 프로그램 액세스 관리를 사용하면 *권한이 있는 사용자*만 Azure Active Directory를 통해 응용 프로그램에 액세스할 수 있습니다. 권한이 있는 사용자가 특정 조건에서 리소스에 액세스하는 방법을 보다 효과적으로 제어하려면 어떻게 해야 합니까? *권한이 있는 사용자*에게도 특정 앱에 대한 액세스를 차단하려는 조건이 있는 경우에는 어떻게 해야 합니까? 예를 들어 권한이 있는 사용자가 신뢰할 수 있는 네트워크에서 특정 앱에 액세스한다면 문제 없겠지만, 신뢰할 수 없는 네트워크에서 이러한 앱에 액세스하는 것을 원하지 않을 수 있습니다. 조건부 액세스를 사용하면 이러한 문제를 해결할 수 있습니다.

조건부 액세스는 특정 조건에 따라 사용자 환경의 응용 프로그램에 대한 액세스를 제어할 수 있는 Azure Active Directory의 기능입니다. 제어 문을 사용하면 추가 요구 사항을 액세스에 연결하거나 액세스를 차단할 수 있습니다. 조건부 액세스의 구현은 정책을 기반으로 합니다. 정책 기반 접근 방식은 원하는 액세스 요구 사항을 반영하므로 구성 환경을 간소화합니다.  

일반적으로 다음 패턴에 기반한 문을 사용하여 액세스 요구 사항을 정의합니다.

![제어](./media/active-directory-conditional-access-azure-portal/10.png)

두 개의 "*this*"를 실제 정보로 바꾸면 다음과 같이 일반적인 정책 문의 예제가 됩니다.

*계약자가 신뢰할 수 없는 네트워크에서 클라우드 앱에 액세스하려고 시도하는 경우 액세스를 차단합니다.*

위 정책 문은 조건부 액세스의 권한을 강조하고 있습니다. 계약자(**주체**)가 기본적으로 조건부 액세스로 클라우드 앱에 액세스할 수 있도록 허용하는 한편 액세스할 수 있는 조건을 정의할 수도 있습니다(**방법**).

Azure Active Directory 조건부 액세스의 컨텍스트에서

- "**When this happens**"는 **조건 문**이라고 합니다.
- "**Then do this**"는 **제어 문**이라고 합니다.

![제어](./media/active-directory-conditional-access-azure-portal/11.png)

조건 문과 제어 문의 조합은 조건부 액세스 정책을 나타냅니다.

![제어](./media/active-directory-conditional-access-azure-portal/12.png)


## <a name="controls"></a>컨트롤

조건부 액세스 정책에서 제어 문은 조건 문을 충족할 때 발생해야 하는 상황을 정의합니다.  
제어 문을 사용하면 추가 요구 사항에 따라 액세스를 차단하거나 액세스를 허용할 수 있습니다.
액세스를 허용하는 정책을 구성할 때는 요구 사항을 하나 이상 선택해야 합니다.   

### <a name="grant-controls"></a>권한 부여 컨트롤
Azure Active Directory의 현재 구현을 사용하면 다음 권한 부여 컨트롤 요구 사항을 구성할 수 있습니다.

![제어](./media/active-directory-conditional-access-azure-portal/05.png)

- **다단계 인증**: 다단계 인증을 통해 강력한 인증을 요구할 수 있습니다. AD FS(Active Directory Federation Service)와 결합된 Azure Multi-Factor 또는 온-프레미스 다단계 인증 공급자를 공급자로 사용할 수 있습니다. 다단계 인증을 사용하면 유효한 사용자의 자격 증명에 액세스 할 수 있는 권한이 없는 사용자가 리소스에 액세스하지 못하도록 방지할 수 있습니다.

- **준수 장치** - 장치 수준에서 조건부 액세스 정책을 설정할 수 있습니다. 정책을 준수하는 컴퓨터 또는 모바일 장치 관리 응용 프로그램에 등록된 모바일 장치만 조직의 리소스에 액세스할 수 있도록 정책을 설정할 수 있습니다. 예를 들어 Intune을 사용하여 장치의 정책 준수를 확인한 다음 사용자가 응용 프로그램에 액세스하려고 할 때 해당 정책을 적용하기 위해 Azure AD에 보고할 수 있습니다. Intune을 사용하여 앱 및 데이터를 보호하는 방법에 대한 자세한 지침은 ‘Microsoft Intune을 사용하여 앱 및 데이터 보호’를 참조하세요. 또한 Intune을 사용하여 분실되거나 도난당한 장치에 대한 데이터 보호도 적용할 수 있습니다. 자세한 내용은 ‘Microsoft Intune을 사용하여 전체 또는 선택적 초기화로 데이터 보호’를 참조하세요.

- **도메인 가입 장치** - Azure Active Directory에 연결하는 데 사용한 장치가 도메인 가입 장치이도록 요구할 수 있습니다. 이 정책은 Windows 데스크톱, 랩톱 및 엔터프라이즈 태블릿에 적용됩니다. Azure AD를 사용하여 도메인 가입 장치의 자동 등록을 설정하는 방법에 대한 자세한 내용은 [Windows 도메인에 가입된 장치의 Azure Active Directory 자동 장치 등록](active-directory-conditional-access-automatic-device-registration.md)을 참조하세요.

조건부 액세스 정책에서 요구 사항을 둘 이상 선택한 경우 해당 조건을 적용하기 위한 요구 사항을 구성할 수도 있습니다. 선택한 모든 제어 문 또는 그 중 하나를 요구하도록 선택할 수 있습니다.

![제어](./media/active-directory-conditional-access-azure-portal/06.png)

### <a name="session-controls"></a>세션 컨트롤
세션 컨트롤을 통해 클라우드 앱 내에서 환경을 제한할 수 있습니다. 세션 컨트롤은 클라우드 앱에서 적용되고 Azure AD가 앱에 제공한 세션에 대한 추가 정보에 의존합니다.

![제어](./media/active-directory-conditional-access-azure-portal/session-control-pic.png)

#### <a name="use-app-enforced-restrictions"></a>앱에서 적용된 제한 사항 사용
이 컨트롤을 사용하여 Azure AD가 장치 정보를 클라우드 앱에 전달하도록 할 수 있습니다. 클라우드 앱은 이 컨트롤을 통해 사용자가 규격 장치 또는 도메인 가입 장치에서 들어오는지 확인합니다. 이 컨트롤은 현재 SharePoint를 통해 클라우드 앱으로만 지원됩니다. SharePoint에서는 장치 정보를 사용하여 사용자에게 장치 상태에 따라 제한된 환경이나 전체 환경을 제공합니다.
SharePoint를 통해 제한된 액세스를 요구하는 방법을 알아보려면[여기](https://aka.ms/spolimitedaccessdocs)로 이동합니다.

## <a name="condition-statement"></a>조건 문

이전 섹션에서는 제어 문 형식으로 리소스에 대한 액세스를 차단하거나 제한하기 위해 지원되는 옵션을 소개했습니다. 조건부 액세스 정책에서는 제어 문이 조건 문의 형식으로 적용될 수 있도록 충족해야 할 조건을 정의합니다.  

조건 문에는 다음과 같은 할당이 포함될 수 있습니다.

![제어](./media/active-directory-conditional-access-azure-portal/07.png)


- **주체** - 대부분의 경우 제어 문을 특정 사용자 집합에 적용할 것입니다. 조건 문에서 정책이 적용되는 사용자 및 그룹을 선택하여 이러한 집합을 정의할 수 있습니다. 필요한 경우 사용자 집합을 제외하여 정책 대상에서 명시적으로 제외할 수도 있습니다.  
사용자 및 그룹을 선택하여 정책이 적용되는 사용자 범위를 정의할 수 있습니다.    

    ![제어](./media/active-directory-conditional-access-azure-portal/08.png)



- **대상** - 일반적으로, 보호 관점에서 다른 앱보다 특별한 주의가 필요한, 사용자 환경에서 실행되는 특정 앱이 있습니다. 예를 들어, 중요한 데이터에 액세스할 수 있는 앱이 영향을 받을 수 있습니다.
클라우드 앱을 선택하면 정책이 적용되는 클라우드 앱의 범위를 정의할 수 있습니다. 필요한 경우 정책에서 일단의 앱을 명시적으로 제외할 수도 있습니다.

    ![제어](./media/active-directory-conditional-access-azure-portal/09.png)


- **방법** - 앱에 대한 액세스를 제어할 수 있는 조건에서 수행하는 한, 사용자가 클라우드 앱에 액세스하는 방법에 대한 추가 제어 문을 부과할 필요는 없습니다. 그러나 신뢰할 수 없는 네트워크 또는 정책을 준수하지 않는 장치에서 클라우드 앱에 대한 액세스를 수행하는 경우에는 상황이 다를 수 있습니다. 조건 문에서 앱에 대한 액세스를 수행하는 방법에 대한 추가 요구 사항이 있는 특정 액세스 조건을 정의할 수 있습니다.

    ![조건](./media/active-directory-conditional-access-azure-portal/21.png)


## <a name="conditions"></a>조건

Azure Active Directory의 현재 구현에서 다음 영역에 대한 조건을 정의할 수 있습니다.

- **로그인 위험** – 로그인 위험은 로그인 시도를 사용자 계정의 합법적인 소유자가 수행하지 않았을 가능성을 추적하기 위해 Azure Active Directory에서 사용하는 개체입니다. 이 개체에서 가능성(높음, 중간 또는 낮음)은 [로그인 위험 수준](active-directory-reporting-risk-events.md#risk-level)이라는 특성의 형태로 저장됩니다. Azure Active Directory에서 로그인 위험을 검색한 경우 이 개체는 사용자의 로그인 동안 생성됩니다. 자세한 내용은 [위험한 로그인](active-directory-identityprotection.md#risky-sign-ins)을 참조하세요.  
계산된 로그인 위험 수준을 조건부 액세스 정책의 조건으로 사용할 수 있습니다. 

    ![조건](./media/active-directory-conditional-access-azure-portal/22.png)

- **장치 플랫폼** - 장치 플랫폼은 장치(Android, iOS, Windows Phone, Windows)에서 실행되는 운영 체제를 특징으로 합니다. 정책의 대상에 포함된 장치 플랫폼뿐만 아니라 정책에서 제외되는 장치 플랫폼도 정의할 수 있습니다.  
정책에서 장치 플랫폼을 사용하려면 먼저 [구성] 토글을 **예**로 변경한 다음, 정책이 적용되는 장치 플랫폼을 모두 또는 개별적으로 선택합니다. 개별 장치 플랫폼을 선택하면 해당 플랫폼에만 정책이 적용됩니다. 이 경우 지원되는 다른 플랫폼에 대한 로그인은 정책의 영향을 받지 않습니다.

    ![조건](./media/active-directory-conditional-access-azure-portal/02.png)

- **위치** - 위치는 Azure Active Directory에 연결하는 데 사용한 클라이언트의 IP 주소로 식별됩니다. 이 조건에서는 신뢰할 수 있는 IP를 잘 알고 있어야 합니다. 신뢰할 수 있는 IP는 조직의 로컬 인트라넷을 나타내는 신뢰할 수 있는 IP 주소 범위를 정의할 수 있는 다단계 인증 기능입니다. 위치 조건을 구성하면 신뢰할 수 있는 IP를 통해 조직의 네트워크와 다른 모든 위치에서 수행되는 연결을 구별할 수 있습니다. 자세한 내용은 [신뢰할 수 있는 IP](../multi-factor-authentication/multi-factor-authentication-whats-next.md#trusted-ips)를 참조하세요.  
모든 위치 또는 모든 신뢰할 수 있는 IP를 포함하거나, 모든 신뢰할 수 있는 IP를 제외할 수 있습니다.

    ![조건](./media/active-directory-conditional-access-azure-portal/03.png)


- **클라이언트 앱** - 클라이언트 앱은 Azure Active Directory에 연결하는 데 사용한 일반적 수준의 앱(웹 브라우저, 모바일 앱, 데스크톱 클라이언트)이거나, 구체적으로 Exchange Active Sync를 선택할 수 있습니다.  
레거시 인증은 최신 인증을 사용하지 않는 이전 Office 클라이언트와 같은 기본 인증을 사용하는 클라이언트를 의미합니다. 현재 레거시 인증에서는 조건부 액세스를 지원하지 않습니다.

    ![조건](./media/active-directory-conditional-access-azure-portal/04.png)


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

조건부 액세스 정책을 구성할 때 알아야 할 사항과 수행하지 않아야 할 사항에 대한 자세한 내용은 다음을 참조하세요. 
