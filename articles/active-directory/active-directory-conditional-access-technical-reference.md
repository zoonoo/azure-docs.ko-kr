---
title: "Azure Active Directory 조건부 액세스 기술 참조 | Microsoft Docs"
description: "조건부 액세스 제어를 통해 Azure Active Directory는 사용자를 인증할 때 및 응용 프로그램에 대한 액세스를 허용하기 전에 선택한 특정 조건을 확인합니다. 이러한 조건이 충족되면 사용자가 인증되고 응용 프로그램에 대한 액세스가 허용됩니다."
services: active-directory.
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 56a5bade-7dcc-4dcf-8092-a7d4bf5df3c1
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2017
ms.author: markvi
ms.reviewer: calebb
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: f96189735512090f993f61c0d64a249f650ea2a2
ms.contentlocale: ko-kr
ms.lasthandoff: 08/29/2017

---
# <a name="azure-active-directory-conditional-access-technical-reference"></a>Azure Active Directory 조건부 액세스 기술 참조

[Azure AD(Azure Active Directory) 조건부 액세스](active-directory-conditional-access-azure-portal.md)를 사용하여 권한 있는 사용자가 리소스를 액세스하는 방법을 미세 조정할 수 있습니다.  
이 항목에서는 조건부 액세스 정책의 다음 항목에 대한 지원 정보를 제공합니다. 

- 클라우드 앱 할당

- 클라이언트 앱 조건



## <a name="cloud-apps-assignments"></a>클라우드 앱 할당

