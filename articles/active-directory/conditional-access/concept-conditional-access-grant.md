---
title: 조건부 액세스 정책에서 컨트롤 부여-Azure Active Directory
description: Azure AD 조건부 액세스 정책의 grant 컨트롤은 무엇 인가요?
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 07/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: a59939fc7988e1a94bdfb9fac2d77011422e4983
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87274683"
---
# <a name="conditional-access-grant"></a>조건부 액세스: Grant

조건부 액세스 정책 내에서 관리자는 액세스 제어를 사용 하 여 리소스에 대 한 액세스를 허용 하거나 차단할 수 있습니다.

![Multi-factor authentication을 요구 하는 권한 부여 컨트롤을 사용 하는 조건부 액세스 정책](./media/concept-conditional-access-grant/conditional-access-grant.png)

## <a name="block-access"></a>액세스 차단

블록은 모든 할당을 고려 하 여 조건부 액세스 정책 구성에 따라 액세스를 방지 합니다.

Block은 적절 한 정보를 사용 하 여 wielded 해야 하는 강력한 컨트롤입니다. 블록 문이 있는 정책에 의도 하지 않은 부작용이 발생할 수 있습니다. 적절 한 테스트 및 유효성 검사는 대규모을 활성화 하기 전에 중요 합니다. 관리자는 [조건부 액세스 보고서 전용 모드](concept-conditional-access-report-only.md) 와 같은 도구를 사용 하 고 변경을 수행할 때 [조건부 액세스의 What If 도구](what-if-tool.md) 를 활용 해야 합니다.

## <a name="grant-access"></a>액세스 권한 부여

관리자는 액세스 권한을 부여할 때 하나 이상의 컨트롤을 적용 하도록 선택할 수 있습니다. 이러한 컨트롤에는 다음 옵션이 포함 됩니다. 

