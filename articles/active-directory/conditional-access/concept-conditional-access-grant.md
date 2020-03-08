---
title: 조건부 액세스 정책에서 컨트롤 부여-Azure Active Directory
description: Azure AD 조건부 액세스 정책의 grant 컨트롤은 무엇 인가요?
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 09a720be10b09e11adc8acdc8ab26a06870311b0
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78892711"
---
# <a name="conditional-access-grant"></a>조건부 액세스: Grant

조건부 액세스 정책 내에서 관리자는 액세스 제어를 사용 하 여 리소스에 대 한 액세스를 허용 하거나 차단할 수 있습니다.

![Multi-factor authentication을 요구 하는 권한 부여 컨트롤을 사용 하는 조건부 액세스 정책](./media/concept-conditional-access-grant/conditional-access-grant.png)

## <a name="block-access"></a>액세스 차단

블록은 모든 할당을 고려 하 여 조건부 액세스 정책 구성에 따라 액세스를 방지 합니다.

Block은 적절 한 정보를 사용 하 여 wielded 해야 하는 강력한 컨트롤입니다. 관리자는 [보고서 전용 모드](concept-conditional-access-report-only.md) 를 사용 하 여를 활성화 하기 전에 테스트 해야 합니다.

## <a name="grant-access"></a>액세스 권한 부여

관리자는 액세스 권한을 부여할 때 하나 이상의 컨트롤을 적용 하도록 선택할 수 있습니다. 이러한 컨트롤에는 다음 옵션이 포함 됩니다. 

- [Multi-factor authentication 필요 (Azure Multi-Factor Authentication)](../authentication/concept-mfa-howitworks.md)
- [장치를 규격으로 표시 해야 함 (Microsoft Intune)](/intune/protect/device-compliance-get-started)
- [하이브리드 Azure AD 조인 장치 필요](../devices/concept-azure-ad-join-hybrid.md)
- [승인 된 클라이언트 앱 필요](app-based-conditional-access.md)
- [앱 보호 정책 필요](app-protection-based-conditional-access.md)

관리자가 이러한 옵션을 결합 하도록 선택 하면 다음 방법을 선택할 수 있습니다.

- 모든 선택 된 컨트롤 필요 (컨트롤 **및** 컨트롤)
- 선택한 컨트롤 (컨트롤 **또는** 컨트롤) 중 하나가 필요 합니다.

기본적으로 조건부 액세스에는 선택한 모든 컨트롤이 필요 합니다.

### <a name="require-multi-factor-authentication"></a>Multi-Factor Authentication 필요

이 확인란을 선택 하면 사용자가 Azure Multi-Factor Authentication를 수행 해야 합니다. Azure Multi-Factor Authentication 배포에 대 한 자세한 내용은 [클라우드 기반 azure Multi-Factor Authentication 배포 계획](../authentication/howto-mfa-getstarted.md)문서에서 찾을 수 있습니다.

### <a name="require-device-to-be-marked-as-compliant"></a>디바이스를 준수 상태로 표시해야 함

Microsoft Intune를 배포한 조직은 장치에서 반환 된 정보를 사용 하 여 특정 규정 준수 요구 사항을 충족 하는 장치를 식별할 수 있습니다. 이 정책 준수 정보는 Intune에서 Azure AD로 전달 되며, 조건부 액세스는 리소스에 대 한 액세스를 부여 하거나 차단 하는 결정을 내릴 수 있습니다. 규정 준수 정책에 대 한 자세한 내용은 Intune을 [사용 하 여 조직의 리소스에 대 한 액세스를 허용 하는 장치에 대 한 규칙 설정](/intune/protect/device-compliance-get-started)문서를 참조 하세요.

장치는 Intune (장치 OS의 경우) 또는 타사 MDM system for Windows 10 장치에서 준수로 표시 될 수 있습니다. Jamf pro는 유일 하 게 지원 되는 타사 MDM 시스템입니다. 통합에 대 한 자세한 내용은 [Jamf Pro를 Intune과 통합](/intune/protect/conditional-access-integrate-jamf)하 여 준수 문서를 참조 하세요.

