---
title: Azure Active Directory 조건부 액세스 설정 참조 | Microsoft Docs
description: Azure Active Directory 조건부 액세스 정책에서 지원 되는 설정에 대 한 개요를 확인 하세요.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 07/10/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d56b1f800c71a5bbef7ffb1155d05e096113e2c
ms.sourcegitcommit: a7a9d7f366adab2cfca13c8d9cbcf5b40d57e63a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/20/2019
ms.locfileid: "71162427"
---
# <a name="azure-active-directory-conditional-access-settings-reference"></a>Azure Active Directory 조건부 액세스 설정 참조

[AZURE AD (Azure Active Directory) 조건부 액세스](../active-directory-conditional-access-azure-portal.md) 를 사용 하 여 권한 있는 사용자가 리소스에 액세스 하는 방법을 제어할 수 있습니다.

이 문서에서는 조건부 액세스 정책의 다음 구성 옵션에 대 한 지원 정보를 제공 합니다.

- 클라우드 애플리케이션 할당
- 디바이스 플랫폼 조건
- 클라이언트 애플리케이션 조건
- 승인된 클라이언트 애플리케이션 요구 사항

찾고 있는 내용이 아니면 이 문서 하단에 의견을 남겨 주세요.

## <a name="cloud-apps-assignments"></a>클라우드 앱 할당

