---
title: 조건부 액세스가 있는 앱 보호 정책 - Azure Active Directory
description: Azure Active Directory에서 조건부 액세스를 사용하여 클라우드 앱 액세스에 대한 앱 보호 정책을 요구하는 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 03/04/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol, rosssmi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9859c884f6a1e22a1ac2bd21106ef51ead23fa41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79080061"
---
# <a name="how-to-require-app-protection-policy-and-an-approved-client-app-for-cloud-app-access-with-conditional-access"></a>방법: 조건부 액세스를 사용하여 클라우드 앱 액세스를 위해 앱 보호 정책 및 승인된 클라이언트 앱 필요

사람들은 개인 및 업무 모두에 모바일 장치를 정기적으로 사용합니다. 조직은 직원의 생산성을 높이는 동시에 잠재적으로 안전하지 않은 응용 프로그램에서 데이터 손실을 방지하려고 합니다. 조건부 액세스를 사용하면 조직은 Intune 앱 보호 정책이 적용된 승인된(최신 인증 가능) 클라이언트 앱에 대한 액세스를 제한할 수 있습니다.

이 문서에서는 Office 365, Exchange Online 및 SharePoint Online과 같은 리소스에 대한 조건부 액세스 정책을 구성하는 두 가지 시나리오를 제공합니다.

