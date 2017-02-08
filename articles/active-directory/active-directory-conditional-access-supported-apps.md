---
title: "Azure Active Directory에서 조건부 액세스 규칙을 사용하는 응용 프로그램 | Microsoft Docs"
description: "Azure Active Directory에서는 사용자를 인증할 때 조건부 액세스 제어를 통해 특정 조건을 확인하여 응용 프로그램에 대한 액세스를 허용합니다."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 62349fba-3cc0-4ab5-babe-372b3389eff6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/26/2016
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: fbc03ebf0c66950ae92ca75cf34da86dfb15a349


---
# <a name="applications-that-use-conditional-access-rules-in-azure-active-directory"></a>Azure Active Directory에서 조건부 액세스 규칙을 사용하는 응용 프로그램
조건부 액세스 규칙은 Azure AD(Azure Active Directory) 연결 응용 프로그램, 페더레이션된 사전 통합 SaaS(Software as a Service) 응용 프로그램, 암호 SSO(Single Sign-On)를 사용하는 프로그램, LOB(기간 업무) 응용 프로그램 및 Azure AD 응용 프로그램 프록시를 사용하는 응용 프로그램에서 지원합니다. 조건부 액세스를 사용할 수 있는 응용 프로그램의 자세한 목록은 [조건부 액세스로 설정된 서비스](active-directory-conditional-access-technical-reference.md)를 참조하세요. 조건부 액세스는 최신 인증을 사용하는 모바일과 데스크톱 응용 프로그램 모두에서 작동합니다. 이 문서에서는 모바일 및 데스크톱 앱에서 조건부 액세스가 작동되는 방법에 대해 설명합니다.

최신 인증을 사용하는 응용 프로그램에서 Azure AD 로그인 페이지를 사용할 수 있습니다. 로그인 페이지에서는 사용자에게 다단계 인증을 요구하는 메시지가 표시됩니다. 사용자의 액세스가 차단되는 경우 메시지가 표시됩니다. 최신 인증은 Azure AD에서 장치를 인증하여 장치 기반 조건부 액세스 정책을 평가하는 데 필요합니다.

조건부 액세스 규칙을 사용할 수 있는 응용 프로그램과 다른 응용 프로그램 진입점을 보호하기 위해 수행해야 하는 조치를 이해하는 것이 중요합니다.

## <a name="applications-that-use-modern-authentication"></a>최신 인증을 사용하는 응용 프로그램
> [!NOTE]
> Azure AD에 Office 365와 대등한 조건부 액세스 정책이 있는 경우 두 조건부 액세스 정책을 모두 구성합니다. 예를 들어 Exchange Online 또는 SharePoint Online에 대한 조건부 액세스 정책이 여기에 해당됩니다.
>
>

Office 365 및 기타 Azure AD 연결 서비스 응용 프로그램에 대한 조건부 액세스를 지원하는 응용 프로그램은 다음과 같습니다.

| 대상 서비스 | 플랫폼 | 응용 프로그램 |
| --- | --- | --- |
| Office 365 Exchange Online |Windows 10 |메일/달력/인물 정보 앱, Outlook 2016, Outlook 2013(최신 인증 사용), 비즈니스용 Skype(최신 인증 사용) |
| Office 365 Exchange Online |Windows 8.1, Windows 7 |Outlook 2016, Outlook 2013(최신 인증 사용), 비즈니스용 Skype(최신 인증 사용) |
| Office 365 Exchange Online |iOS, Android |Outlook 모바일 앱 |
| Office 365 Exchange Online |Mac OS X |다단계 인증 및 위치 전용 Outlook 2016, 장치 기반 정책(향후 지원 예정), 비즈니스용 Skype(향후 지원 예정) |
| Office 365 SharePoint Online |Windows 10 |Office 2016 앱, Office Universal 앱, Office 2013(최신 인증 사용), 비즈니스용 OneDrive 앱(NGSC(차세대 동기화 클라이언트) 향후 지원 예정), Office Groups(향후 지원 예정), SharePoint 앱(향후 지원 예정) |
| Office 365 SharePoint Online |Windows 8.1, Windows 7 |Office 2016 앱, Office 2013(최신 인증 사용), 비즈니스용 OneDrive 앱(Groove 동기화 클라이언트) |
| Office 365 SharePoint Online |iOS, Android |Office 모바일 앱 |
| Office 365 SharePoint Online |Mac OS X |다단계 인증 및 위치 전용 Outlook 2016, 장치 기반 정책(향후 지원 예정) |
| Office 365 Yammer |Windows 10, iOS 및 Android |Office Yammer 앱 |
| Dynamics CRM |Windows 10, Windows 8.1, Windows 7, iOS 및 Android |Dynamics CRM 앱 |
| PowerBI 서비스 |Windows 10, Windows 8.1, Windows 7, iOS 및 Android |PowerBI 앱 |
| Azure 원격 앱 서비스 |Windows 10, Windows 8.1, Windows 7, iOS, Android 및 Mac OS X |Azure 원격 앱 |
| 모든 My Apps 앱 서비스 |Android 및 iOS |모든 My Apps 앱 서비스 |