장치가 준수 상태로 표시 되기 전에 Azure AD에 등록 되어 있어야 합니다. 장치 등록에 대 한 자세한 내용은 [장치 Id 정의](../devices/overview.md)문서에서 찾을 수 있습니다.

### <a name="require-hybrid-azure-ad-joined-device"></a>하이브리드 Azure AD 조인 장치 필요

조직에서는 조건부 액세스 정책의 일부로 장치 id를 사용 하도록 선택할 수 있습니다. 조직에서는이 확인란을 사용 하 여 장치가 하이브리드 Azure AD에 가입 되도록 요구할 수 있습니다. 장치 id에 대 한 자세한 내용은 [장치 id 란?](../devices/overview.md)문서를 참조 하세요.

### <a name="require-approved-client-app"></a>승인된 클라이언트 앱 필요

조직에서 선택한 클라우드 앱에 대 한 액세스를 승인 된 클라이언트 앱에서 수행 해야 하도록 요구할 수 있습니다. 이러한 승인 된 클라이언트 앱은 모든 MDM (모바일 장치 관리) 솔루션과 독립적인 [Intune 앱 보호 정책을](/intune/app-protection-policy) 지원 합니다.

이 설정은 다음 클라이언트 앱에 적용됩니다.

- Microsoft Azure Information Protection
- Microsoft 예약
- Microsoft Cortana
- Microsoft Dynamics 365
- Microsoft Edge
- Microsoft Excel
- Microsoft Flow
- Microsoft Intune Managed Browser
- Microsoft Invoicing
- Microsoft Kaizala
- Microsoft Launcher
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

**주의**

- 승인된 클라이언트 앱은 Intune 모바일 애플리케이션 관리 기능을 지원합니다.
- **승인된 클라이언트 앱 필요** 요구 사항:
   - 는 iOS 및 Android for device 플랫폼 조건만 지원 합니다.
- 조건부 액세스는 InPrivate 모드에서 승인 된 클라이언트 앱으로 Microsoft Edge를 고려할 수 없습니다.

구성 예제는 [방법: 조건부 액세스를 사용 하는 클라우드 앱 액세스를 위해 승인 된 클라이언트 앱 필요](app-based-conditional-access.md) 문서를 참조 하세요.

### <a name="require-app-protection-policy"></a>앱 보호 정책 필요

조건부 액세스 정책에서 선택한 클라우드 앱에 액세스할 수 있으려면 먼저 클라이언트 앱에 [Intune 앱 보호 정책이](/intune/app-protection-policy) 있어야 합니다. 

이 설정은 다음 클라이언트 앱에 적용됩니다.

- Microsoft Cortana
- Microsoft OneDrive
- Microsoft Outlook
- Microsoft Planner

**주의**

- 앱 보호 정책에 대 한 앱은 정책 보호를 사용 하 여 Intune 모바일 응용 프로그램 관리 기능을 지원 합니다.
- **앱 보호 정책** 요구 사항은 다음과 같습니다.
    - 는 iOS 및 Android for device 플랫폼 조건만 지원 합니다.

구성 예제는 [방법: 조건부 액세스를 사용 하 여 cloud app access에 앱 보호 정책 및 승인 된 클라이언트 앱 필요](app-protection-based-conditional-access.md) 문서를 참조 하세요.

### <a name="terms-of-use"></a>사용 약관

조직에서 사용 약관을 만든 경우 권한 부여 컨트롤에서 추가 옵션이 표시 될 수 있습니다. 이러한 옵션을 사용 하면 관리자가 정책에 의해 보호 되는 리소스에 액세스 하는 조건으로 사용 약관에 대 한 승인을 요구할 수 있습니다. 사용 약관에 대 한 자세한 내용은 [Azure Active Directory 사용 약관](terms-of-use.md)문서에서 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [조건부 액세스: 세션 컨트롤](concept-conditional-access-session.md)

- [조건부 액세스 공통 정책](concept-conditional-access-policy-common.md)

- [보고서 전용 모드](concept-conditional-access-report-only.md)
