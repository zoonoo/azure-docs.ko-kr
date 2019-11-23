---
title: Access controls in Azure Active Directory Conditional Access
description: Learn how access controls in Azure Active Directory Conditional Access work.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: cac92da744b3d5b7aeaa325c7cc564a3d7e2abdd
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74380804"
---
# <a name="what-are-access-controls-in-azure-active-directory-conditional-access"></a>What are access controls in Azure Active Directory Conditional Access?

With [Azure Active Directory (Azure AD) Conditional Access](../active-directory-conditional-access-azure-portal.md), you can control how authorized users access your cloud apps. In a Conditional Access policy, you define the response ("do this") to the reason for triggering your policy ("when this happens").

![제어](./media/controls/10.png)

In the context of Conditional Access,

- “**When this happens**”는 **조건**이라고 합니다.
- “**Then do this**”는 **액세스 제어**라고 합니다.

The combination of a condition statement with your controls represents a Conditional Access policy.

![제어](./media/controls/61.png)

각 컨트롤은 로그인하는 사용자 또는 시스템에 의해 충족되어야 하는 요구 사항 또는 사용자가 로그인한 후 수행할 수 있는 작업에 대한 제한입니다.

두 가지 형식의 컨트롤이 있습니다.

- **권한 부여 컨트롤** - 액세스를 제어하기 위해
- **세션 컨트롤** - 세션 내에서 액세스를 제한하기 위해

This topic explains the various controls that are available in Azure AD Conditional Access. 

## <a name="grant-controls"></a>권한 부여 컨트롤

권한 부여 컨트롤을 사용하여 액세스를 완전히 차단하거나 원하는 컨트롤을 선택하여 추가 요구 사항으로 액세스를 허용할 수 있습니다. 여러 컨트롤의 경우 다음이 필요할 수 있습니다.

- 충족되도록 선택한 모든 컨트롤(*AND*)
- 충족되도록 선택한 하나의 컨트롤(*OR*)

![제어](./media/controls/18.png)

### <a name="multi-factor-authentication"></a>Multi-factor authentication

이 컨트롤을 사용하여 지정된 클라우드 앱에 액세스하기 위해 다단계 인증을 요구할 수 있습니다. 이 컨트롤은 다음 다단계 공급자를 지원합니다.

- Azure Multi-Factor Authentication
- AD FS(Active Directory Federation Service)와 결합된 온-프레미스 다단계 인증 공급자.

다단계 인증을 사용하면 유효한 사용자의 주 자격 증명에 액세스 할 수 있는 권한이 없는 사용자가 리소스에 액세스하지 못하도록 방지할 수 있습니다.

### <a name="compliant-device"></a>규정 준수 디바이스

You can configure Conditional Access policies that are device-based. The objective of a device-based Conditional Access policy is to only grant access to the selected cloud apps from [managed devices](require-managed-devices.md). 디바이스를 규정 준수 상태로 표시하도록 요구하는 것은 관리 디바이스에 대한 액세스 권한을 제한해야 하는 한 가지 옵션입니다. 디바이스는 Intune(모든 디바이스 OS용) 또는 Windows 10 디바이스용 타사 MDM 시스템에서 규정 준수 상태로 표시할 수 있습니다. Windows 10 이외의 디바이스 OS 형식에 대한 타사 MDM 시스템은 지원되지 않습니다. 

먼저 디바이스를 Azure AD에 등록해야 규정 준수 상태로 표시할 수 있습니다. 디바이스를 등록하려면 다음 세 가지 옵션이 있습니다. 

- Azure AD 등록 디바이스
- Azure AD 조인 디바이스  
- 하이브리드 Azure AD 가입 디바이스

These three options are discussed in the article [What is a device identity?](../devices/overview.md)

For more information, see [how to require managed devices for cloud app access with Conditional Access](require-managed-devices.md).

### <a name="hybrid-azure-ad-joined-device"></a>하이브리드 Azure AD 조인 디바이스

Requiring a Hybrid Azure AD joined device is another option you have to configure device-based Conditional Access policies. 즉, Windows 데스크톱, 랩톱 및 엔터프라이즈 태블릿을 온-프레미스 Active Directory에 조인해야 합니다. If this option is selected, your Conditional Access policy grants access to access attempts made with devices that are joined to your on-premises Active Directory and your Azure Active Directory.  