조건부 액세스 정책을 구성할 경우 [정책을 적용할 클라우드 앱을 선택](active-directory-conditional-access-azure-portal.md#who)해야 합니다. 

![제어](./media/active-directory-conditional-access-technical-reference/09.png)


### <a name="microsoft-cloud-apps"></a>Microsoft 클라우드 앱

Microsoft의 다음 클라우드 앱에 조건부 액세스 정책을 할당할 수 있습니다.

- Azure 원격 앱

- Dynamics CRM

- Microsoft Office 365 Yammer

- Microsoft Office 365 Exchange Online

- Microsoft Office 365 SharePoint Online(비즈니스용 OneDrive 포함)

- Microsoft Power BI 

- Visual Studio Team Services

- Microsoft 팀


### <a name="other-apps"></a>다른 앱 

Microsoft 클라우드 앱 외에도 다음과 같은 형식의 클라우드 앱에 조건부 액세스 정책을 할당할 수 있습니다.

- Azure AD(Azure Active Directory) - 연결 응용 프로그램

- 사전 통합되고 페더레이션된 SaaS(software as a service) 응용 프로그램

- 암호 SSO(Single Sign-On)를 사용하는 응용 프로그램

- 기간 업무 응용 프로그램

- Azure AD 응용 프로그램 프록시를 사용하는 응용 프로그램 


## <a name="client-apps-conditions"></a>클라이언트 앱 조건 

조건부 액세스 정책을 구성할 경우 [클라이언트 앱 조건](active-directory-conditional-access-azure-portal.md#client-apps)을 설정할 수 있습니다. 클라이언트 앱 조건을 사용하면 이러한 형식의 클라이언트 앱에서 액세스하려는 경우 권한을 부여하거나 액세스를 차단할 수 있습니다.

- 브라우저
- 모바일 앱 및 데스크톱 앱

![제어](./media/active-directory-conditional-access-technical-reference/03.png)


### <a name="supported-browsers"></a>지원되는 브라우저 

조건부 액세스 정책에서 *브라우저*를 선택하여 리소스에 대한 액세스 권한을 부여하는 경우 지원되는 브라우저를 사용하여 액세스를 시도할 때에만 액세스 권한이 부여됩니다. 지원되지 않는 브라우저를 사용하여 액세스하려는 경우 시도가 차단됩니다.

![지원되는 브라우저](./media/active-directory-conditional-access-technical-reference/05.png)

조건부 액세스 정책에서 다음 브라우저가 지원됩니다. 


| OS                     | 브라우저                 | 지원     |
| :--                    | :--                      | :-:         |
| Win 10                 | IE, Edge                 | ![확인][1] |
| Win 10                 | Chrome                   | 미리 보기     |
| Win 8 / 8.1            | IE, Chrome               | ![확인][1] |
| Win 7                  | IE, Chrome               | ![확인][1] |
| iOS                    | Safari                   | ![확인][1] |
| Android                | Chrome                   | ![확인][1] |
| Windows Phone          | IE, Edge                 | ![확인][1] |
| Windows Server 2016    | IE, Edge                 | ![확인][1] |
| Windows Server 2016    | Chrome                   | 서비스 예정 |
| Windows Server 2012 R2 | IE, Chrome               | ![확인][1] |
| Windows Server 2008 R2 | IE, Chrome               | ![확인][1] |
| Mac OS                 | Safari                   | ![확인][1] |
| Mac OS                 | Chrome                   | 서비스 예정 |

> [!NOTE]
> Chrome 지원의 경우 Windows 10 크리에이터 업데이트를 사용하고 [여기](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji)에 있는 확장을 설치해야 합니다.


### <a name="supported-mobile-apps-and-desktop-clients"></a>지원되는 모바일 앱 및 데스크톱 클라이언트

조건부 액세스 정책에서 **모바일 앱 및 데스크톱 클라이언트**를 선택하여 리소스에 대한 액세스 권한을 부여하는 경우 지원되는 모바일 앱 및 데스크톱 클라이언트를 사용하여 액세스를 시도할 때에만 액세스 권한이 부여됩니다. 지원되지 않는 모바일 앱 및 데스크톱 클라이언트를 사용하여 액세스하려는 경우 시도가 차단됩니다.

![제어](./media/active-directory-conditional-access-technical-reference/06.png)

Office 365 및 기타 Azure AD 연결 서비스 응용 프로그램에 대한 조건부 액세스를 지원하는 모바일 앱 및 데스크톱 클라이언트는 다음과 같습니다.


| 클라이언트 앱| 대상 서비스| 플랫폼 |
| :-- | --- | --- |
| 앱에 대한 MFA 및 위치 정책입니다. 장치 기반 정책은 지원되지 않습니다.| 모든 My Apps 앱 서비스| Android 및 iOS|
| Azure 원격 앱| Azure 원격 앱 서비스| Windows 10, Windows 8.1, Windows 7, iOS, Android 및 Mac OS X|
| Dynamics CRM 앱| Dynamics CRM| Windows 10, Windows 8.1, Windows 7, iOS 및 Android|
| Microsoft Teams Services - Microsoft Teams 및 모든 클라이언트 앱(Windows 데스크톱, MAC OS X, iOS, Android, WP, 웹 클라이언트)을 지원하는 서비스를 모두 제어합니다.| Microsoft 팀| Windows 10, Windows 8.1, Windows 7, iOS/Android 및 MAC OSX|
| 메일/달력/인물 정보 앱, Outlook 2016, Outlook 2013(최신 인증 사용), 비즈니스용 Skype(최신 인증 사용)| Office 365 Exchange Online| 윈도우 10|
| Outlook 2016, Outlook 2013(최신 인증 사용), 비즈니스용 Skype(최신 인증 사용)| Office 365 Exchange Online| Windows 8.1, Windows 7|
| Outlook 모바일 앱| Office 365 Exchange Online| iOS|
| Outlook 2016(macOS용 Office)| Office 365 Exchange Online| Mac OS X|
| Office 2016 앱, Universal Office 앱, Office 2013(최신 인증 사용), OneDrive 동기화 클라이언트([참고](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e) 참조), Office 그룹 지원은 나중에 지원될 예정입니다. SharePoint 앱 지원은 나중에 지원될 예정입니다.| Office 365 SharePoint Online| 윈도우 10|
| Office 2016 앱, Office 2013(최신 인증 사용), OneDrive 동기화 클라이언트([참고](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e) 참조)| Office 365 SharePoint Online| Windows 8.1, Windows 7|
| Office 모바일 앱| Office 365 SharePoint Online| iOS, Android|
| macOS용 Office 2016(Word, Excel, PowerPoint, OneNote만 해당) 향후 제공될 예정인 비즈니스용 OneDrive 지원| Office 365 SharePoint Online| Mac OS X|
| Office Yammer 앱| Office 365 Yammer| Windows 10, iOS, Android|
| PowerBI 앱. Android용 Power BI 앱은 장치 기반 조건부 액세스를 현재 지원하지 않습니다.| PowerBI 서비스| Windows 10, Windows 8.1, Windows 7 및 iOS|
| Visual Studio Team Services 앱| Visual Studio Team Services| Windows 10, Windows 8.1, Windows 7, iOS 및 Android|














## <a name="next-steps"></a>다음 단계

- 조건부 액세스에 대한 개요는 [Azure Active Directory의 조건부 액세스](active-directory-conditional-access-azure-portal.md)를 참조하세요.
- 사용자 환경에서 조건부 액세스 정책을 구성할 준비가 완료된 경우 [Azure Active Directory의 조건부 액세스 모범 사례](active-directory-conditional-access-best-practices.md)를 참조하세요.



<!--Image references-->
[1]: ./media/active-directory-conditional-access-technical-reference/01.png