- [Multi-factor authentication 필요 (Azure Multi-Factor Authentication)](../authentication/concept-mfa-howitworks.md)
- [장치를 규격으로 표시 해야 함 (Microsoft Intune)](/intune/protect/device-compliance-get-started)
- [하이브리드 Azure AD 조인 장치 필요](../devices/concept-azure-ad-join-hybrid.md)
- [승인된 클라이언트 앱 필요](app-based-conditional-access.md)
- [앱 보호 정책 필요](app-protection-based-conditional-access.md)
- [암호 변경 필요](#require-password-change)

관리자가 이러한 옵션을 결합 하도록 선택 하면 다음 방법을 선택할 수 있습니다.

- 모든 선택 된 컨트롤 필요 (컨트롤 **및** 컨트롤)
- 선택한 컨트롤 (컨트롤 **또는** 컨트롤) 중 하나가 필요 합니다.

기본적으로 조건부 액세스에는 선택한 모든 컨트롤이 필요 합니다.

### <a name="require-multi-factor-authentication"></a>다단계 인증 필요

이 확인란을 선택 하면 사용자가 Azure Multi-Factor Authentication를 수행 해야 합니다. Azure Multi-Factor Authentication 배포에 대 한 자세한 내용은 [클라우드 기반 azure Multi-Factor Authentication 배포 계획](../authentication/howto-mfa-getstarted.md)문서에서 찾을 수 있습니다.

### <a name="require-device-to-be-marked-as-compliant"></a>디바이스를 준수 상태로 표시해야 함

Microsoft Intune를 배포한 조직은 장치에서 반환 된 정보를 사용 하 여 특정 규정 준수 요구 사항을 충족 하는 장치를 식별할 수 있습니다. 이 정책 준수 정보는 Intune에서 Azure AD로 전달 되며, 조건부 액세스는 리소스에 대 한 액세스를 부여 하거나 차단 하는 결정을 내릴 수 있습니다. 규정 준수 정책에 대 한 자세한 내용은 Intune을 [사용 하 여 조직의 리소스에 대 한 액세스를 허용 하는 장치에 대 한 규칙 설정](/intune/protect/device-compliance-get-started)문서를 참조 하세요.

장치는 Intune (장치 OS의 경우) 또는 타사 MDM system for Windows 10 장치에서 준수로 표시 될 수 있습니다. Jamf pro는 유일 하 게 지원 되는 타사 MDM 시스템입니다. 통합에 대 한 자세한 내용은 [Jamf Pro를 Intune과 통합](/intune/protect/conditional-access-integrate-jamf)하 여 준수 문서를 참조 하세요.

장치가 준수 상태로 표시 되기 전에 Azure AD에 등록 되어 있어야 합니다. 장치 등록에 대 한 자세한 내용은 [장치 Id 정의](../devices/overview.md)문서에서 찾을 수 있습니다.

### <a name="require-hybrid-azure-ad-joined-device"></a>하이브리드 Azure AD 조인 디바이스 필요

조직에서는 조건부 액세스 정책의 일부로 장치 id를 사용 하도록 선택할 수 있습니다. 조직에서는이 확인란을 사용 하 여 장치가 하이브리드 Azure AD에 가입 되도록 요구할 수 있습니다. 장치 id에 대 한 자세한 내용은 [장치 id 란?](../devices/overview.md)문서를 참조 하세요.

[장치 코드 OAuth 흐름](../develop/v2-oauth2-device-code.md)을 사용 하는 경우 관리 되는 장치 부여 컨트롤 필요 또는 장치 상태 조건이 지원 되지 않습니다. 이는 인증을 수행 하는 장치에서 코드를 제공 하는 장치에 장치 상태를 제공할 수 없고 토큰의 장치 상태가 인증을 수행 하는 장치에 잠겨 있기 때문입니다. 대신 multi-factor authentication 권한 부여 제어를 사용 해야 합니다.

### <a name="require-approved-client-app"></a>승인된 클라이언트 앱 필요

조직에서 선택한 클라우드 앱에 대 한 액세스를 승인 된 클라이언트 앱에서 수행 해야 하도록 요구할 수 있습니다. 이러한 승인 된 클라이언트 앱은 모든 MDM (모바일 장치 관리) 솔루션과 독립적인 [Intune 앱 보호 정책을](/intune/app-protection-policy) 지원 합니다.

이 권한 부여 컨트롤을 활용 하기 위해 조건부 액세스를 사용 하려면 장치를 Azure Active Directory에 등록 해야 합니다. 그러면 broker 앱을 사용 해야 합니다. 브로커 앱은 iOS의 경우 Microsoft Authenticator, Android 디바이스의 경우 Microsoft 회사 포털일 수 있습니다. 사용자가 인증을 시도할 때 broker 앱이 장치에 설치 되어 있지 않은 경우 사용자는 broker 앱을 설치 하기 위해 앱 스토어로 리디렉션됩니다.

이 설정은 다음 iOS 및 Android 앱에 적용 됩니다.

- Microsoft Azure Information Protection
- Microsoft Bookings
- Microsoft Cortana
- Microsoft Dynamics 365
- Microsoft Edge
- Microsoft Excel
- Microsoft Power Automate
- Microsoft Invoicing
- Microsoft Kaizala
- Microsoft Launcher
- Microsoft Office
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft PowerApps
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- 비즈니스용 Microsoft Skype
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Microsoft To-Do
- Microsoft Visio
- Microsoft Word
- Microsoft Yammer
- Microsoft Whiteboard

**주의**

- 승인된 클라이언트 앱은 Intune 모바일 애플리케이션 관리 기능을 지원합니다.
- **승인된 클라이언트 앱 필요** 요구 사항:
   - 디바이스 플랫폼 조건에서는 iOS 및 Android만 지원됩니다.
   - 장치를 등록 하려면 broker 앱이 필요 합니다. IOS에서 broker 앱은 Microsoft Authenticator 되며 Android에서 Intune 회사 포털 앱입니다.
- 조건부 액세스는 InPrivate 모드에서 승인 된 클라이언트 앱으로 Microsoft Edge를 고려할 수 없습니다.

구성 예제는 [방법: 조건부 액세스를 사용 하는 클라우드 앱 액세스를 위해 승인 된 클라이언트 앱 필요](app-based-conditional-access.md) 문서를 참조 하세요.

### <a name="require-app-protection-policy"></a>앱 보호 정책 필요

조건부 액세스 정책에서 선택한 클라우드 앱에 액세스할 수 있으려면 먼저 클라이언트 앱에 [Intune 앱 보호 정책이](/intune/app-protection-policy) 있어야 합니다. 

이 권한 부여 컨트롤을 활용 하기 위해 조건부 액세스를 사용 하려면 장치를 Azure Active Directory에 등록 해야 합니다. 그러면 broker 앱을 사용 해야 합니다. 브로커 앱은 iOS의 경우 Microsoft Authenticator, Android 디바이스의 경우 Microsoft 회사 포털일 수 있습니다. 사용자가 인증을 시도할 때 broker 앱이 장치에 설치 되어 있지 않은 경우 사용자는 broker 앱을 설치 하기 위해 앱 스토어로 리디렉션됩니다.

이 설정은 다음 클라이언트 앱에 적용됩니다.

- Microsoft Cortana
- Microsoft Edge
- Microsoft Excel
- Microsoft Office
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Word
- Intune의 여러 줄
- 9 메일-전자 메일 & 달력

> [!NOTE]
> Microsoft Kaizala, 비즈니스용 Microsoft Skype 및 Microsoft Visio는 **앱 보호 정책 부여 필요** 를 지원 하지 않습니다. 이러한 앱이 작동 해야 하는 경우 **승인 된 앱** 을 독점적으로 부여 필요를 사용 하세요. 이러한 세 응용 프로그램에서는 두 가지 권한 사이에 또는 절을 사용할 수 없습니다.

**주의**

- 앱 보호 정책에 대 한 앱은 정책 보호를 사용 하 여 Intune 모바일 응용 프로그램 관리 기능을 지원 합니다.
- **앱 보호 정책** 요구 사항은 다음과 같습니다.
    - 디바이스 플랫폼 조건에서는 iOS 및 Android만 지원됩니다.
    - 장치를 등록 하려면 broker 앱이 필요 합니다. IOS에서 broker 앱은 Microsoft Authenticator 되며 Android에서 Intune 회사 포털 앱입니다.

구성 예제는 [방법: 조건부 액세스를 사용 하 여 cloud app access에 앱 보호 정책 및 승인 된 클라이언트 앱 필요](app-protection-based-conditional-access.md) 문서를 참조 하세요.

### <a name="require-password-change"></a>암호 변경 필요 

사용자 위험을 감지 하면 관리자는 사용자 위험 정책 조건을 사용 하 여 사용자가 Azure AD 셀프 서비스 암호 재설정을 사용 하 여 암호를 안전 하 게 변경 하도록 선택할 수 있습니다. 사용자 위험이 감지 되 면 사용자는 셀프 서비스 암호 재설정을 수행 하 여 자동으로 수정할 수 있습니다. 이렇게 하면 사용자 위험 이벤트를 닫아서 관리자의 불필요 한 소음을 방지 합니다. 

사용자에 게 암호를 변경 하 라는 메시지가 표시 되 면 먼저 multi-factor authentication을 완료 해야 합니다. 모든 사용자가 multi-factor authentication에 등록 했는지 확인 하 고 계정에 대 한 위험이 감지 되는 경우에 대비 하 여 준비 합니다.  

> [!WARNING]
> 사용자는 사용자 위험 정책을 트리거하기 전에 셀프 서비스 암호 재설정에 이미 등록되어 있어야 합니다. 

암호 변경 컨트롤을 사용 하 여 정책을 구성할 때 몇 가지 제한 사항이 있습니다.  

1. ' 모든 클라우드 앱 '에 정책을 할당 해야 합니다. 이렇게 하면 다른 앱에 로그인 하 여 공격자가 다른 앱을 사용 하 여 사용자의 암호를 변경 하 고 계정 위험을 다시 설정할 수 없습니다. 
1. 규격 장치 요구와 같은 다른 컨트롤에는 암호 변경 필요를 사용할 수 없습니다.  
1. 암호 변경 컨트롤은 사용자 및 그룹 할당 조건, 클라우드 앱 할당 조건 (모두로 설정 되어야 함) 및 사용자 위험 조건에만 사용할 수 있습니다. 

### <a name="terms-of-use"></a>사용 약관

조직에서 사용 약관을 만든 경우 권한 부여 컨트롤에서 추가 옵션이 표시 될 수 있습니다. 이러한 옵션을 사용 하면 관리자가 정책에 의해 보호 되는 리소스에 액세스 하는 조건으로 사용 약관에 대 한 승인을 요구할 수 있습니다. 사용 약관에 대 한 자세한 내용은 [Azure Active Directory 사용 약관](terms-of-use.md)문서에서 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [조건부 액세스: 세션 컨트롤](concept-conditional-access-session.md)

- [조건부 액세스 일반 정책](concept-conditional-access-policy-common.md)

- [보고서 전용 모드](concept-conditional-access-report-only.md)