조건부 액세스 정책을 사용 하 여 사용자가 [클라우드 앱](conditions.md#cloud-apps-and-actions)에 액세스 하는 방법을 제어할 수 있습니다. 조건부 액세스 정책을 구성 하는 경우 하나 이상의 클라우드 앱을 선택 해야 합니다. 

![정책에 대한 클라우드 앱 선택](./media/technical-reference/09.png)

### <a name="microsoft-cloud-applications"></a>Microsoft 클라우드 애플리케이션

Microsoft에서 다음 클라우드 앱에 조건부 액세스 정책을 할당할 수 있습니다.

- Azure Analysis Services
- Azure DevOps
- Azure SQL Database 및 데이터 웨어하우스- [자세한 정보](https://docs.microsoft.com/azure/sql-database/sql-database-conditional-access)
- Dynamics CRM Online
- Microsoft Application Insights 분석
- Microsoft Azure Information Protection-자세히 [알아보기](https://docs.microsoft.com/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- Microsoft Azure 관리- [자세한 정보](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)
- Microsoft Azure 구독 관리
- Microsoft Cloud App Security
- Microsoft Commerce Tools Access Control 포털
- Microsoft Commerce Tools 인증 서비스
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- Microsoft Intune 등록
- Microsoft Planner
- Microsoft PowerApps
- Bing에서 Microsoft Search
- Microsoft StaffHub
- Microsoft Stream
- Microsoft 팀
- Office 365 Exchange Online
- Office 365 SharePoint Online
- Office 365 Yammer
- Office Delve
- Office Sway
- Outlook Groups
- Power BI 서비스
- Project Online
- 비즈니스 온라인용 Skype
- VPN(가상 사설망)
- Visual Studio App Center
- Windows Defender ATP

### <a name="other-applications"></a>다른 애플리케이션

Microsoft 클라우드 앱 외에도 다음과 같은 유형의 클라우드 앱에 조건부 액세스 정책을 할당할 수 있습니다.

- Azure AD에 연결된 애플리케이션
- 사전 통합되고 페더레이션된 SaaS(software as a service) 애플리케이션
- 암호 SSO(Single Sign-On)를 사용하는 애플리케이션
- 기간 업무 애플리케이션
- Azure AD 애플리케이션 프록시를 사용하는 애플리케이션

## <a name="device-platform-condition"></a>디바이스 플랫폼 조건

조건부 액세스 정책에서 장치 플랫폼 조건을 구성 하 여 클라이언트의 운영 체제에 정책을 연결할 수 있습니다. Azure AD 조건부 액세스는 다음과 같은 장치 플랫폼을 지원 합니다.

- Android
- iOS
- Windows Phone
- Windows
- macOS

![클라이언트 OS에 액세스 정책 연결](./media/technical-reference/41.png)

**다른 클라이언트** 상태를 사용 하 여 레거시 인증을 차단 하는 경우 장치 플랫폼 조건을 설정할 수도 있습니다.

## <a name="client-apps-condition"></a>클라이언트 앱 조건

조건부 액세스 정책에서 [클라이언트 앱](conditions.md#client-apps) 조건을 구성 하 여 액세스 시도를 시작한 클라이언트 앱에 정책을 연결할 수 있습니다. 다음과 같은 종류의 클라이언트 앱에서 액세스를 시도할 때 액세스를 차단하거나 부여하는 클라이언트 앱 조건을 설정합니다.

- 브라우저
- 모바일 앱 및 데스크톱 앱

![클라이언트 앱에 대한 액세스 제어](./media/technical-reference/03.png)

### <a name="supported-browsers"></a>지원되는 브라우저

조건부 액세스 정책에서 **브라우저** 를 클라이언트 앱으로 선택할 수 있습니다.

![지원되는 브라우저의 액세스 제어](./media/technical-reference/05.png)

이 설정은 모든 브라우저에서 작동합니다. 하지만 호환 디바이스 요구 사항과 같은 디바이스 정책을 충족하기 위해 다음 운영 체제와 브라우저가 지원됩니다.

| OS                     | 브라우저                                        |
| :--                    | :--                                             |
| Windows 10             | Microsoft Edge, Internet Explorer, Chrome       |
| Windows 8 / 8.1        | Internet Explorer, Chrome                       |
| Windows 7              | Internet Explorer, Chrome                       |
| iOS                    | Microsoft Edge, Intune Managed Browser, Safari  |
| Android                | Microsoft Edge, Intune Managed Browser, Chrome  |
| Windows Phone          | Microsoft Edge, Internet Explorer               |
| Windows Server 2019    | Microsoft Edge, Internet Explorer, Chrome       |
| Windows Server 2016    | Internet Explorer |
| Windows Server 2012 R2 | Internet Explorer                       |
| Windows Server 2008 R2 | Internet Explorer                       |
| macOS                  | Chrome, Safari                                  |


#### <a name="why-do-i-see-a-certificate-prompt-in-the-browser"></a>브라우저에 인증서 프롬프트가 표시 되는 이유는 무엇 인가요?

Windows 7, iOS, Android 및 macOS Azure AD는 장치가 Azure AD에 등록 될 때 프로 비전 되는 클라이언트 인증서를 사용 하 여 장치를 식별 합니다.  사용자가 브라우저를 통해 처음으로 로그인 하면 사용자에 게 인증서를 선택 하 라는 메시지가 표시 됩니다. 사용자는 브라우저를 사용 하기 전에이 인증서를 선택 해야 합니다.

#### <a name="chrome-support"></a>Chrome 지원

**Windows 10 크리에이터 업데이트 (버전 1703)** 이상에서 Chrome을 지원 하려면 [windows 10 계정 확장](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji)을 설치 합니다. 이 확장은 조건부 액세스 정책에 장치 관련 세부 정보가 필요한 경우에 필요 합니다.

이 확장을 Chrome 브라우저에 자동으로 배포하려면 다음 레지스트리 키를 만듭니다.

|    |    |
| --- | --- |
| 경로 | HKEY_LOCAL_MACHINE\Software\Policies\Google\Chrome\ExtensionInstallForcelist |
| Name | 1 |
| 형식 | REG_SZ (String) |
| data | ppnbnpeolgkicgegkbkbjmhlideopiji; https\://clients2.google.com/service/update2/crx |

**Windows 8.1 및 7**에서 Chrome을 지원하려면 다음 레지스트리 키를 만듭니다.

|    |    |
| --- | --- |
| 경로 | HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls |
| Name | 1 |
| 형식 | REG_SZ (String) |
| data | {"pattern":"https://device.login.microsoftonline.com","filter":{"ISSUER":{"CN":"MS-Organization-Access"}}} |

이러한 브라우저는 디바이스 인증을 지원하므로 정책에 대해 디바이스 유효성을 검사하고 식별하는 것이 가능합니다. 브라우저가 개인 모드로 실행 중이면 디바이스 검사가 실패합니다.

### <a name="supported-mobile-applications-and-desktop-clients"></a>지원되는 모바일 애플리케이션 및 데스크톱 클라이언트

조건부 액세스 정책에서 **모바일 앱 및 데스크톱 클라이언트** 를 클라이언트 앱으로 선택할 수 있습니다.

![지원되는 모바일 앱 또는 데스크톱 클라이언트에 대한 액세스 제어](./media/technical-reference/06.png)

이 설정은 다음 모바일 앱 및 데스크톱 클라이언트에서 수행하는 액세스 시도에 영향을 미칩니다.

| 클라이언트 앱 | 대상 서비스 | 플랫폼 |
| --- | --- | --- |
| Dynamics CRM 앱 | Dynamics CRM | Windows 10, Windows 8.1, iOS 및 Android |
| 메일/달력/사람 앱, Outlook 2016, Outlook 2013(최신 인증 포함)| Office 365 Exchange Online | Windows 10 |
| 앱에 대한 MFA 및 위치 정책입니다. 디바이스 기반 정책은 지원되지 않습니다.| 모든 My Apps 앱 서비스| Android 및 iOS |
| Microsoft Teams Services - Microsoft Teams 및 모든 클라이언트 앱(Windows 데스크톱, iOS, Android, WP 및 웹 클라이언트)을 지원하는 서비스를 모두 제어합니다. | Microsoft 팀 | Windows 10, Windows 8.1, Windows 7, iOS, Android 및 macOS |
| Office 2016 앱, Office 2013(최신 인증 사용), OneDrive 동기화 클라이언트([참고](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e) 참조) | Office 365 SharePoint Online | Windows 8.1, Windows 7 |
| Office 2016 앱, Universal Office 앱, Office 2013(최신 인증 사용), OneDrive 동기화 클라이언트([참고](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e) 참조), Office 그룹 지원은 나중에 지원될 예정입니다. SharePoint 앱 지원은 나중에 지원될 예정입니다. | Office 365 SharePoint Online | Windows 10 |
| Office 2016(Word, Excel, PowerPoint, OneNote만 해당) 향후 제공될 예정인 비즈니스용 OneDrive 지원| Office 365 SharePoint Online| macOS|
| Office 2019| Office 365 SharePoint Online | Windows 10, macOS |
| Office 모바일 앱 | Office 365 SharePoint Online | Android, iOS |
| Office Yammer 앱 | Office 365 Yammer | Windows 10, iOS, Android |
| Outlook 2019 | Office 365 SharePoint Online | Windows 10, macOS |
| Outlook 2016(macOS용 Office) | Office 365 Exchange Online | macOS |
| Outlook 2016, Outlook 2013(최신 인증 사용), 비즈니스용 Skype(최신 인증 사용) | Office 365 Exchange Online | Windows 8.1, Windows 7 |
| Outlook 모바일 앱 | Office 365 Exchange Online | Android, iOS |
| Power BI 앱 | Power BI 서비스 | Windows 10, Windows 8.1, Windows 7, Android 및 iOS |
| 비즈니스용 Skype | Office 365 Exchange Online| Android, iOS |
| Visual Studio Team Services 앱 | Visual Studio Team Services | Windows 10, Windows 8.1, Windows 7, iOS 및 Android |

## <a name="support-for-legacy-authentication"></a>레거시 인증 지원

**다른 클라이언트**를 선택하여 IMAP, MAPI, POP, SMTP 및 최신 인증을 사용하지 않는 이전 Office 앱과 같은 메일 프로토콜과 함께 기본 인증을 사용하는 앱에 영향을 미치는 조건을 지정할 수 있습니다.  

![다른 클라이언트](./media/technical-reference/11.png)

자세한 내용은 [클라이언트 앱](conditions.md#client-apps)을 참조하세요.

## <a name="approved-client-app-requirement"></a>승인된 클라이언트 앱 요구 사항

조건부 액세스 정책에서 선택한 클라우드 앱에 대 한 액세스를 승인 된 클라이언트 앱에서 수행 해야 하도록 요구할 수 있습니다. 

![승인된 클라이언트 앱에 대한 액세스 제어](./media/technical-reference/21.png)

이 설정은 다음 클라이언트 앱에 적용됩니다.

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
   - [디바이스 플랫폼 조건](#device-platform-condition)에서는 iOS 및 Android만 지원됩니다.

## <a name="app-protection-policy-requirement"></a>앱 보호 정책 요구 사항 

조건부 액세스 정책에서 선택한 클라우드 앱에 대 한 액세스를 사용할 수 있으려면 먼저 클라이언트 앱에 앱 보호 정책이 있어야 합니다. 

![앱 보호 정책을 사용 하 여 액세스 제어](./media/technical-reference/22.png)

이 설정은 다음 클라이언트 앱에 적용됩니다.

- Microsoft Cortana
- Microsoft OneDrive
- Microsoft Outlook
- Microsoft Planner

**주의**

- 앱 보호 정책에 대 한 앱은 정책 보호를 사용 하 여 Intune 모바일 응용 프로그램 관리 기능을 지원 합니다.
- **앱 보호 정책** 요구 사항은 다음과 같습니다.
    - [디바이스 플랫폼 조건](#device-platform-condition)에서는 iOS 및 Android만 지원됩니다.

## <a name="next-steps"></a>다음 단계

- 조건부 액세스에 대 한 개요는 [Azure Active Directory의 조건부 액세스 란?](../active-directory-conditional-access-azure-portal.md) 을 참조 하세요.
- 사용자 환경에서 조건부 액세스 정책을 구성할 준비가 된 경우 [Azure Active Directory의 조건부 액세스에 대 한 권장 방법](best-practices.md)을 참조 하세요.

<!--Image references-->
[1]: ./media/technical-reference/01.png