For more information, see [set up Azure Active Directory device-based Conditional Access policies](require-managed-devices.md).

### <a name="approved-client-app"></a>승인된 클라이언트 앱

직원이 개인 및 회사 작업 모두에 모바일 디바이스를 사용하므로 관리되지 않는 경우에도 디바이스를 사용하여 액세스되는 회사 데이터를 보호하는 기능이 있어야 합니다.
[Intune 앱 보호 정책](https://docs.microsoft.com/intune/app-protection-policy)을 사용하여 모든 MDM(모바일 디바이스 관리) 솔루션과는 별도로 회사의 데이터를 보호할 수 있습니다.

승인된 클라이언트 앱을 사용하여 [Intune 앱 보호 정책](https://docs.microsoft.com/intune/app-protection-policy)을 지원하도록 클라우드 앱에 액세스를 시도하는 클라이언트 앱을 요구할 수 있습니다. 예를 들어 Exchange Online에 대한 액세스를 Outlook 앱으로 제한할 수 있습니다. A Conditional Access policy that requires approved client apps is  also known as [app-based Conditional Access policy](app-based-conditional-access.md). 지원되는 승인된 클라이언트 앱의 목록은 [승인된 클라이언트 앱 요구 사항](technical-reference.md#approved-client-app-requirement)을 참조하세요.

### <a name="app-protection-policy-preview"></a>App protection policy (preview)

직원이 개인 및 회사 작업 모두에 모바일 디바이스를 사용하므로 관리되지 않는 경우에도 디바이스를 사용하여 액세스되는 회사 데이터를 보호하는 기능이 있어야 합니다.
[Intune 앱 보호 정책](https://docs.microsoft.com/intune/app-protection-policy)을 사용하여 모든 MDM(모바일 디바이스 관리) 솔루션과는 별도로 회사의 데이터를 보호할 수 있습니다.

With app protection policy, you can limit access to client applications that have reported to Azure AD has having received [Intune app protection policies](https://docs.microsoft.com/intune/app-protection-policy). For example, you can restrict access to Exchange Online to the Outlook app that has an Intune app protection policy. A Conditional Access policy that requires app protection policy is also known as [app protection-based Conditional Access policy](app-protection-based-conditional-access.md). 

Your device must be registered to Azure AD before an application can be marked as policy protected.

For a list of supported policy protected client apps, see [app protection policy requirement](technical-reference.md#app-protection-policy-requirement).

### <a name="terms-of-use"></a>사용 조건

테넌트의 사용자가 리소스에 대한 액세스 권한을 부여 받기 전에 사용 조건에 동의하도록 요구할 수 있습니다. 관리자는 PDF 문서를 업로드하여 사용 조건을 구성하고 사용자 지정할 수 있습니다. 사용자가 이 제어 범위에 해당하는 경우 사용 조건에 동의한 경우에만 애플리케이션에 대한 액세스가 부여됩니다.

## <a name="custom-controls-preview"></a>사용자 지정 컨트롤(미리 보기)

사용자 지정 컨트롤은 Azure Active Directory Premium P1 버전의 기능입니다. 사용자 지정 컨트롤을 사용하는 경우 Azure Active Directory 외부의 추가 요구 사항을 충족시키기 위해 사용자를 호환 가능한 서비스로 리디렉션합니다. 이 제어를 충족시키기 위해 사용자의 브라우저는 외부 서비스로 리디렉션되고 필요한 인증 또는 유효성 검사 활동을 수행한 다음 Azure Active Directory로 다시 리디렉션됩니다. Azure Active Directory verifies the response and, if the user was successfully authenticated or validated, the user continues in the Conditional Access flow.

These controls allow the use of certain external or custom services as Conditional Access controls, and generally extend the capabilities of Conditional Access.

현재 호환 서비스를 제공하는 공급자는 다음과 같습니다.

- [Duo Security](https://duo.com/docs/azure-ca)
- [Entrust Datacard](https://www.entrustdatacard.com/products/authentication/intellitrust)
- [GSMA](https://mobileconnect.io/azure/)
- [Ping Identity](https://documentation.pingidentity.com/pingid/pingidAdminGuide/index.shtml#pid_c_AzureADIntegration.html)
- RSA
- [SecureAuth](https://docs.secureauth.com/pages/viewpage.action?pageId=47238992#)
- [Silverfort](https://www.silverfort.io/company/using-silverfort-mfa-with-azure-active-directory/)
- [Symantec VIP](https://help.symantec.com/home/VIP_Integrate_with_Azure_AD)
- [Thales (Gemalto)](https://resources.eu.safenetid.com/help/AzureMFA/Azure_Help/Index.htm)
- [Trusona](https://www.trusona.com/docs/azure-ad-integration-guide)

이러한 서비스에 대한 자세한 내용은 공급자에게 직접 문의하세요.

### <a name="creating-custom-controls"></a>사용자 지정 컨트롤 만들기

사용자 지정 컨트롤을 만들려면 먼저 사용할 공급자에게 문의해야 합니다. Each non-Microsoft provider has its own process and requirements to sign up, subscribe, or otherwise become a part of the service, and to indicate that you wish to integrate with Conditional Access. 이 시점에서 공급자는 JSON 형식의 데이터 블록을 제공합니다. This data allows the provider and Conditional Access to work together for your tenant, creates the new control and defines how Conditional Access can tell if your users have successfully performed verification with the provider.

Custom controls cannot be used with Identity Protection's automation requiring multi-factor authentication or to elevate roles in Privileged Identity Manager (PIM).

JSON 데이터를 복사한 다음 관련 텍스트 상자에 붙여넣습니다. 변경 사항을 명시적으로 이해하지 못한 경우 JSON을 변경하지 마세요. 변경하면 공급자와 Microsoft 간의 연결이 끊어져 잠재적으로 사용자가 사용자 계정 밖에서 잠길 수 있습니다.

The option to create a custom control is in the **Manage** section of the **Conditional Access** page.

![제어](./media/controls/82.png)

**새 사용자 지정 컨트롤**을 클릭하면 컨트롤의 JSON 데이터에 대한 텍스트 상자가 있는 블레이드가 열립니다.  

![제어](./media/controls/81.png)

### <a name="deleting-custom-controls"></a>사용자 지정 컨트롤 삭제

To delete a custom control, you must first ensure that it isn’t being used in any Conditional Access policy. 작업이 완료되면 다음을 수행합니다.

1. 사용자 지정 컨트롤 목록으로 이동합니다.
1. …를 클릭합니다.  
1. **삭제**를 선택합니다.

### <a name="editing-custom-controls"></a>사용자 지정 컨트롤 편집

사용자 지정 컨트롤을 편집하려면 현재 컨트롤을 삭제하고 업데이트된 정보로 새 컨트롤을 만들어야 합니다.

## <a name="session-controls"></a>세션 컨트롤

세션 컨트롤은 클라우드 앱 내에서 제한된 환경을 활성화합니다. 세션 컨트롤은 클라우드 앱에서 적용되고 Azure AD가 앱에 제공한 세션에 대한 추가 정보에 의존합니다.

![제어](./media/controls/31.png)

### <a name="use-app-enforced-restrictions"></a>앱에서 적용된 제한 사항 사용

이 컨트롤을 사용하여 Azure AD가 디바이스 정보를 선택한 클라우드 앱에 전달하도록 할 수 있습니다. 디바이스 정보를 사용하면 클라우드 앱이 규정 준수 또는 도메인 조인 디바이스에서 연결이 초기화되는지 여부를 알 수 있습니다. 이 컨트롤은 선택한 클라우드 앱으로 SharePoint Online 및 Exchange Online만 지원합니다. 선택하면 클라우드 앱에서는 디바이스 정보를 사용하여 사용자에게 디바이스 상태에 따라 제한된 환경이나 전체 환경을 제공합니다.

자세한 내용은 다음을 참조하세요.

- [SharePoint Online을 사용하여 제한된 액세스를 사용하도록 설정](https://aka.ms/spolimitedaccessdocs)
- [Exchange Online을 사용하여 제한된 액세스를 사용하도록 설정](https://aka.ms/owalimitedaccess)

## <a name="next-steps"></a>다음 단계

- 조건부 액세스 정책을 구성하는 방법을 알아보려면 [Azure Active Directory 조건부 액세스를 사용하는 특정 앱에 MFA 요구](app-based-mfa.md)를 참조하세요.
- 사용자 환경에 대한 조건부 액세스 정책을 구성할 준비가 완료된 경우 Azure Active Directory의 [조건부 액세스 모범 사례](best-practices.md)를 참조하세요.
