---
title: 조건부 액세스 정책에서 컨트롤 부여 - Azure Active Directory
description: Azure AD 조건부 액세스 정책의 권한 부여 컨트롤이란 무엇입니까?
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 02ec8dace971cd4dc1407c9e8d20839504c9ecc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331850"
---
# <a name="conditional-access-grant"></a>조건부 액세스: 보조금

조건부 액세스 정책 내에서 관리자는 액세스 제어를 사용하여 리소스에 대한 액세스 권한을 부여하거나 차단할 수 있습니다.

![다단계 인증이 필요한 권한 부여 컨트롤이 있는 조건부 액세스 정책](./media/concept-conditional-access-grant/conditional-access-grant.png)

## <a name="block-access"></a>액세스 차단

블록은 모든 할당을 고려하고 조건부 액세스 정책 구성에 따라 액세스를 차단합니다.

블록은 적절한 지식으로 휘두르면 강력한 컨트롤입니다. 이를 활성화하기 전에 관리자가 [보고서 전용 모드를](concept-conditional-access-report-only.md) 사용하여 테스트해야 합니다.

## <a name="grant-access"></a>액세스 권한 부여

관리자는 액세스 권한을 부여할 때 하나 이상의 컨트롤을 적용하도록 선택할 수 있습니다. 이러한 컨트롤에는 다음 옵션이 포함됩니다. 

- [다단계 인증 필요(Azure 다단계 인증)](../authentication/concept-mfa-howitworks.md)
- [장치를 규격으로 표시해야 합니다(Microsoft Intune)](/intune/protect/device-compliance-get-started)
- [하이브리드 Azure AD 조인 장치 필요](../devices/concept-azure-ad-join-hybrid.md)
- [승인된 클라이언트 앱 필요](app-based-conditional-access.md)
- [앱 보호 정책 필요](app-protection-based-conditional-access.md)

관리자가 이러한 옵션을 결합하도록 선택하면 다음 방법을 선택할 수 있습니다.

- 선택한 모든 컨트롤 필요(제어 **및** 제어)
- 선택한 컨트롤 중 **하나(제어 OR** 컨트롤) 요구

기본적으로 조건부 액세스에는 선택한 모든 컨트롤이 필요합니다.

### <a name="require-multi-factor-authentication"></a>Multi-Factor Authentication 필요

이 확인란을 선택하면 사용자가 Azure 다단계 인증을 수행해야 합니다. Azure 다단계 인증 배포에 대한 자세한 내용은 [클라우드 기반 Azure 다단계 인증 배포 계획](../authentication/howto-mfa-getstarted.md)문서에서 확인할 수 있습니다.

### <a name="require-device-to-be-marked-as-compliant"></a>디바이스를 준수 상태로 표시해야 함

Microsoft Intune을 배포한 조직은 장치에서 반환된 정보를 사용하여 특정 규정 준수 요구 사항을 충족하는 장치를 식별할 수 있습니다. 이 정책 준수 정보는 Intune에서 Azure AD로 전달되며, 여기서 조건부 Access는 리소스에 대한 액세스 권한을 부여하거나 차단하기로 결정을 내릴 수 있습니다. 규정 준수 정책에 대한 자세한 내용은 Intune 을 [사용하여 조직의 리소스에 대한 액세스를 허용하는 장치의 규칙 설정](/intune/protect/device-compliance-get-started)문서를 참조하십시오.

장치는 Intune(모든 장치 OS에 대해) 또는 Windows 10 장치의 타사 MDM 시스템에서 준수하는 것으로 표시할 수 있습니다. Jamf pro는 유일한 타사 MDM 시스템입니다. 통합에 대한 자세한 내용은 문서에서 찾을 수 있습니다, [준수를 위한 Intune와 Jamf Pro통합.](/intune/protect/conditional-access-integrate-jamf)

장치를 Azure AD에 등록해야 규정을 준수하는 것으로 표시할 수 있습니다. 장치 등록에 대한 자세한 내용은 문서에서 찾을 수 [있습니다.](../devices/overview.md)

### <a name="require-hybrid-azure-ad-joined-device"></a>하이브리드 Azure AD 조인 장치 필요

조직은 조건부 액세스 정책의 일부로 장치 ID를 사용하도록 선택할 수 있습니다. 조직에서는 장치가 이 확인란을 사용하여 하이브리드 Azure AD 조인되도록 요구할 수 있습니다. 장치 ID에 대한 자세한 내용은 [장치 ID란](../devices/overview.md)무엇입니까?

### <a name="require-approved-client-app"></a>승인된 클라이언트 앱 필요

