
<properties
    pageTitle="조건부 액세스 - 지원되는 응용 프로그램 | Microsoft Azure"
    description="조건부 액세스 제어를 통해 Azure Active Directory는 사용자를 인증할 때 및 응용 프로그램에 대한 액세스를 허용하기 전에 선택한 특정 조건을 확인합니다. 이러한 조건이 충족되면 사용자가 인증되고 응용 프로그램에 대한 액세스가 허용됩니다."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
    ms.date="09/26/2016"
    ms.author="markvi"/>



# <a name="conditional-access-support-for-applications"></a>응용 프로그램에 대한 조건부 액세스 지원

조건부 액세스 규칙은 암호 Single Sign-On, LOB(기간 업무) 응용 프로그램 및 Azure AD 응용 프로그램 프록시를 사용하는 응용 프로그램인, Azure Active Directory에 연결된 응용 프로그램, 사전 통합된 페더레이션 SaaS 응용 프로그램에서 지원됩니다. 조건부 액세스를 사용하도록 설정할 수 있는 응용 프로그램의 자세한 목록은 [조건부 액세스로 설정된 서비스](active-directory-conditional-access-technical-reference.md#Services-enabled-with-conditional-access)를 참조합니다. 조건부 액세스는 최신 인증을 사용하는 모바일 및 데스크톱 응용 프로그램에서 작동합니다. 이 항목에서는 이러한 앱의 모바일 및 데스크톱 버전과 관련하여 무엇이 지원되는지 설명합니다.

최신 인증 프로그램을 사용하는 응용 프로그램은 Azure AD 로그인 페이지를 표시할 수 있습니다. 이렇게 하면 사용자에게 다단계 인증 인라인 메시지가 나타나거나, 액세스가 차단되었을 때 최종 사용자에 대한 메시지를 표시할 수 있습니다. 최신 인증은 장치 기반 조건부 액세스 정책을 평가하기 위해 Azure AD를 사용하여 장치를 인증하는 데 필요합니다.

어떤 응용 프로그램이 지원되고, 다른 진입점의 보안을 유지하기 위해 어떤 조치가 필요한지 이해하는 데 중요합니다.

## <a name="applications-using-modern-authentication"></a>최신 인증을 사용하는 응용 프로그램
다음 응용 프로그램은 Office 365 및 기타 Azure AD 연결된 서비스 응용 프로그램에 액세스하는 경우 조건부 액세스를 지원합니다.

| 대상 서비스  | 플랫폼  | 응용 프로그램                                                  |
|--------------|-----------------|----------------------------------------------------------------|
|Office 365 Exchange Online | Windows 10|메일/달력/인물 정보 앱, Outlook 2016, Outlook 2013(최신 인증 사용), 비즈니스용 Skype(최신 인증 포함)|
|Office 365 Exchange Online| Windows 7, Windows 8.1, |Outlook 2016, Outlook 2013(최신 인증 사용), 비즈니스용 Skype(최신 인증 사용)|
|Office 365 Exchange Online|iOS, Android|  Outlook 모바일 앱|
|Office 365 Exchange Online|Mac OSX| MFA/위치 전용 Outlook 2016; 장치 기반 정책 지원 서비스 예정, 비즈니스용 Skype 지원 서비스 예정|
|Office 365 SharePoint Online|Windows 10| Office 2016 앱, Office Universal 앱, Office 2013(최신 인증 사용), 비즈니스용 OneDrive 앱(NGSC 또는 차세대 동기화 클라이언트) 지원 서비스 예정, Office Groups 지원 서비스 예정, SharePoint 앱 지원 서비스 예정|
|Office 365 SharePoint Online|Windows 7, Windows 8.1,|Office 2016 앱, Office 2013(최신 인증 사용), 비즈니스용 OneDrive 앱(Groove 동기화 클라이언트)|
|Office 365 SharePoint Online|iOS, Android|  Office 모바일 앱 |
|Office 365 SharePoint Online|Mac OSX| MFA/위치 전용 Office 2016 앱; 장치 기반 정책 지원 서비스 예정|
|Office 365 Yammer|Windows 10, iOS 및 Android | Office Yammer 앱|
|Dynamics CRM|Windows 10, 7, 8.1, iOS 및 Android | Dynamics CRM 앱|
|PowerBI 서비스|Windows 10, 7, 8.1, iOS 및 Android | PowerBI 앱|
|Azure 원격 앱 서비스|Windows 10, 7, 8.1, iOS 및 Android, Mac OSX |Azure 원격 앱|
|모든 My Apps 앱 서비스|Android 및 iOS|모든 My Apps 앱 서비스 |


## <a name="applications-that-do-not-use-modern-authentication"></a>최신 인증을 사용하지 않는 응용 프로그램

현재 최신 인증을 사용하지 않는 앱은 조건부 액세스가 강제로 적용되지 않으므로 다른 방법을 사용하여 액세스를 차단해야 합니다. 이전 앱 버전이 오래된 프로토콜을 사용하여 빌드되었기 때문에, 이는 주로 Exchange와 SharePoint 액세스에서 고려되는 사항입니다.

## <a name="office-365-sharepoint-online"></a>Office 365 SharePoint Online

레거시 프로토콜은 Set-SPOTenant cmdlet을 사용하여 SharePoint에서 사용을 중지할 수 있습니다. 이 cmdlet을 사용하면 Office 클라이언트에서 오래된 인증 프로토콜이 SharePoint Online 리소스에 액세스하지 못하게 차단합니다. 

**예제 명령**:     `Set-SPOTenant -LegacyAuthProtocolsEnabled $false`
 
## <a name="office-365-exchange-online"></a>Office 365 Exchange Online

Exchange에는 두 가지 프로토콜 검토 카테고리가 있으므로 조직에 알맞은 정책을 선택해야 합니다.

1. Exchange ActiveSync. 기본적으로 MFA와 위치에 대한 조건부 액세스 정책은 Exchange ActiveSync에 강제로 적용되지 않습니다. 따라서 Exchange ActiveSync 정책을 직접 구성하거나 AD FS 규칙으로 Exchange ActiveSync를 차단하여 액세스를 보호할 수 있습니다.
2. 레거시 프로토콜입니다. AD FS에서 레거시 프로토콜을 차단할 수 있습니다. 최신 인증을 사용하지 않는 Office 2013 및 그 이전 제품과 같이 오래된 Office 클라이언트의 액세스를 차단합니다.


### <a name="example-ad-fs-rules"></a>AD FS 규칙 예제
다음 규칙은 두 가지 일반적인 구성으로 AD FS에서 레거시 프로토콜 액세스를 차단할 수 있습니다.

### <a name="option-1:-allow-exchange-activesync-and-only-allow-legacy-apps-on-the-intranet"></a>옵션 1: Exchange ActiveSync를 허용하고 인트라넷에 있는 레거시 앱만 허용합니다.

다음 세 가지 규칙을 Microsoft Office 365 ID 플랫폼의 AD FS 신뢰 당사자 트러스트에 적용하면 Exchange ActiveSync 트래픽과 더불어 브러우저 및 최신 인증 트래픽이 허용됩니다. 익스트라넷에서 레거시 앱이 차단됩니다. 

규칙 1

    `@RuleName = “Allow all intranet traffic”
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"] 
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");`

규칙 2

    @RuleName = “Allow EAS”
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"] 
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

규칙 3

    @RuleName = “Allow Extranet browser or browser dialog traffic”
    c1:[Type == " http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] && 
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"] 
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

### <a name="option-2:-allow-exchange-activesync-and-block-legacy-apps"></a>옵션 2: Exchange ActiveSync를 허용하고 레거시 앱을 차단합니다. 
다음 세 가지 규칙을 Microsoft Office 365 ID 플랫폼의 AD FS 신뢰 당사자 트러스트에 적용하면 Exchange ActiveSync 트래픽과 더불어 브러우저 및 최신 인증 트래픽이 허용됩니다. 모든 위치에서 레거시 앱이 차단됩니다. 

규칙 1

    @RuleName = “Allow all intranet traffic only for browser and modern authentication clients”
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"] && 
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"] 
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


규칙 2 

    @RuleName = “Allow EAS”
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"] 
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


규칙 3 

    @RuleName = “Allow Extranet browser or browser dialog traffic”
    c1:[Type == " http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] && 
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"] 
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");











<!--HONumber=Oct16_HO2-->