## <a name="applications-that-do-not-use-modern-authentication"></a>최신 인증을 사용하지 않는 응용 프로그램
현재 최신 인증을 사용하지 않는 앱에 대한 액세스는 다른 방법을 사용하여 차단해야 합니다. 최신 인증을 사용하지 않는 앱에 대한 액세스 규칙이 조건부 액세스를 통해 강제로 적용되지 않기 때문입니다. 이는 기본적으로 Exchange 및 SharePoint에 대한 액세스를 위한 고려 사항입니다. 이전 버전의 앱은 대부분 이전 액세스 제어 프로토콜을 사용합니다.

### <a name="control-access-in-office-365-sharepoint-online"></a>Office 365 SharePoint Online 액세스 제어
Set-SPOTenant cmdlet을 사용하여 SharePoint 액세스에 대한 레거시 프로토콜 사용을 중지할 수 있습니다. 이 cmdlet을 사용하면 Office 클라이언트에서 오래된 인증 프로토콜로 SharePoint Online 리소스에 액세스할 수 없도록 차단합니다.

**예제 명령**:     `Set-SPOTenant -LegacyAuthProtocolsEnabled $false`

### <a name="control-access-in-office-365-exchange-online"></a>Office 365 Exchange Online 액세스 제어
Exchange에서는 중요한 두 가지 범주의 프로토콜을 제공합니다. 다음 옵션을 검토한 후에 조직에 적합한 정책을 선택합니다.

* **Exchange ActiveSync** - 기본적으로 다단계 인증 및 위치에 대한 조건부 액세스 정책은 Exchange ActiveSync에 강제로 적용되지 않습니다. 따라서 Exchange ActiveSync 정책을 직접 구성하거나 AD FS(Active Directory Federation Services) 규칙을 통해 Exchange ActiveSync를 차단함으로써 이러한 서비스에 대한 액세스를 보호해야 합니다.
* **레거시 프로토콜** - AD FS를 사용하면 레거시 프로토콜을 차단할 수 있습니다. 이렇게 하면 최신 인증을 사용하지 않는 Office 2013 및 이전 버전의 Office와 같이 오래된 Office 클라이언트에 대한 액세스를 차단합니다.

### <a name="use-ad-fs-to-block-legacy-protocol"></a>AD FS를 통한 레거시 프로토콜 차단
다음 예제를 사용하면 AD FS 수준에서 레거시 프로토콜 액세스를 차단할 수 있습니다. 일반적인 두 가지 구성에서 선택합니다.

#### <a name="option-1-allow-exchange-activesync-and-allow-legacy-apps-but-only-on-the-intranet"></a>옵션 1: Exchange ActiveSync 허용 및 인트라넷에 있는 레거시 앱만 허용
다음 세 가지 규칙을 Microsoft Office 365 ID 플랫폼의 AD FS 신뢰 당사자 트러스트에 적용하면 Exchange ActiveSync 트래픽과 브라우저 및 최신 인증 트래픽에 액세스할 수 있습니다. 레거시 앱은 익스트라넷에서 차단됩니다.

##### <a name="rule-1"></a>규칙 1
    @RuleName = "Allow all intranet traffic"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

##### <a name="rule-2"></a>규칙 2
    @RuleName = "Allow Exchange ActiveSync"
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

##### <a name="rule-3"></a>규칙 3
    @RuleName = "Allow extranet browser and browser dialog traffic"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

#### <a name="option-2-allow-exchange-activesync-and-block-legacy-apps"></a>옵션 2: Exchange ActiveSync 허용 및 레거시 앱 차단
다음 세 가지 규칙을 Microsoft Office 365 ID 플랫폼의 AD FS 신뢰 당사자 트러스트에 적용하면 Exchange ActiveSync 트래픽과 브라우저 및 최신 인증 트래픽에 액세스할 수 있습니다. 레거시 앱은 모든 위치에서 차단됩니다.

##### <a name="rule-1"></a>규칙 1
    @RuleName = "Allow all intranet traffic only for browser and modern authentication clients"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


##### <a name="rule-2"></a>규칙 2
    @RuleName = "Allow Exchange ActiveSync"
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


##### <a name="rule-3"></a>규칙 3
    @RuleName = "Allow extranet browser and browser dialog traffic"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");



<!--HONumber=Dec16_HO5-->