조직에서는 선택한 클라우드 앱에 대한 액세스 시도를 승인된 클라이언트 앱에서 만들어야 할 수 있습니다. 이러한 승인된 클라이언트 앱은 모든 MDM(모바일 장치 관리) 솔루션과 관계없이 [Intune 앱 보호 정책을](/intune/app-protection-policy) 지원합니다.

이 권한 부여 컨트롤을 활용하려면 조건부 Access에서 브로커 앱을 사용해야 하는 Azure Active Directory에 장치를 등록해야 합니다. 브로커 앱은 iOS의 경우 Microsoft Authenticator, Android 디바이스의 경우 Microsoft 회사 포털일 수 있습니다. 사용자가 인증을 시도할 때 브로커 앱이 장치에 설치되지 않은 경우 사용자는 브로커 앱을 설치하기 위해 앱 스토어로 리디렉션됩니다.

이 설정은 다음 iOS 및 Android 앱에 적용됩니다.

- Microsoft Azure Information Protection
- Microsoft Bookings
- Microsoft Cortana
- Microsoft Dynamics 365
- Microsoft Edge
- Microsoft Excel
- Microsoft Flow
- Microsoft Intune Managed Browser
- Microsoft Invoicing
- Microsoft Kaizala
- Microsoft Launcher
- Microsoft Office
- 마이크로소프트 오피스 허브
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
- Microsoft 팀
- Microsoft To-Do
- Microsoft Visio
- Microsoft Word
- Microsoft Yammer
- Microsoft Whiteboard

**발언**

- 승인된 클라이언트 앱은 Intune 모바일 애플리케이션 관리 기능을 지원합니다.
- **승인된 클라이언트 앱 필요** 요구 사항:
   - 디바이스 플랫폼 조건에서는 iOS 및 Android만 지원됩니다.
   - 브로커 앱은 장치를 등록해야 합니다. iOS에서 브로커 앱은 마이크로 소프트 인증자이며 안드로이드에서는 Intune 회사 포털 앱입니다.
- 조건부 액세스는 InPrivate 모드에서 승인된 클라이언트 앱으로 Microsoft Edge를 고려할 수 없습니다.

구성 예제에 [대한 조건부 액세스를 사용하여 클라우드 앱 액세스에 대해 승인된 클라이언트 앱이 필요한](app-based-conditional-access.md) 방법 문서를 참조하십시오.

### <a name="require-app-protection-policy"></a>앱 보호 정책 필요

조건부 액세스 정책에서 선택한 클라우드 앱에 액세스하기 전에 클라이언트 앱에 [Intune 앱 보호 정책을](/intune/app-protection-policy) 제공하도록 요구할 수 있습니다. 

이 권한 부여 컨트롤을 활용하려면 조건부 Access에서 브로커 앱을 사용해야 하는 Azure Active Directory에 장치를 등록해야 합니다. 브로커 앱은 iOS의 경우 Microsoft Authenticator, Android 디바이스의 경우 Microsoft 회사 포털일 수 있습니다. 사용자가 인증을 시도할 때 브로커 앱이 장치에 설치되지 않은 경우 사용자는 브로커 앱을 설치하기 위해 앱 스토어로 리디렉션됩니다.

이 설정은 다음 클라이언트 앱에 적용됩니다.

- Microsoft Cortana
- Microsoft OneDrive
- Microsoft Outlook
- Microsoft Planner

**발언**

- 앱 보호 정책용 앱은 정책 보호를 통해 Intune 모바일 응용 프로그램 관리 기능을 지원합니다.
- **앱 보호 정책** 요구 사항:
    - 디바이스 플랫폼 조건에서는 iOS 및 Android만 지원됩니다.
    - 브로커 앱은 장치를 등록해야 합니다. iOS에서 브로커 앱은 마이크로 소프트 인증자이며 안드로이드에서는 Intune 회사 포털 앱입니다.

구성 예제에 [대한 조건부 액세스를 사용하여 클라우드 앱 액세스에 대한 앱 보호 정책 및 승인된 클라이언트 앱이 필요한](app-protection-based-conditional-access.md) 방법 문서를 참조하십시오.

### <a name="terms-of-use"></a>사용 약관

조직에서 사용 약관을 만든 경우 추가 옵션이 권한 부여 제어 아래에 표시될 수 있습니다. 이러한 옵션을 통해 관리자는 정책으로 보호되는 리소스에 액세스하는 조건으로 사용 약관을 승인해야 합니다. 사용 약관에 대한 자세한 내용은 Azure [Active Directory 사용 약관의](terms-of-use.md)문서에서 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [조건부 액세스: 세션 컨트롤](concept-conditional-access-session.md)

- [조건부 액세스 공통 정책](concept-conditional-access-policy-common.md)

- [보고서 전용 모드](concept-conditional-access-report-only.md)