- [시나리오 1: Office 365 앱에는 앱 보호 정책이 적용된 승인된 앱이 필요합니다.](#scenario-1-office-365-apps-require-approved-apps-with-app-protection-policies)
- [시나리오 2: Exchange 온라인 및 SharePoint Online에서 승인된 클라이언트 앱 및 앱 보호 정책이 필요합니다.](#scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app-and-app-protection-policy)

조건부 액세스에서 이러한 클라이언트 앱은 앱 보호 정책으로 보호되는 것으로 알려져 있습니다. 앱 보호 정책에 대한 자세한 내용은 이 문서에서 찾을 수 [있습니다.](/intune/apps/app-protection-policy)

적격 클라이언트 앱 목록은 [앱 보호 정책 요구 사항을](concept-conditional-access-grant.md)참조하세요.

> [!NOTE]
>    또는 절은 사용자가 **앱 보호 필요 정책** 또는 승인된 클라이언트 앱 권한 부여 **컨트롤을** 지원하는 앱을 활용할 수 있도록 정책 내에서 사용됩니다. 앱 **보호 필수 정책** 권한 부여 제어를 지원하는 앱에 대한 자세한 내용은 앱 [보호 정책 요구 사항을](concept-conditional-access-grant.md)참조하세요.

## <a name="scenario-1-office-365-apps-require-approved-apps-with-app-protection-policies"></a>시나리오 1: Office 365 앱에는 앱 보호 정책이 적용된 승인된 앱이 필요합니다.

이 시나리오에서 ContosoOffice 365 리소스에 대 한 모든 모바일 액세스 는 Outlook 모바일, OneDrive 및 Microsoft 팀 액세스 권한을 받기 전에 앱 보호 정책에 의해 보호 된 승인 된 클라이언트 응용 프로그램을 사용 해야 합니다 결정 했습니다. 모든 사용자는 이미 Azure AD 자격 증명으로 로그인하고 Azure AD 프리미엄 P1 또는 P2 및 Microsoft Intune을 포함하는 라이선스를 할당받았습니다.

조직은 모바일 장치에서 승인된 클라이언트 앱을 사용하도록 요구하기 위해 다음 단계를 완료해야 합니다.

**1단계: Office 365에 대한 Azure AD 조건부 액세스 정책 구성**

1. **Azure 포털에** 글로벌 관리자, 보안 관리자 또는 조건부 액세스 관리자로 로그인합니다.
1. Azure **Active 디렉터리** > **보안** > **조건부 액세스로**이동합니다.
1. **새 정책**을 선택합니다.
1. 정책 이름을 지정합니다. 조직에서 정책 이름에 대해 의미 있는 표준을 만드는 것이 좋습니다.
1. 할당에서 사용자 **및 그룹을** **선택합니다.**
   1. **포함**에서 **이** 정책을 적용하려는 모든 사용자 또는 특정 **사용자 및 그룹을** 선택합니다. 
   1. **완료를 선택합니다.**
1. **클라우드 앱 또는 작업** > **에서 포함**, Office **365(미리 보기)를**선택합니다.
1. **조건에서**장치 **플랫폼을**선택합니다.
   1. **구성을** **예로 설정합니다.**
   1. **안드로이드와** **아이폰 OS를**포함 .
1. **조건에서**클라이언트 **앱(미리 보기)을**선택합니다.
   1. **구성을** **예로 설정합니다.**
   1. **모바일 앱 및 데스크톱 클라이언트** 및 **최신 인증 클라이언트**를 선택합니다.
1. **액세스 제어** > **권한 부여에서**다음 옵션을 선택합니다.
   - **승인된 클라이언트 앱 필요**
   - **앱 보호 정책 필요(미리 보기)**
   - **선택한 컨트롤 중 하나가 필요합니다.**
1. 설정을 확인하고 **정책 활성화를** **설정 합니다.**
1. 정책을 만들고 활성화하려면 **만들기를** 선택합니다.

**2단계: EAS(ActiveSync)를 사용 하 여 온라인 교환에 대 한 Azure AD 조건부 액세스 정책 구성**

이 단계의 조건부 액세스 정책의 경우 다음 구성 요소를 구성합니다.

1. Azure **Active 디렉터리** > **보안** > **조건부 액세스로**이동합니다.
1. **새 정책**을 선택합니다.
1. 정책 이름을 지정합니다. 조직에서 정책 이름에 대해 의미 있는 표준을 만드는 것이 좋습니다.
1. 할당에서 사용자 **및 그룹을** **선택합니다.**
   1. **포함**에서 **이** 정책을 적용하려는 모든 사용자 또는 특정 **사용자 및 그룹을** 선택합니다. 
   1. **완료를 선택합니다.**
1. **클라우드 앱 또는 작업** > **포함**에서 **Office 365 Exchange Online을**선택합니다.
1. **조건**하에서 :
   1. **클라이언트 앱(미리 보기)**:
      1. **구성을** **예로 설정합니다.**
      1. **모바일 앱 및 데스크톱 클라이언트 및** Exchange **ActiveSync 클라이언트를**선택합니다.
1. **액세스 제어** > **권한 부여,** 권한 부여 **액세스,** **앱 보호 정책 필요**를 선택하고 **을 선택합니다.**
1. 설정을 확인하고 **정책 활성화를** **설정 합니다.**
1. 정책을 만들고 활성화하려면 **만들기를** 선택합니다.

**3 단계: iOS 및 Android 클라이언트 응용 프로그램에 대 한 Intune 응용 프로그램 보호 정책 구성**

Android 및 iOS용 앱 보호 정책을 만드는 단계는 [앱 보호 정책을 만들고 할당하는 방법](/intune/apps/app-protection-policies)문서를 검토합니다. 

## <a name="scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app-and-app-protection-policy"></a>시나리오 2: Exchange 온라인 및 SharePoint Online에서 승인된 클라이언트 앱 및 앱 보호 정책이 필요합니다.

이 시나리오에서 Contoso는 사용자가 액세스 권한을 받기 전에 앱 보호 정책에 의해 보호되는 Outlook mobile과 같은 승인된 클라이언트 앱을 사용하는 한 모바일 장치에서전자 메일 및 SharePoint 데이터에만 액세스할 수 있다고 결정했습니다. 모든 사용자는 이미 Azure AD 자격 증명으로 로그인하고 Azure AD 프리미엄 P1 또는 P2 및 Microsoft Intune을 포함하는 라이선스를 할당받았습니다.

조직은 모바일 장치 및 Exchange ActiveSync 클라이언트에서 승인된 클라이언트 앱을 사용하도록 하려면 다음 세 단계를 완료해야 합니다.

**1단계: Exchange Online 및 SharePoint Online에 액세스할 때 승인된 클라이언트 앱 및 앱 보호 정책을 사용해야 하는 Android 및 iOS 기반 최신 인증 클라이언트에 대한 정책입니다.**

1. **Azure 포털에** 글로벌 관리자, 보안 관리자 또는 조건부 액세스 관리자로 로그인합니다.
1. Azure **Active 디렉터리** > **보안** > **조건부 액세스로**이동합니다.
1. **새 정책**을 선택합니다.
1. 정책 이름을 지정합니다. 조직에서 정책 이름에 대해 의미 있는 표준을 만드는 것이 좋습니다.
1. 할당에서 사용자 **및 그룹을** **선택합니다.**
   1. **포함**에서 **이** 정책을 적용하려는 모든 사용자 또는 특정 **사용자 및 그룹을** 선택합니다. 
   1. **완료를 선택합니다.**
1. **클라우드 앱 또는 작업** > **포함**, Office **365 Exchange 온라인** 및 Office **365 SharePoint Online을**선택합니다.
1. **조건에서**장치 **플랫폼을**선택합니다.
   1. **구성을** **예로 설정합니다.**
   1. **안드로이드와** **아이폰 OS를**포함 .
1. **조건에서**클라이언트 **앱(미리 보기)을**선택합니다.
   1. **구성을** **예로 설정합니다.**
   1. **모바일 앱 및 데스크톱 클라이언트** 및 **최신 인증 클라이언트**를 선택합니다.
1. **액세스 제어** > **권한 부여에서**다음 옵션을 선택합니다.
   - **승인된 클라이언트 앱 필요**
   - **앱 보호 정책 필요(미리 보기)**
   - **선택한 컨트롤 중 하나가 필요합니다.**
1. 설정을 확인하고 **정책 활성화를** **설정 합니다.**
1. 정책을 만들고 활성화하려면 **만들기를** 선택합니다.

**2단계: 승인된 클라이언트 앱을 사용해야 하는 Exchange ActiveSync 클라이언트에 대한 정책입니다.**

1. Azure **Active 디렉터리** > **보안** > **조건부 액세스로**이동합니다.
1. **새 정책**을 선택합니다.
1. 정책 이름을 지정합니다. 조직에서 정책 이름에 대해 의미 있는 표준을 만드는 것이 좋습니다.
1. 할당에서 사용자 **및 그룹을** **선택합니다.**
   1. **포함**에서 **이** 정책을 적용하려는 모든 사용자 또는 특정 **사용자 및 그룹을** 선택합니다. 
   1. **완료를 선택합니다.**
1. **클라우드 앱 또는 작업** > **포함**에서 **Office 365 Exchange Online을**선택합니다.
1. **조건**하에서 :
   1. **클라이언트 앱(미리 보기)**:
      1. **구성을** **예로 설정합니다.**
      1. **모바일 앱 및 데스크톱 클라이언트 및** Exchange **ActiveSync 클라이언트를**선택합니다.
1. **액세스 제어** > **권한 부여,** 권한 부여 **액세스,** **앱 보호 정책 필요**를 선택하고 **을 선택합니다.**
1. 설정을 확인하고 **정책 활성화를** **설정 합니다.**
1. 정책을 만들고 활성화하려면 **만들기를** 선택합니다.

**3단계: iOS 및 Android 클라이언트 응용 프로그램에 대한 Intune 앱 보호 정책을 구성합니다.**

Android 및 iOS용 앱 보호 정책을 만드는 단계는 [앱 보호 정책을 만들고 할당하는 방법](/intune/apps/app-protection-policies)문서를 검토합니다. 

## <a name="next-steps"></a>다음 단계

[조건부 액세스란 무엇입니까?](overview.md)

[조건부 액세스 구성 요소](concept-conditional-access-policies.md)

[일반 조건부 액세스 정책](concept-conditional-access-policy-common.md)

